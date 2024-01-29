---
title: Slint 焦点处理
date: 2024-01-08 22:39:12
tags:
  - Slint
---
# Slint 焦点处理
某些元素(如TextInput)不仅接受来自鼠标/手指的输入，还接受来自(虚拟)键盘的键事件。为了让控件接收这些事件，它必须具有焦点。这可以通过has-focus (out)属性看到。

你可以通过调用focus()来手动激活元素的焦点:
```Slint
import { Button } from "std-widgets.slint";

export component App inherits Window {
    VerticalLayout {
        alignment: start;
        Button {
            text: "press me";
            clicked => { input.focus(); }
        }
        input := TextInput {
            text: "I am a text input field";
        }
    }
}
```

如果你已经在组件中包装了TextInput，那么你可以使用forward-focus属性来转发这样的焦点激活，以引用应该接收它的元素:
```Slint
import { Button } from "std-widgets.slint";

component LabeledInput inherits GridLayout {
    forward-focus: input;
    Row {
        Text {
            text: "Input Label:";
        }
        input := TextInput {}
    }
}

export component App inherits Window {
    GridLayout {
        Button {
            text: "press me";
            clicked => { label.focus(); }
        }
        label := LabeledInput {
        }
    }
}
```

如果在窗口上使用forward-focus属性，则指定的元素将在窗口第一次接收焦点时接收焦点-它将成为初始焦点元素。