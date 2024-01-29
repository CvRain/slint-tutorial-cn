---
title: Slint Stats语句
date: 2024-01-14 13:02:43
tags:
  - Slint
---
# Slint States
states 语句允许一次性声明状态并设置多个元素的属性：
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;
    default-font-size: 24px;

    label := Text { }
    ta := TouchArea {
        clicked => {
            active = !active;
        }
    }
    property <bool> active: true;
    states [
        active when active && !ta.has-hover: {
            label.text: "Active";
            root.background: blue;
        }
        active-hover when active && ta.has-hover: {
            label.text: "Active\nHover";
            root.background: green;
        }
        inactive when !active: {
            label.text: "Inactive";
            root.background: gray;
        }
    ]
}
```
在此示例中，活动和活动悬停状态是根据活动布尔属性的值和 TouchArea 的 has-hover 来定义的。当用户将鼠标悬停在示例上时，它将在蓝色和绿色背景之间切换，并相应地调整文本标签。单击可切换活动属性，从而进入非活动状态。

# Transitions
转换将动画绑定到状态更改
此示例定义了两个转换。首先，out 关键字用于在离开禁用状态时为所有属性设置 800 毫秒的动画。第二个转换使用 in 关键字在转换到按下状态时对背景进行动画处理。

```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    text := Text { text: "hello"; }
    in-out property<bool> pressed;
    in-out property<bool> is-enabled;

    states [
        disabled when !root.is-enabled : {
            background: gray; // same as root.background: gray;
            text.color: white;
            out {
                animate * { duration: 800ms; }
            }
        }
        down when pressed : {
            background: blue;
            in {
                animate background { duration: 300ms; }
            }
        }
    ]
}
```
