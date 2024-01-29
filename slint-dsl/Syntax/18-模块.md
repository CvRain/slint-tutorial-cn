---
title: Slint 模块
date: 2024-01-15 17:58:12
tags:
  - Slint
---
# Slint 模块
定义在`.slint`文件用的组件能够作为一个元素被其他`.slint`文件使用，也就是说可以被导出或者导入

在一般情况下，每一个被定义在`.slint`文件中的类型都是私有的，使用`export`属性可以改变。

```Slint
component ButtonHelper inherits Rectangle {
    // ...
}

component Button inherits Rectangle {
    // ...
    ButtonHelper {
        // ...
    }
}

export { Button }
```
在这个例子中，`Button`能够被其他`.slint`文件访问，但是`ButtonHelper`不可以

也可以仅出于导出目的更改名称，而不影响其内部使用：
```Slint
component Button inherits Rectangle {
    // ...
}

export { Button as ColorButton }
```
在这里例子中，`Button`不再能够被其他`.slint`文件访问到了，取而代之的是`ColorButton`这个名字

为了方便起见，导出组件的第三种方法是立即声明它已导出：
```Slint
export component Button inherits Rectangle {
    // ...
}
```

同样，可以导入从其他文件导出的组件：
```Slint
import { Button } from "./button.slint";

export component App inherits Rectangle {
    // ...
    Button {
        // ...
    }
}
```

如果两个文件以相同的名称导出类型，则您可以选择在导入时分配不同的名称：
```Sint
import { Button } from "./button.slint";
import { Button as CoolButton } from "../other_theme/button.slint";

export component App inherits Rectangle {
    // ...
    CoolButton {} // from other_theme/button.slint
    Button {} // from button.slint
}
```

元素，全局单例以及结构体都能够被导入和导出。
还可以导出从其他文件导入的全局变量（请参阅全局单例）：
```Slint
import { Logic as MathLogic } from "math.slint";
export { MathLogic } // known as "MathLogic" when using native APIs to access globals
```

# 模块语法
导入类型支持以下语法：
```Slint
import { export1 } from "module.slint";
import { export1, export2 } from "module.slint";
import { export1 as alias1 } from "module.slint";
import { export1, export2 as alias2, /* ... */ } from "module.slint";
```

导出类型支持以下语法：
```Slint
// Export declarations
export component MyButton inherits Rectangle { /* ... */ }

// Export lists
component MySwitch inherits Rectangle { /* ... */ }
export { MySwitch };
export { MySwitch as Alias1, MyButton as Alias2 };

// Re-export all types from other module
export * from "other_module.slint";
```

# 组件库
将代码库拆分为单独的模块文件可以促进重用，并通过允许您隐藏帮助器组件来改进封装。这在项目自己的目录结构中效果很好。要在项目之间共享组件库而不对其相对路径进行硬编码，请使用组件库语法：
```Slint
import { MySwitch } from "@mylibrary";
```

在上面的示例中，MySwitch 组件将从名为“mylibrary”的组件库中导入。该库的路径必须在编译时单独定义。使用以下方法之一帮助 Slint 编译器将“mylibrary”解析为磁盘上的正确路径：
- 当使用Rust语言并存在`build.rs`文件，调用[`with_library_paths`](https://slint.dev/releases/1.3.2/docs/rust/slint_build/struct.CompilerConfiguration)来提供从库名称到路径的映射。
- 当在命令行中使用`slint-viewer`时，附加参数`-Lmylibrary=/path/to/my/library`为每一个组件库
- 在 VS 代码扩展中使用实时预览时，请使用`Slint: Library Paths`设置配置 Slint 扩展的库路径。
