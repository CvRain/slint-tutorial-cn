---
title: Slint 全局单例
date: 2024-01-15 17:40:01
tags:
  - Slint
---
# Slint 全局单例
定义一个全局单例的实例需要使用到`global Name { /*各种属性和回调函数*/} `的方式，以使属性和回调在整个项目中可用。使用 Name.property 访问它们。

```Slint
global Palette  {
    in-out property<color> primary: blue;
    in-out property<color> secondary: green;
}

export component Example inherits Rectangle {
    background: Palette.primary;
    border-color: Palette.secondary;
    border-width: 2px;
}
```

导出全局以使其可从其他文件访问（请参阅模块[Modules](https://slint.dev/releases/1.3.2/docs/slint/src/language/syntax/modules.html)）。从文件中导出全局变量，同时导出主应用程序组件，以使其对业务逻辑中的本机代码可见。
```Slint
export global Logic  {
    in-out property <int> the-value;
    pure callback magic-operation(int) -> int;
}
```

**在Rust中的使用方式**
```Rust
slint::slint!{
export global Logic {
    in-out property <int> the-value;
    pure callback magic-operation(int) -> int;
}

export component App inherits Window {
    // ...
}
}

fn main() {
    let app = App::new();
    app.global::<Logic>().on_magic_operation(|value| {
        eprintln!("magic operation input: {}", value);
        value * 2
    });
    app.global::<Logic>().set_the_value(42);
    // ...
}
```

**在C++中的使用方式**
```C++
#include "app.h"

fn main() {
    auto app = App::create();
    app->global<Logic>().on_magic_operation([](int value) -> int {
        return value * 2;
    });
    app->global<Logic>().set_the_value(42);
    // ...
}
```

可以使用双向绑定语法从全局重新公开回调或属性。
```Slint
global Logic  {
    in-out property <int> the-value;
    pure callback magic-operation(int) -> int;
}

component SomeComponent inherits Text {
    // use the global in any component
    text: "The magic value is:" + Logic.magic-operation(42);
}

export component MainWindow inherits Window {
    // re-expose the global properties such that the native code
    // can access or modify them
    in-out property the-value <=> Logic.the-value;
    pure callback magic-operation <=> Logic.magic-operation;

    SomeComponent {}
}
```