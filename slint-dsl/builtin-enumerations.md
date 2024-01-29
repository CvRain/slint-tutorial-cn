---
title: Slint 内置枚举元素
date: 2024-01-28 20:33:33
tags:
  - Slint
---
# Slint 内置枚举元素

## `AccessibleRole`
 此枚举表述了`accessible-role`属性中的不同值， 用于描述辅助技术（例如屏幕阅读器）中元素的作用。
 - `none` 此元素是不可访问的
 - `button` 此元素是一个`Button`组件其或者行为类似`Button`
 - `checkbox` 此元素是一个`CheckBox`或者其行为类型`CheckBox`
 - `combobox` 此元素是一个`ComboBox`或者其行为类型`ComboBox`
 - `slider` 此元素是一个`Slider`或者其行为类型`Slider`
 - `spinbox` 此元素是一个`SpinBox`或者其行为类型`SpinBox`
 - `tab` 此元素是一个`Tab`或者其行为类型`Tab`
 - `text` 此元素是一个`Text`或者其行为类型`Text`
 - `progress-indicator` 此元素是一个`ProcessIndicator`或者其行为类型`ProcessIndicator`

## `DialogButtonRole`
此枚举表示`dialog-button-role`属性的值，该属性可以添加到`Dialog`的任何元素以将该项目放入按钮行中，其确切位置取决于角色和平台。

- `none` 这个按钮不是用来进入最下面一排的
- `accept` 此规则让主按钮点击后为确认对话框，如“确实"或者”是“
- `reject`  此规则让主按钮点击为拒绝对话框，如“取消”或者”不“
- `apply` “Apply（接受）”按钮的职责
- `reset`“Rest （重置）” 按钮的职责
- `help` “Help（帮助）”按钮的职责
- `action`  执行其他操作的任何其他按钮的的功能。

## `EventResult`
该枚举描述事件处理程序是否拒绝或接受事件。
- `reject`  该事件被此事件处理程序拒绝，然后可能由父项处理
- `accept` 该事件已被接受，不会进一步处理

