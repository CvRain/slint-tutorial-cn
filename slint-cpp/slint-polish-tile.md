---
title: Slint C++  开幕动画
date: 2023-12-10 10:20:26
tags:
  - Cpp
  - Slint
---
# Slint C++ 图片开幕动画

按照小游戏的设定，一开始图片是被挡住的，当鼠标点击了所在图片之后，图片就被掀开。现在我们需要做一个类似开幕的效果，他的实现方式也不是很困难：给这个矩形上面附加两个1/2大小的矩形，再叠加一个透明矩形用来接受鼠标事件，当点击之后左边的矩形往左走，右边的矩形往右走，为他们两个顺便加上过度动画效果。
<!-- more -->

## 动画设计
- 左幕布
	- 高度设置为磁贴块的大小
	- 宽度设置为磁贴块的一半大小
	- 位置设置在磁贴块的左边
	- 当点击之后宽度逐渐到0,到0后设置为不可见
- 右幕布
	- 高度设置为磁贴块的大小
	- 宽度设置为磁贴块的一半大小
	- 位置设置在磁贴块的右边
	- 当点击之后宽度逐渐到0,到0后设置为不可见

## 添加一块幕布
```Slint
component MemoryTile inherits Rectangle{
    width: 64px;
    height: 64px;
    background: gray;

    Image {
        width: parent.width;
        height: parent.height;
        source: @image-url("icons/bus.png");
    }

    // 左幕布
    Rectangle {
        width: parent.width / 2;
        height: parent.height;
        x: 0px;
        background: darkgray;
    }
}
export component MainWindow inherits Window {
    MemoryTile {}
}
```
添加一块的效果
![添加一块的效果](/img/slint-polish-tile/img01.png)

## 添加两块幕布
```Slint
component MemoryTile inherits Rectangle{
    width: 64px;
    height: 64px;
    background: gray;

    Image {
        width: parent.width;
        height: parent.height;
        source: @image-url("icons/bus.png");
    }

    // 左幕布
    Rectangle {
        width: parent.width / 2;
        height: parent.height;
        x: 0px;
        background: darkgray;
    }

    // 右幕布
    Rectangle {
        width: parent.width / 2;
        height: parent.height;
        x: parent.width / 2;
        background: darkgray;
    }
}
export component MainWindow inherits Window {
    MemoryTile {}
}
```

## 最终代码
定义了一个名为`MemoryTile`的组件，以及一个名为`MainWindow`的窗口组件。`MemoryTile`组件继承自`Rectangle`，具有以下属性和回调函数：

1. `callback clicked`：定义了一个名为`clicked`的回调函数。
2. `in property <bool> open_curtain`：定义了一个名为`open_curtain`的属性，类型为布尔值，表示幕布的打开状态。
3. `in property <bool> solved`：定义了一个名为`solved`的属性，类型为布尔值，表示组件的状态。
4. `in property <image> icon`：定义了一个名为`icon`的属性，类型为图像，表示组件的图标。

组件的宽度和高度分别为64像素，背景颜色根据`solved`属性的值来决定，如果是`solved`则为深灰色，否则为灰色。

接下来，代码定义了一个名为`animate`的动画属性，用于改变组件的背景颜色。动画的持续时间为800毫秒，使用`ease-in`ease-in` easing函数。

然后，代码定义了一个名为`Image`的子组件，其宽度、高度和源图像都来自父组件的`icon`属性。

接下来，代码定义了两个名为`Rectangle`的子组件，分别表示左幕布和右幕布。左幕布的宽度根据`open_curtain`属性的值来决定，如果是`open_curtain`则为0像素，否则为父组件宽度的一半。背景颜色为深灰色。当`open_curtain`属性改变时，宽度会通过动画进行更新。

右幕布的宽度、高度和背景颜色与左幕布相同。此外，当`open_curtain`属性改变时，还会通过动画更新位置和宽度。

最后，代码定义了一个名为`TouchArea`的子组件，当用户点击时，会调用父组件的`clicked`回调函数。

最后，代码导出了`MainWindow`组件，并在其中定义了一个`MemoryTile`组件，其`icon`属性为`icons/bus.png`，当点击时会改变`open_curtain`属性的值。

```Slint
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
    MemoryTile {
        icon: @image-url("icons/bus.png");
        clicked => {
            self.open-curtain = !self.open-curtain;
        }
    }
}
```

