---
title: Slint C++  生成随机排列的磁贴
date: 2023-12-21 00:17:24
tags:
---
# Slint C++ 生成随机排列的磁贴
我们要做的就是获取以 .slint 语言声明的图块列表，复制它，然后对其进行打乱。我们将通过 C++ 代码访问 memory_tiles 属性来完成此操作。对于每个顶级属性，都会生成一个 getter 和 setter 函数 - 在我们的例子中为 get_memory_tiles 和 set_memory_tiles。由于 memory_tiles 是 .slint 语言中的数组，因此它表示为 std::shared_ptr<slint::Model>。我们无法修改 .slint 生成的模型，但我们可以从中提取图块，并将其放入继承自 Model 的 slint::VectorModel 中。 VectorModel允许我们进行修改，我们可以用它来替换静态生成的模型。

**官方的示例代码**
```C++
// ...

#include <random> // Added

int main()
{
    auto main_window = MainWindow::create();
    auto old_tiles = main_window->get_memory_tiles();
    std::vector<TileData> new_tiles;
    new_tiles.reserve(old_tiles->row_count() * 2);
    for (int i = 0; i < old_tiles->row_count(); ++i) {
        new_tiles.push_back(*old_tiles->row_data(i));
        new_tiles.push_back(*old_tiles->row_data(i));
    }
    std::default_random_engine rng {};
    std::shuffle(new_tiles.begin(), new_tiles.end(), rng);
    auto tiles_model = std::make_shared<slint::VectorModel<TileData>>(new_tiles);
    main_window->set_memory_tiles(tiles_model);

    main_window->run();
}

```

**修改后并添加注释的代码**
```C++
#include "memory.h"
#include <random>
#include <vector>

int main() {
    // Create the main window
    auto main_window = MainWindow::create();

    // Get the current tiles and store them
    const auto old_tiles = main_window->get_memory_tiles();
    // Create a new vector to store the new tiles
    std::vector<TileData> new_tiles{};
    // Reserve space for the new tiles
    new_tiles.reserve(old_tiles->row_count() * 2);

    // Copy the old tiles into the new vector
    for (int i = 0; i < old_tiles->row_count(); i++) {
        new_tiles.emplace_back(*old_tiles->row_data(i));
        new_tiles.emplace_back(*old_tiles->row_data(i));
    }
    // Create a random number generator
    std::default_random_engine rng(std::random_device{}());
    // Shuffle the new tiles
    std::shuffle(new_tiles.begin(), new_tiles.end(), rng);

    // Create a new model with the shuffled tiles
    auto &&tiles_model = std::make_shared<slint::VectorModel<TileData>>(new_tiles);
    // Set the new tiles as the memory tiles
    main_window->set_memory_tiles(tiles_model);

    // Run the main window
    main_window->run();
}
```