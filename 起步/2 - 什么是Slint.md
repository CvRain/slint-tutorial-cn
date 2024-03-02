Slint是一个强大的声明式GUI工具包，用于在Rust、c++和JavaScript中构建本地用户界面。

Slint使开发人员能够为各种平台设计、开发和部署优雅、现代和时尚的GUI，包括嵌入式系统、桌面应用程序和Web。 它遵循一种声明式编程方法，允许您用类似于HTML/CSS的语言描述UI。表示层和业务逻辑之间的这种分离加快了设计过程。 使用Slint的Live-Preview，您可以快速迭代，调整颜色、动画、几何图形和文本，并立即验证更改。 它支持针对不同屏幕分辨率和大小的响应式布局。 Slint支持多语言支持，允许您在使用相同的代码库和集成C++、JavaScript或Rust实现的业务逻辑的同时重新设计UI。

**开发体验**
Slint提供无缝的开发人员体验： 在通用语言服务器和VS Code扩展之间选择，以获得代码完成、代码导航、诊断和语法高亮等功能。享受原生应用的灵活性，访问完整的操作系统 API，利用 CPU 和 GPU 内核，并连接到外设。 Slint 运行时是轻量级的，适合在不到 300KiB 的 RAM 中，并且它是用 Rust 构建的。 

**性能和图形渲染**
Slint 优先考虑性能和流畅的用户体验： 它使用最优的图形渲染方法，包括 GPU 加速、DMA2D、Framebuffer 或逐行渲染。 无论您是针对嵌入式设备还是桌面系统，Slint 都确保高效的性能。


## 支持的平台

### Windows

|Operating System|Architecture|
|---|---|
|Windows 10|x86-64|
|Windows 11|x86-64|

### macOS

| Operating System  | Architecture    |
| ----------------- | --------------- |
| macOS 11 Big Sur  | x86-64, aarch64 |
| macOS 12 Monterey | x86-64, aarch64 |
| macOS 13 Ventura  | x86-64, aarch64 |

### Linux

Linux 桌面发行版呈现出多样化的格局，Slint 应该在其中任何一个上运行，只要它们使用 Wayland 或 X-Windows、glibc 和 d-bus。如果 Linux 发行版提供长期支持 (LTS)，则在 Slint 版本发布时，Slint 应该在最新的 LTS 或更高版本上运行。


### 嵌入式平台
Slint可以在多种嵌入式平台上运行。一般来说，Slint需要一个具有工作OpenGL ES 2.0(或更新版本)或Vulkan驱动程序的现代Linux用户空间。我们已经成功地运行了Slint

Slint 的平台抽象允许集成到任何基于 Rust 或 C++ 的微控制器开发环境中。开发人员需要实现提供输入事件（例如触摸或键盘）的功能，以及将 Slint 渲染的像素显示到帧缓冲区或行缓冲区中。
