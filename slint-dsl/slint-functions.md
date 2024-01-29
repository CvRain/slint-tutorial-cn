---
title: Slint 函数
date: 2024-01-12 10:30:07
tags:
  - Slint
---
# Slint 函数
在slint中定义一个函数需要使用到`function`关键字

函数可以在括号中添加参数，参数需要遵循`函数名:类型`的格式。这些参数可以通过函数体内的名称来引用。

函数也可以返回一个值，返回的类型需要使用`->`符号在函数中标明。
`function 函数名称(参数1: 类型) -> 返回类型`
`return `关键字在函数体内使用以返回声明类型的表达式。如果函数没有显式返回值，则默认返回最后一条语句的值。（这一点和Rust的函数语法很像）

函数可以用 `pure` 关键字注释。这表明该功能不会引起任何副作用。更多详细信息可以在“纯度”章节中找到。

默认情况下，函数是私有的，无法从外部组件访问。但是，可以使用 `public` 或 `protected` 关键字修改它们的可访问性。
- 用 `public` 注解的函数可以被任何组件访问。
- 用 `protected` 注解的函数只能被直接继承它的组件访问。

## 例子
```Slint
export component Example {
    in-out property <int> min;
    in-out property <int> max;
    protected function set-bounds(min: int, max: int) {
        root.min = min;
        root.max = max
    }
    public pure function inbound(x: int) -> int {
        return Math.min(root.max, Math.max(root.min, x));
    }
}
```
在上面的示例中，set-bounds 是一个受保护的函数，用于更新根组件的 min 和 max 属性。入站函数是一个公共纯函数，它接受整数 x 并返回限制在最小和最大边界内的值。

