---
title: Slint 字体处理
date: 2024-01-09 09:54:20
tags:
  - Slint
---
# Slint 字体处理
一些元素诸如`Text`和`TextInput` 能够渲染文本以及允许通过不同的属性自定义文本的外观。以 font- 为前缀的属性，例如 font-family、font-size 和 font-weight 会影响用于渲染到屏幕的字体的选择。如果未指定任何这些属性，则应用周围 Window 元素中的 default-font- 值，例如 default-font-family。

选择用于渲染的字体会自动从系统中获取。还可以在您的设计中包含自定义字体。自定义字体必须是 TrueType 字体 (.ttf)、TrueType 字体集合 (.ttc) 或 OpenType 字体 (.otf)。您可以使用导入语句选择自定义字体：在 .slint 文件中导入“./my_custom_font.ttf”。这指示 Slint 编译器包含字体并使字体系列全局可与 font-family 属性一起使用。

简单来说，Slint支持自定义字体，而且方法并不复杂。甚至只需要两个步骤就可以了
1.  `import` + 字体文件路径
2.  `default-font-family` 设置字体样式

```Slint
import "./NotoSans-Regular.ttf";

export component Example inherits Window {
    default-font-family: "Noto Sans";

    Text {
        text: "Hello World";
    }
}
```