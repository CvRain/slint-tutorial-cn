---
title: Slint 容器组件
date: 2024-01-08 21:00:53
tags:
  - Slint
---
# Slint 容器组件
在创建组件时，有时影响子元素在使用时的位置是很有用的。例如，假设有一个组件在用户放置的元素上方绘制一个标签:
<!--more-->
```Slint
export component MyApp inherits Window {

    BoxWithLabel {
        Text {
            // ...
        }
    }

    // ...
}
```

您可以使用布局实现这样的BoxWithLabel。默认情况下，像Text元素这样的子元素成为BoxWithLabel的直接子元素，但是我们需要它们成为布局的子元素。为此，你可以在组件的元素层次结构中使用@children表达式来改变默认的子节点位置:
```Slint
component BoxWithLabel inherits GridLayout {
    Row {
        Text { text: "label text here"; }
    }
    Row {
        @children
    }
}

export component MyApp inherits Window {
    preferred-height: 100px;
    BoxWithLabel {
        Rectangle { background: blue; }
        Rectangle { background: yellow; }
    }
}
```

上面说的有点弯弯绕了，甚至前五分钟我没有理解这到底在说什么，简单来说。假设一个用户创建了一个组件，但是这个组件实际的工作性质是一个容器，也就是可以嵌入其他组件，那么就在需要嵌入的地方塞一个`@children`

```Slint
component GridRectangle {  
    GridLayout {  
        spacing: 5px;  
        Row{  
            Rectangle {  
                width: 50px;  
                height: 50px;  
                background: #FFDDDD;  
            }  
            Rectangle {  
                width: 50px;  
                height: 50px;  
                background: #FFEEDD;  
            }  
        }  
        Row{  
            Rectangle {  
                width: 50px;  
                height: 50px;  
                background: #FFEEEE;  
                @children  
            }  
            Rectangle {  
                width: 50px;  
                height: 50px;  
                background: #FFDDEE;  
            }  
        }  
    }  
}  
  
component ColordRectangle {  
    Rectangle {  
        background: lightpink;  
        @children  
    }  
}  
  
export component MainWindow inherits Window {  
    preferred-width: 640px;  
    preferred-height: 480px;  
  
    max-height: 640px;  
    max-width: 480px;  
  
    ColordRectangle {  
        preferred-width: parent.width;  
        preferred-height: parent.height;  
        GridRectangle {}  
    }  
  
}
```

@children是一个表达式，它允许您在自定义组件中嵌套其他组件。例如，如果您想在BoxWithLabel组件中添加一个标签，您可以将子元素放在BoxWithLabel的布局中，而不是直接作为BoxWithLabel的子元素。这样，您就可以使用@children表达式来控制标签的位置。

**`@children`占位符只能在元素层次结构中出现一次**
比如上面的例子修改成为这样是不可以的
```Slint
component BoxWithLabel inherits GridLayout {
    Row {
        @children
    }
    Row {
        @children
    }
}
```


