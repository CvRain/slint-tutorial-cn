---
title: Slint C++  创建更多的磁贴
date: 2023-12-20 17:07:11
tags:
  - Slint
  - Cpp
---
# Slint C++ 创建更多的磁贴
前一个章节我们完成了一个磁贴的创建，接下来我们需要创建一个网格，在里面创建更多的磁贴来完成游戏的基本布局。当然，我们需要分两步走：

<!--more-->

1. 数据模型：这应该是一个数组，其中每个元素描述图块数据结构，例如图像的 url、图像是否可见以及该图块是否已解决。我们从 C++ 代码修改模型。  一种使用上面的 .slint 标记代码创建多个图块实例的方法。  
  
2. 在 Slint 中，我们可以使用括号声明结构数组，以创建模型。我们可以使用 for 循环创建同一元素的多个实例。在 .slint 中，for 循环是声明性的，并且在模型更改时自动更新。我们实例化所有不同的 MemoryTile 元素，并根据它们的索引将它们放置在网格上，图块之间有一点间距。

## 创建一个磁贴模型
现在我们需要控制多个磁贴了，如果还是像之前那样操作单个磁贴就非常麻烦，他有不同的属性：图片地址，是否可见，是否被点击。在C++中可以使用结构体或者类来完成我们的需求，在`Slint`中也可以实现，让我们在`.slint`文件中最上面添加他们：
```Slint
struct TileData{
	image: image,
	image_visable: bool,
	solved: bool,
}
```
创建了一个叫做`TileData`的结构体，结构体冒号左边的是名字，右边是类型：
- image:  图片类型，用来存放图片
- image_visable:  图片是否可见
- sovled：是否被消除掉，（我不是很喜欢这个名字）

## 修改MainWindow组件
接下来，我们使用以下代码片段替换 memory.slint 文件底部的导出组件
```Slint
export component MainWindow inherits Window {
    width: 326px;
    height: 326px;

    in property <[TileData]> memory_tiles: [
        { image: @image-url("icons/at.png") },
        { image: @image-url("icons/balance-scale.png") },
        { image: @image-url("icons/bicycle.png") },
        { image: @image-url("icons/bus.png") },
        { image: @image-url("icons/cloud.png") },
        { image: @image-url("icons/cogs.png") },
        { image: @image-url("icons/motorcycle.png") },
        { image: @image-url("icons/video.png") },
    ];
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
            tile.image_visible = !tile.image_visible;
        }
    }
}

```


1. `in property <[TileData]> memory_tiles: [ ... ]`：定义了一个名为`memory_tiles`的属性，其类型为`<[TileData]>`，表示它是一个数组，其中每个元素都是一个`TileData`类型的对象。`TileData`类型未知，可能是一个包含图像URL和其他属性的对象。

2. `for tile[i] in memory_tiles : MemoryTile { ... }`：这是一个使用QML的for循环语法遍历`memory_tiles`数组的过程。对于数组中的每个元素，都会创建一个名为`tile`的局部变量，并将其传递给名为`MemoryTile`的子组件。

3. `x: mod(i, 4) * 74px;` 和 `y: floor(i / 4) * 74px;`：这两个表达式用于计算`tile`组件的坐标。`mod(i, 4)`表示将`i`除以4的余数，`floor(i / 4)`表示将`i`除以4的整数部分向下取整。然后，这些值乘以74像素（这是每个小方块的宽度），以计算`tile`组件的x和y坐标。

4. `width: 64px;` 和 `height: 64px;`：定义了`tile`组件的宽度和高度，均为64像素。

5. `icon: tile.image;`：将`tile.image`（可能是图像URL或其他属性）分配给`icon`属性，该属性将显示在`tile`组件上。

6. `open_curtain: tile.image_visible || tile.solved;`：这个表达式定义了`open_curtain`属性的值。`tile.image_visible`是一个布尔值，表示图像是否可见。`tile.solved`也是一个布尔值，表示该方块是否已解决。`||`表示逻辑或操作，即如果`tile.image_visible`为真或`tile.solved`为真，则`open_curtain`为真。这意味着`tile`组件的遮罩层（如果有的话）将始终显示。

7. `solved: tile.solved;`：将`tile.solved`（布尔值，表示方块是否已解决）分配给`solved`属性。

8. `clicked => { ... }`：这是一个事件处理程序，当`tile`组件被单击时触发。事件处理程序中的代码将在被单击的`tile`组件上执行。

## 完整代码
你也可以将数组复制一次，这样可以得到4x4个完整的磁贴
```Slint
struct TileData {
    image: image,
    image_visible: bool,
    solved: bool,
}

component MemoryTile inherits Rectangle{
    callback clicked;  
    in property <bool> open_curtain; 
    in property <bool> solved;
    in property <image> icon;

    width: 64px;
    height: 64px;
    background: solved? darkgrey : grey;

    animate background {
         duration: 800ms;
    }

    Image {
        width: parent.width;
        height: parent.height;
        source: icon;
    }

    // 左幕布
    Rectangle {
        width: open_curtain ? 0px : (parent.width / 2);
        height: parent.height;
        x: 0px;
        background: darkgrey;
        animate width {
             duration: 250ms;
             easing: ease-in;
        }
    }

    // 右幕布
    Rectangle {
        width: open_curtain ? 0px : (parent.width / 2);
        height: parent.height;
        x: open_curtain ? parent.width : (parent.width / 2);
        background: darkgrey;
        animate width {
            duration: 250ms;
            easing: ease-in;
       }
       animate x {
            duration: 250ms;
            easing: ease-in;
       }
    }

    TouchArea {
        clicked => {
            root.clicked();
        }
    }
}
export component MainWindow inherits Window {
    width: 326px;
    height: 326px;

    in property <[TileData]> memory_tiles: [
        { image: @image-url("icons/at.png") },
        { image: @image-url("icons/balance-scale.png") },
        { image: @image-url("icons/bicycle.png") },
        { image: @image-url("icons/bus.png") },
        { image: @image-url("icons/cloud.png") },
        { image: @image-url("icons/cogs.png") },
        { image: @image-url("icons/motorcycle.png") },
        { image: @image-url("icons/video.png") },
        { image: @image-url("icons/at.png") },
        { image: @image-url("icons/balance-scale.png") },
        { image: @image-url("icons/bicycle.png") },
        { image: @image-url("icons/bus.png") },
        { image: @image-url("icons/cloud.png") },
        { image: @image-url("icons/cogs.png") },
        { image: @image-url("icons/motorcycle.png") },
        { image: @image-url("icons/video.png") },
    ];

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
            tile.image_visible = !tile.image_visible;
        }
    }
}
```