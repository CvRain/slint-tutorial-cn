---
title: Slint 内置元素
date: 2024-01-27 09:06:22
tags:
  - Slint
---
# 内置元素

## 通用属性
### 几何坐标
这些属性对所有可见项目都有效：
- `width` 和 `height` (length)：元素的尺寸，被设置后可以重写默认的大小
- `x`和`y`（length）：元素相对于其父级元素的偏移位置
- `z`(float)：允许指定不同的顺序来将项目与其同级项目堆叠。 （默认值：0）
- `absolute-position`(float)：元素在所包含窗口中的位置。
