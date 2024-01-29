---
title: Slint 消息回调
date: 2024-01-12 12:09:49
tags:
  - Slint
---
# 消息回调
组件能够定义回调以和外界交流改变的状态信息。回调是通过“调用”它们来调用的，就像调用函数一样。
***个人的理解，消息回调类似于Qt中常用的信号槽之槽函数。***
您可以通过使用 => 箭头语法声明处理程序来对回调调用做出反应。内置的 TouchArea 元素声明一个 clicked 回调，当用户触摸该元素覆盖的矩形区域或用鼠标单击该区域时会调用该回调。在下面的示例中，通过声明处理程序并调用我们的自定义回调，将该回调的调用转发到另一个自定义回调（hello）：
```Slint
export component Example inherits Rectangle {
    // declare a callback
    callback hello;

    area := TouchArea {
        // sets a handler with `=>`
        clicked => {
            // emit the callback
            root.hello()
        }
    }
}
```

可以向回调添加参数：
```Slint
export component Example inherits Rectangle {
    // declares a callback
    callback hello(int, string);
    hello(aa, bb) => { /* ... */ }
}
```

回调也可能返回一个值：
```Slint
export component Example inherits Rectangle {
    // declares a callback with a return value
    callback hello(int, int) -> int;
    hello(aa, bb) => { aa + bb }
}
```

# 别名
可以用与双向绑定类似的方式声明回调别名：
```Slint
export component Example inherits Rectangle {
    callback clicked <=> area.clicked;
    area := TouchArea {}
}
```
