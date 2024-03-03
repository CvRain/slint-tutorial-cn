
## 前置准备
- 可以使用任意支持Rust语言的IDE进行开发，比如vscode和RustRover。
- 最好能够支持Slint语法的编辑器。

## 创建项目
1. 通过命令创建： `cargo new first_window`
2. 或者直接通过IDE直接创建

## 添加Slint库
1. 通过指令添加：`cargo add slint@1.4.1`
2. 在`Cargo.toml`中手动添加： 
```toml
[package]
name = "first_window"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
slint = "1.4.1"
```

## 创建窗口

### 第一次创建窗口

编辑 `main.rs`
```Rust
fn main() {
    MainWindow::new().unwrap().run().unwrap();
}

slint::slint! {
    export component MainWindow inherits Window {
        Text {
            text: "hello world";
            color: green;
        }
    }
}
```

之后编译运行，就得到了一个窗口出现在屏幕中，Slint的窗口默认是跟随系统主题的，由于我运行在Arch hyprland 深色主题的环境上，所以窗口呈现的是主题设置的颜色。如果是在windows上运行，应该是一个经典的窗口。

![](Img/0-4-1.png)

我们观察代码，不难发现这里使用了一个`slint::slint!`的宏，在其中使用Slint语法格式完成的窗口定义。而且实际代码书写中，不会大面积的使用内嵌代码的方式去构建一个应用，我们需要将UI布局，也就是Slint文件拆出来进行编写。


### 第二次创建窗口

**创建并编辑 ui/applicatin.silnt**
```Slint
export component MainWindow inherits Window {
}
```

**在项目中创建build.rs**
```Rust
fn main(){
    slint_build::compile("ui/application.slint").unwrap();
}
```
这只是一个简略写法，事实上我们可以遍历整个ui目录，批量编译各个文件，当然也可以发挥你自己的想法加入比如多线程编译或者其他内容。


在`Cargo.toml`进行如下编辑：
```toml
[package]
...
build = "build.rs"

[dependencies]
...

[build-dependencies]
slint-build = "1.4.1"
```
到了这一步后，先进行`cargo build`以下，让cargo_build帮我生成rust文件。

**编辑 main.rs**
```Rust
slint::include_modules!();

fn main() {
    MainWindow::new()
    .expect("Cloud not create window")
    .run();
}
```
接下来编译运行项目，就再次可以看到窗口啦！


当看到这里就可以发现，slint文件和qt的qwidget、qml，gtk的Cambalache类似，会在编译源码之前，将ui文件编译为对应平台对应系统的代码文件。在Rust中就将MainWindow编译为MainWindow模块（通过`slint::include_modules!();`导入），在C++就编译为一个名为MainWindow.h的头文件。再之后聊到控件的交互通信会详细介绍这些内容。