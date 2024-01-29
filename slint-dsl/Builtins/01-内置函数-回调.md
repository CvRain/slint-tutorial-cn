---
title: Slint 内置函数-回调
date: 2024-01-27 08:57:17
tags:
  - Slint
---
# 内置回调函数
# `init()`
每一个组件都隐式定义了一个`init`回调函数。您可以为其分配一个代码块，该代码块将在实例化元素时以及使用其最终绑定的值初始化所有属性后调用。调用顺序是从内到外。以下示例将打印“first”，然后“second”，然后“third”：
```Slint
component MyButton inherits Rectangle {
    in-out property <string> text: "Initial";
    init => {
        // If `text` is queried here, it will have the value "Hello".
        debug("first");
    }
}

component MyCheckBox inherits Rectangle {
    init => { debug("second"); }
}

export component MyWindow inherits Window {
    MyButton {
        text: "Hello";
        init => { debug("third"); }
    }
    MyCheckBox {
    }
}
```

不要使用回调函数用来初始化组件的属性，因为这违反了描述式语法的规则。避免以这样的方式使用回调，除非真的需要它，例如，为了通知某些本机代码：
```Slint
global SystemService  {
    // This callback can be implemented in native code using the Slint API
    callback ensure_service_running();
}

export component MySystemButton inherits Rectangle {
    init => {
        SystemService.ensure_service_running();
    }
    // ...
}
```
