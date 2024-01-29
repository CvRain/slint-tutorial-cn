---
title: Slint C++  画一个矩形
date: 2023-12-10 09:34:55
tags:
  - Cpp
  - Slint
---
# Slint C++ 画一个矩形
官方在教程中给的一个案例是记忆磁贴，也就是一组图片里选择两个相同图案的消除。这个是官方给的[demo](https://slint.dev/blog/memory-game-tutorial/memory_clip.mp4)
现在我们已经有了一个窗口，接下来就是研究一下怎么画一个矩形然后在里面塞一个图片。

打开熟悉的memory.slint
```slint
component MemoryTile inherits Rectangle{
    width: 64px;
    height: 64px;
    background: gray;
}
export component MainWindow inherits Window {
    MemoryTile {}
}
```
我们在这里创建了一个叫做`MemoryTile`的组件，他继承了`Rectangle`，在其中我们设置了宽度、高度、背景颜色，和QML不同的是：.slint语言中的长度有一个单位，这里是px后缀。这使得代码更容易阅读，编译器可以检测到何时意外地将值与附加的不同单元混合在一起。
![运行效果](/img/slint-rectangle/img01.png)

请注意，我们导出MainWindow组件。这是必要的，以便我们以后可以从业务逻辑中访问它。

接下来就是往里面塞图片
```slint
component MemoryTile inherits Rectangle{
    width: 64px;
    height: 64px;
    background: gray;

    Image {
        width: parent.width;
        height: parent.height;
        source: @image-url("icons/bus.png");
    }
}
export component MainWindow inherits Window {
    MemoryTile {}
}
```


在矩形中，我们放置了一个Image元素，该元素使用@Image-url（）宏加载一个图标。该路径是相对于memory.slint所在的文件夹的。这个图标和我们稍后要使用的其他图标需要先安装。图片素材可以自己做一套也可以直接下载 [Zip archive](https://slint.dev/blog/memory-game-tutorial/icons.zip)
![贴图片的效果](/img/slint-rectangle/img02.png)