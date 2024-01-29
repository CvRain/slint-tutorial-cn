---
title: Slint  重复（repetition）
date: 2024-01-13 12:25:35
tags:
  - Slint
---
# Slint Repetition
使用 `for in`语句可以多次创建同一元素
语法看起来长这个样子：`for name[index] in model : id := Element { ... }

model 能够是以下类型：
- 一个整数，在这种情况下该元素将重复指定的次数
- 原生声明的数组类型或模型，在这种情况下，将为数组或模型中的每个元素实例化该元素。
该名称可用于在元素内查找，并且将类似于设置为模型值的伪属性。索引是可选的，将设置为模型中该元素的索引。 id 也是可选的。

# 示例
```Slint
export component Example inherits Window {
    preferred-width: 300px;
    preferred-height: 100px;
    for my-color[index] in [ #e11, #1a2, #23d ]: Rectangle {
        height: 100px;
        width: 60px;
        x: self.width * index;
        background: my-color;
    }
}
```
创建了一个`color`类型的数组`[ #e11, #1a2, #23d ]`，对它进行遍历，`index`的内容就会从0到2, my-color的颜色就会从`#e11, #1a2, #23d`进行选择。


```Slint
export component Example inherits Window {
    preferred-width: 50px;
    preferred-height: 50px;
    in property <[{foo: string, col: color}]> model: [
        {foo: "abc", col: #f00 },
        {foo: "def", col: #00f },
    ];
    VerticalLayout {
        for data in root.model: my-repeated-text := Text {
            color: data.col;
            text: data.foo;
        }
    }
}
```