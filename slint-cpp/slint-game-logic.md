---
title: Slint C++  实现最后的逻辑
date: 2023-12-21 14:26:09
tags:
  - Slint
  - Cpp
---
# 实现最后的逻辑
我们也将用 C++ 实现游戏规则。 Slint 的总体理念是，仅用 .slint 语言实现用户界面，并用您最喜欢的编程语言实现业务逻辑。游戏规则应强制规定最多有两块牌的窗帘打开。如果图块匹配，那么我们认为它们已解决并且它们保持打开状态。否则，我们会等待一段时间，以便玩家可以记住图标的位置，然后再次关闭它们。
<!--more-->
我们将修改 memory.slint 文件中的 .slint 标记，以便在用户单击图块时向 C++ 代码发出信号。需要对 MainWindow 进行两处更改： 我们需要为 MainWindow 添加一种调用 C++ 代码的方法，以便检查一对图块是否已被解决。我们需要添加一个属性，C++ 代码可以切换该属性以禁用进一步的图块交互，以防止玩家打开超过允许的图块。不允许作弊！首先，我们将回调和属性声明粘贴到 MainWindow 中：
```slint
    export component MainWindow inherits Window {
        width: 326px;
        height: 326px;

        callback check_if_pair_solved(); // 在这里添加一个回调函数
        in property <bool> disable_tiles; // 对外暴露一个禁用磁贴的变量

        in-out property <[TileData]> memory_tiles: [
           { image: @image-url("icons/at.png") },
```

之后修改组件MainWindow中的内容，修改其中的点击事件，在原来是让图片从可视化到不可视化的切换，现在则首先判断是否是禁用状态的，再修改图片可视化状态。
```Slint
        for tile[i] in memory_tiles : MemoryTile {
            x: mod(i, 4) * 74px;
            y: floor(i / 4) * 74px;
            width: 64px;
            height: 64px;
            icon: tile.image;
            open_curtain: tile.image_visible || tile.solved;
            // propagate the solved status from the model to the tile
            solved: tile.solved;
            clicked => {
                // old: tile.image_visible = !tile.image_visible;
                // new:
                if (!root.disable_tiles) {
                    tile.image_visible = !tile.image_visible;
                    root.check_if_pair_solved();
                }
            }
        }

```

在 C++ 方面，我们现在可以向 check_if_pair_solved 回调添加一个处理程序，该处理程序将检查是否打开了两个图块。如果它们匹配，则已求解的属性在模型中设置为 true。如果它们不匹配，则启动一个计时器，该计时器将在一秒钟后关闭它们。当计时器运行时，我们禁用每个图块，以便在此期间无法单击任何内容。
**官方代码**
```C++

    auto tiles_model = std::make_shared<slint::VectorModel<TileData>>(new_tiles);
    main_window->set_memory_tiles(tiles_model);

    main_window->on_check_if_pair_solved(
            [main_window_weak = slint::ComponentWeakHandle(main_window)] {
                auto main_window = *main_window_weak.lock();
                auto tiles_model = main_window->get_memory_tiles();
                int first_visible_index = -1;
                TileData first_visible_tile;
                for (int i = 0; i < tiles_model->row_count(); ++i) {
                    auto tile = *tiles_model->row_data(i);
                    if (!tile.image_visible || tile.solved)
                        continue;
                    if (first_visible_index == -1) {
                        first_visible_index = i;
                        first_visible_tile = tile;
                        continue;
                    }
                    bool is_pair_solved = tile == first_visible_tile;
                    if (is_pair_solved) {
                        first_visible_tile.solved = true;
                        tiles_model->set_row_data(first_visible_index,
                                                  first_visible_tile);
                        tile.solved = true;
                        tiles_model->set_row_data(i, tile);
                        return;
                    }
                    main_window->set_disable_tiles(true);

                    slint::Timer::single_shot(std::chrono::seconds(1),
                        [=]() mutable {
                            main_window->set_disable_tiles(false);
                            first_visible_tile.image_visible = false;
                            tiles_model->set_row_data(first_visible_index,
                                                      first_visible_tile);
                            tile.image_visible = false;
                            tiles_model->set_row_data(i, tile);
                        });
                }
            });

    main_window->run();
}

```

**代码解析**
```C++
// 调用main_window的on_check_if_pair_solved函数，检查是否是成对
main_window->on_check_if_pair_solved(
            [main_window_weak = slint::ComponentWeakHandle(main_window)] {
                auto main_window = main_window_weak.lock().value();
                auto tiles_model = main_window->get_memory_tiles();
                int first_visible_index = -1;
                TileData first_visible_tile;
                // 遍历每一行
                for (int i = 0; i < tiles_model->row_count(); ++i) {
                    auto tile = tiles_model.get()->row_data(i).value();
                    // 如果tile不可见或者已经解决，则跳过
                    if (!tile.image_visible || tile.solved)
                        continue;
                    // 如果当前行是第一个可见行，则记录下当前tile
                    if (first_visible_index == -1) {
                        first_visible_index = i;
                        first_visible_tile = tile;
                        continue;
                    }
                    // 如果当前tile和第一个可见tile相同，则将两个tile都设置为已解决
                    bool is_pair_solved = tile == first_visible_tile;
                    if (is_pair_solved) {
                        first_visible_tile.solved = true;
                        tiles_model->set_row_data(first_visible_index,
                                                  first_visible_tile);
                        tile.solved = true;
                        tiles_model->set_row_data(i, tile);
                        return;
                    }
                    // 如果不是成对，则禁用所有tile，1秒后恢复
                    main_window->set_disable_tiles(true);

                    slint::Timer::single_shot(std::chrono::seconds(1),
                        [=]() mutable {
                            main_window->set_disable_tiles(false);
                            first_visible_tile.image_visible = false;
                            tiles_model->set_row_data(first_visible_index,
                                                      first_visible_tile);
                            tile.image_visible = false;
                            tiles_model->set_row_data(i, tile);
                        });
                }
            });
```

**至此，整个Slint C++ tutorial 就完结了（撒花）**
基本摸清了创建一个简单Slint应用的简单流程，之后便是研究研究其他控件还有通信的东西了。