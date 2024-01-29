---
title: Slint C++  环境配置
date: 2023-12-09 17:53:52
tags:
  - Cpp
  - Slint
---
# Slint C++ 环境配置

## 准备部分
`Slint` 支持使用`C++`,`Rust`,`JavaScript`进行开发。使用C++进行开发需要准备如下环境：
- C++ >= 20
- CMake >= 3.21
- Ninja
实际上Slint的实现库使用的语言是Rust， 简单的可以理解在C++中使用Slint时，会将Rust代码编译成为C++可以调用的库。`.slint`文件也会编译成为C++头文件以供调用。来看一下原文：
> 我们还没有提供Slint的二进制文件，所以我们将使用CMake集成，它将自动从源代码构建工具和库。由于它是用Rust编程语言实现的，这意味着您还需要安装Rust编译器（1.70或更新版本）。您可以按照Rust网站上的说明轻松安装Rust编译器。我们将使用cmake的内置FetchContent模块来获取Slint的源代码。

上一个初始使用到的`CMakeLists.txt`文件，一定要记得安装Rust并且还拥有git
```CMake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.21)
project(memory LANGUAGES CXX)

include(FetchContent)
FetchContent_Declare(
    Slint
    GIT_REPOSITORY https://github.com/slint-ui/slint.git
    # `release/1` will auto-upgrade to the latest Slint >= 1.0.0 and < 2.0.0
    # `release/1.0` will auto-upgrade to the latest Slint >= 1.0.0 and < 1.1.0
    GIT_TAG release/1
    SOURCE_SUBDIR api/cpp
)
FetchContent_MakeAvailable(Slint)

add_executable(memory_game main.cpp)
target_link_libraries(memory_game PRIVATE Slint::Slint)
slint_target_sources(memory_game memory.slint)
# On Windows, copy the Slint DLL next to the application binary so that it's found.
if (WIN32)
    add_custom_command(TARGET memory_game POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_RUNTIME_DLLS:memory_game> $<TARGET_FILE_DIR:memory_game> COMMAND_EXPAND_LISTS)
endif()
```

在第一次配置CMake的时候会自动下载并编译Slint的Rust库。可以在cmake build 文件夹中看到:

在加载CMakeLists.txt中前几次可能出现加载失败的情况，在信息中可以找到下面几行：
```
[cmake] 正克隆到 'slint-src'...
[cmake] 致命错误：在 '/mnt/Workspace/CLang/Slint/HelloWorld/build/_deps/slint-src' 检测到可疑的仓库所有权
[cmake] 要为本仓库创建特例，请运行：
[cmake] 
[cmake] 	git config --global --add safe.directory /mnt/Workspace/CLang/Slint/HelloWorld/build/_deps/slint-src
[cmake] CMake Error at slint-subbuild/slint-populate-prefix/tmp/slint-populate-gitclone.cmake:49 (message):
[cmake]   Failed to checkout tag: 'release/1'
```
然后我们从善如流，将这条指令敲一下：
```Bash
git config --global --add safe.directory /mnt/Workspace/CLang/Slint/HelloWorld/build/_deps/slint-src

git config --global --add safe.directory /mnt/Workspace/CLang/Slint/HelloWorld/build/_deps/corrosion-src
```
然后就可以喝杯`java`等待一下了


## 代码部分
熟悉CMake的人应该对此很熟悉。我们看到这个CMakeLists.txt引用了一个main.cpp，我们稍后会添加它，它还有一行slint_target_sources（memory_game memory.slint），这是一个slint函数，用于将memory.slinnt文件添加到目标。然后，我们必须在同一目录中创建memory.slint文件。现在让我们用一个你好的世界来填充它：

```Slint
// memory.slint
export component MainWindow inherits Window {
    Text {
        text: "hello world";
        color: green;
    }
}
```

这是一个名为MainWindow的组件，它继承自Window组件。下面是代码的逐行解释：
1. `export component MainWindow inherits Window {`：这行代码定义了一个名为MainWindow的组件，它继承自Window组件。Slint是一种JavaScript静态代码检查工具，它可以帮助你发现代码中的错误和问题。
2. `Text {`：这行代码定义了一个名为Text的子组件。子组件通常包含在父组件中使用的UI元素。
3. `text: "hello world"`：这行代码定义了Text组件的text属性，其值为"hello world"。
4. `color: green`：这行代码定义了Text组件的color属性，其值为"green"。

这个看上去有一种QML的味道，但是实际上又不太一样，不过QML也是通过编译成为头文件提供给C++使用的，所以猜猜接下来需要配置什么？

```C++
// main.cpp

#include "memory.h" // generated header from memory.slint

int main()
{
    auto main_window = MainWindow::create();
    main_window->run();
}
```