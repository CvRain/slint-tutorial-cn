---
title: Slint 表达式
date: 2024-01-11 11:18:07
tags:
  - Slint
---
# Slint 表达式
表达式是在用户界面中声明关系和连接的强大方式。它们通常用于将基本算术与访问其他元素的属性结合起来。当这些属性发生变化时，表达式会自动重新求值，并为与该表达式关联的属性赋一个新值:
```Slint
export component Example {
    // declare a property of type int
    in-out property<int> my-property;

    // This accesses the property
    width: root.my-property * 20px;

}
```
当`my-property`改变时，宽度也会自动改变

数字表达式中的算术运算与大多数编程语言中的操作符*、+、-、/类似:
```Slint
export component Example {
    in-out property <int> p: 1 * 2 + 3 * 4; // same as (1 * 2) + (3 * 4)
}
```

用`+`串联字符串。
运算符&&和||表示布尔值之间的逻辑与或。操作符`==`、`!=`、`>`、`<`、`>=`和`<=`比较相同类型的值。

访问元素的属性，方法是使用元素的名称，后跟一个“`.`属性名”:
```Slint
export component Example {
    foo := Rectangle {
        x: 42px;
    }
    x: foo.x;
}
```

三目运算符`expression : expression ? expression` 在slint中也是被支持的，就像C语言和JavaScript中一样
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    Rectangle {
        touch := TouchArea {}
        background: touch.pressed ? #111 : #eee;
        border-width: 5px;
        border-color: !touch.enabled ? #888
            : touch.pressed ? #aaa
            : #555;
    }
}
```