## `FillRule`
该枚举描述了决定路径所描述的形状内部应该是什么的不同方法。
- **`nonzero`**: The [“nonzero” fill rule as defined in SVG](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/fill-rule#nonzero).
- **`evenodd`**: The [“evenodd” fill rule as defined in SVG](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/fill-rule#evenodd)

## `ImageFit`
该枚举定义了图片资源在`Image`组件中怎样的适配方式
- `fill` 图片的尺寸和缩放根据`Image`元素的宽度和高度自己适配
- `contain` 源图像被缩放以适应图像元素的尺寸，同时保留纵横比。
- `cover` 源图像被缩放以覆盖图像元素的尺寸，同时保留纵横比。如果源图像的长宽比与元素的长宽比不匹配，则图像将被剪裁以适合。
## `ImageRendering`
该枚举指定如何缩放源图像。
- `smooth` 使用线性插值算法缩放图像。
- `pixelated` 使用最近邻算法缩放图像。

# `InputType`
该枚举用于定义输入字段的类型。

- `text` 默认值。以标准的方式渲染所有的字符
- `password` 默认的将所有的字符均渲染为“\*”
- `number` 输入将只能接受并渲染数字字符（0-9）
- `decimal` 如果字符是小数的有效部分，这将接受并呈现字符

## `LayoutAlignment`
枚举类型描述了在`HorizontalBox`, `VerticalBox`, `HorizontalLayout`, `VerticalLayout`中的布局对其方式
- `stretch` 使用布局中所有元素的最小大小，在所有元素之间基于 `*-stretch `分配剩余空间。
- `center` 对所有元素使用首选大小，在第一个元素之前和最后一个元素之后均匀分配剩余空间。
- `start` 对所有元素使用首选大小，将剩余空间放在最后一个元素之后。
- `end` 对所有元素使用首选大小，将剩余空间放在第一个元素之前。
- `space-between` 对所有元素使用首选大小，在元素之间均匀分配剩余空间。
- `space-around` 对所有元素使用首选大小，在第一个元素之前、最后一个元素之后以及元素之间均匀分配剩余空间。

## `MouseCurosr`
该枚举代表不同类型的鼠标光标。它是 CSS 中可用鼠标光标的子集。有关详细信息和象形图，请参阅光标的 MDN 文档([MDN Documentation for cursor](https://developer.mozilla.org/en-US/docs/Web/CSS/cursor#values))。根据后端和使用的操作系统，单向调整大小光标可能会替换为双向光标。
- **`default`**: 系统默认的光标
- **`none`**: 无光标展示。
- **`help`**: 表明是帮助信息的光标。
- **`pointer`**: 表示为具有链接的光标。
- **`progress`**: 程序正在忙，但仍然可以交互。
- **`wait`**:  程序正在忙，需要等待。
- **`crosshair`**: 一个十字线   。
- **`text`**:  选中文本。
- **`alias`**: 正在创建别名或快捷方式。
- **`copy`**: 一个副本已被创建。
- **`move`**: 某些东西被移动了。
- **`no-drop`**: 一些东西不能往这里丢。
- **`not-allowed`**: 一个不被允许的行为。
- **`grab`**: 有东西是可以抓住的。
- **`grabbing`**: 有东西被抓住了。
- **`col-resize`**: 指示列可水平调整大小。
- **`row-resize`**: 指示行可垂直调整大小。
- **`n-resize`**: 向北单向调整大小。
- **`e-resize`**: 向东单向调整大小。
- **`s-resize`**: 向南单向调整大小。
- **`w-resize`**: 向西单向调整大小。
- **`ne-resize`**: 向东北方向单向调整大小。
- **`nw-resize`**: 向西北单向调整大小。
- **`se-resize`**: 单向调整东南方向大小。
- **`sw-resize`**: 向西南方向单向调整大小。
- **`ew-resize`**: 东西向双向调整大小。
- **`ns-resize`**: 南北双向调整大小。
- **`nesw-resize`**: 东北-西南双向调整大小。
- **`nwse-resize`**: 西北-东南方向双向调整大小。

## `Orientation`
表示元素或小部件（例如滑块）的方向。
- `horizontal`: 水平元素定向
- `vertical`: 垂直元素定向

## `PathEvent`
PathEvent 是描述路径组成的低级数据结构。通常，它是在编译时根据更高级别的描述（例如 SVG 命令）生成的。
- **`begin`**：路径的开始。  
- **`line`**：路径上的直线。  
- **`quadratic`**：路径上的二次贝塞尔曲线。  
- **`cubic`**：路径上的三次贝塞尔曲线。  
- **`end-open`**：保持开放的路径末端。  
- **`end-closed`**：闭合路径的末端。

## `PointerEventButton`
该枚举描述了指针事件的不同类型的按钮，通常是鼠标或铅笔上的按钮。
- `other`： 非左、右、中按钮。例如，这用于具有多个按钮的鼠标上的第四个按钮。
- `left`：左按钮。
- `right`：右按钮。
- `middle`：中按钮。

## `PointerEventKind`
枚举描述了 `PointerEventButton` 事件中存在的元素
- `cancel`：行动被取消了。
- `down`：按钮被按下了。
- `up`：按钮被释放了（抬起按钮）。

## `SortOrder`
该枚举表示排序顺序属性的不同值。它用于按列对 [`StandardTableView`](https://slint.dev/releases/1.3.2/docs/slint/src/language/widgets/standardtableview.html) 进行排序。
- `unsorted`：此列未能排序。
- `ascending`：该列按升序排序。
- `descending`：该列按降序排序。

## `StandardButtonKind`
使用此枚举将标准按钮添加到[`Dialog`](https://slint.dev/releases/1.3.2/docs/slint/src/language/builtins/elements.html#dialog)中。这些[`StandardButton`](https://slint.dev/releases/1.3.2/docs/slint/src/language/widgets/standardbutton.html)标准按钮的外观和位置取决于应用程序运行的环境（操作系统、UI 环境等）。

（按照本人的理解是，在一个弹出的对话框中可以添加多个按钮，每个按钮有不同作用以及相对应的功能）

- `ok`：接受对话框的“确定”按钮，单击时将其关闭。
- `cancel`：一个“取消”按钮，用于拒绝对话框，单击时将其关闭。
- `apply`：一个“应用”按钮，应该接受来自对话框的值而不关闭它。
- `close`：“关闭”按钮，它应该关闭对话框而不查看值。
- `reset`：“重置”按钮，该按钮应将对话框重置为其初始状态。
- `help`：“帮助”按钮，单击时应显示上下文相关文档。
- `yes`：“是”按钮，用于确认操作。
- `no`：“否”按钮，用于拒绝操作。
- `abort`：“中止”按钮，用于中止操作。
- `retry`：“重试”按钮，用于重试失败的操作。
- `ignore`：“忽略”按钮，用于忽略失败的操作。

## `TextHorizontalAlignment`
该枚举描述了文本沿 Text 元素水平轴的不同类型的对齐方式。
- `left`：文本将与包含框的左边缘对齐。
- `center`：文本将在包含框中水平居中。
- `right`：文本将与包含框的右侧对齐。

## `TextOverflow`
该枚举描述了当文本太宽而无法适应文本宽度时文本的显示方式。
- `clip`：文本将被简单地剪切。
- `elide`：多出的文本会以省略号呈现

## `TextVerticalAlignment`
该枚举描述了文本沿 Text 元素垂直轴的不同类型的对齐方式。
- `top`：文本将与包含框的顶部对齐。
- `center`：文本将在包含框中垂直居中。
- `bottom`：文本将与包含框的底部对齐。

## `TextWrap`
该枚举描述了文本太宽而无法适应文本宽度时如何换行。
- `no-warp`：文本不会换行，而是会溢出。
- `word-warp`：文本将在单词边界处换行。