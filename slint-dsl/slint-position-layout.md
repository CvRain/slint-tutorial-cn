---
title: Slint 元素的定位和布局
date: 2023-12-22 18:14:21
tags:
  - Slint
---
2# Slint 元素的定位和布局
所有视觉元素都显示在一个窗口中。 x 和 y 属性存储元素相对于其父元素的坐标。 Slint 通过将父元素的位置添加到元素的位置来确定元素的绝对位置。如果父元素存在了一个坐标，那么子类就会从进行坐标的累加计算，直到顶级元素。
`width`和`height`会存储元素的宽度和高度（很容易理解不是）

您可以通过两种方式放置元素来创建整个图形用户界面：
- 通过`x`,`y`,`width`,`height`属性显式创建
- 通过布局元素自动设置

显式放置非常适合元素很少的静态场景。布局适用于复杂的用户界面，并有助于创建可扩展的用户界面。布局元素表达元素之间的几何关系。

## 显式放置
```slint
export component Example inherits Window {
    width: 200px;    //设置窗口宽度为200px
    height: 200px;   //设置窗口高度为200px
    Rectangle {      //定义一个矩形
        x: 100px;
        y: 70px;
        width: parent.width - self.x;    // 宽度是父元素宽度减自身x位置
        height: parent.height - self.y;
        background: blue;    // 背景颜色是蓝色
        Rectangle {
            x: 10px;
            y: 5px;
            width: 50px;
            height: 30px;
            background: green;
        }
    }
}
```

两个矩形的位置和内部绿色矩形的大小都是固定的。外部蓝色矩形的大小是使用宽度和高度属性的绑定表达式自动计算的。计算结果是左下角与窗口的角对齐 - 只要窗口的宽度和高度发生变化，它就会更新。

当为任何几何属性指定显式值时，Slint 要求您为数字附加一个单位。您可以选择两种不同的单位：
- 逻辑像素，使用px单位后缀。这是推荐单位。
- 物理像素，使用phx单位后缀

逻辑像素会根据系统配置的设备像素比率自动缩放。  宽度和高度属性也可以指定为百分比单位，该单位相对于父元素应用。例如宽度：50% 表示父级宽度的一半。  
  
x 和 y 属性的默认值使得元素在其父元素中居中。  
  
宽度和高度的默认值取决于元素的类型。有些元素会根据它们的内容自动调整大小，比如Image、Text和大多数小部件。以下元素没有内容，当它们没有子元素时，默认会填充它们的父元素:
- `Rectangle`
- `TouchArea`
- `FocusScope`
- `Flickable`
布局也默认填充父级，无论它们自己的首选大小如何。
其他元素（包括没有基础的自定义元素）默认使用其首选尺寸。

## 首选尺寸
元素的首选大小可以通过preferred-width和preferred-height属性指定。

当未显式设置时，首选大小取决于子元素，并且是具有更大首选大小的子元素的首选大小，其x和y属性未设置。因此，首选大小从子到父计算，就像其他约束(最大和最小大小)一样，除非显式覆盖。

一种特殊情况是使用100%作为值将首选大小设置为父元素的大小。例如，这个组件将默认使用父组件的大小:
```slint
component MyComponent {
    preferred-width: 100%;
    preferred-height: 100%;
    // ...
}
```

## 使用布局自动放置
Slint带有不同的布局元素，可以自动计算其子元素的位置和大小:
- `verticallout` / `HorizontalLayout`:子元素沿着垂直或水平轴放置。可以理解是水平对齐和垂直布局
- `GridLayout` 元素通过行列进行放置
布局是可以嵌套的，通过嵌套布局可以完成更加复杂的组建排布

您可以使用不同的约束来调整自动放置，以适应用户界面的设计。每个元素都有最小大小、最大大小和首选大小。使用以下属性显式设置这些属性:
- `min-width`
- `min-height`
- `max-width`
- `max-height`
- `preferred-width`
- `preferred-height`
任何指明了`width`和`height`的元素都会存在固定的大小（比如给MainWindow设置了大小，那么就无法再放大和缩小窗口了）

当布局中有额外的空间时，元素可以沿着布局轴拉伸。你可以用这些属性来控制元素和它的兄弟元素之间的拉伸系数:
- `horizontal-stretch`
- `vertical-stretch`
值为 0 意味着元素根本不会拉伸。如果所有元素的拉伸因子都为 0，则所有元素都会同等拉伸。
这些约束属性的默认值可能取决于元素的内容。如果元素的 x 或 y 未设置，这些约束也会自动应用于父元素。 

## 布局元素的通用属性
- `spacing` 控制两个并列元素的间距
- `padding` 这指定布局内的填充、元素之间的空间以及布局的边框距离（可以参考css的盒子模型）。
如果不设置spacing的大小（默认为0）则会导致两个元素堆积在一起。为了进行更细粒度的控制，可以将 padding 属性拆分为布局每一侧的属性：
- `padding-left`
- `padding-right`
- `padding-top`
- `padding-bottom`

## `VerticalLayout` 和 `HorizontalLayout`
`verticalalLayout`和`HorizontalLayout`元素将它们的子元素放置在列或行中。默认情况下，它们会拉伸或收缩以占用整个空间。您可以根据需要调整元素的对齐方式。

下面的示例将蓝色和黄色矩形放在一行中，并均匀地拉伸到200个逻辑像素的宽度上:
```slint
// Stretch by default
export component Example inherits Window {
    width: 200px;
    height: 200px;
    HorizontalLayout {
        Rectangle { background: blue; min-width: 20px; }
        Rectangle { background: yellow; min-width: 30px; }
    }
}
```
另一方面，下面的示例指定矩形应对齐布局的开始(可视左侧)。这将导致不拉伸，而是矩形保留其指定的最小宽度:
```slint
// Unless an alignment is specified
export component Example inherits Window {
    width: 200px;
    height: 200px;
    HorizontalLayout {
        alignment: start;
        Rectangle { background: blue; min-width: 20px; }
        Rectangle { background: yellow; min-width: 30px; }
    }
}
```

下面的例子包含了两个更复杂场景的布局:
```slint
export component Example inherits Window {
    width: 200px;
    height: 200px;
    HorizontalLayout {
        // Side panel
        Rectangle { background: green; width: 10px; }

        VerticalLayout {
            padding: 0px;
            //toolbar
            Rectangle { background: blue; height: 7px; }

            Rectangle {
                border-color: red; border-width: 2px;
                HorizontalLayout {
                    Rectangle { border-color: blue; border-width: 2px; }
                    Rectangle { border-color: green; border-width: 2px; }
                }
            }
            Rectangle {
                border-color: orange; border-width: 2px;
                HorizontalLayout {
                    Rectangle { border-color: black; border-width: 2px; }
                    Rectangle { border-color: pink; border-width: 2px; }
                }
            }
        }
    }
}
```


## 对齐方式
如果指定了每个元素的宽度或高度，则根据它们的宽度或高度设置大小，否则它将被设置为最小大小，这是通过min-width或min-height属性设置的，或者是内部布局的最小大小，无论大小较大。

元素按照对齐方式放置。只有当布局的对齐属性为layoutalalignment时，元素的大小才会大于最小尺寸。拉伸(默认)

这个例子展示了不同的对齐可能性
```slint
export component Example inherits Window {
    width: 300px;
    height: 200px;
    VerticalLayout {
        HorizontalLayout {
            alignment: stretch;
            Text { text: "stretch (default)"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: start;
            Text { text: "start"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: end;
            Text { text: "end"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: start;
            Text { text: "start"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: center;
            Text { text: "center"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: space-between;
            Text { text: "space-between"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
        HorizontalLayout {
            alignment: space-around;
            Text { text: "space-around"; }
            Rectangle { background: blue; min-width: 20px; }
            Rectangle { background: yellow; min-width: 30px; }
        }
    }
}
```
![](/img/slint/20231222205719.png)

## 拉伸算法
当对齐设置为拉伸(默认值)时，元素的大小将达到最小大小，然后额外的空间将在元素之间共享，这些元素与使用水平拉伸和垂直拉伸属性设置的拉伸因子成比例。拉伸后的尺寸不会超过最大尺寸。拉伸因子是一个浮点数。具有默认内容大小的元素通常默认为0，而默认为其父元素大小的元素默认为1。伸缩因子为0的元素将保持其最小大小，除非所有其他元素的伸缩因子也为0或达到其最大大小。
```slint
export component Example inherits Window {
    width: 300px;
    height: 200px;
    VerticalLayout {
        // Same stretch factor (1 by default): the size is divided equally
        HorizontalLayout {
            Rectangle { background: blue; }
            Rectangle { background: yellow;}
            Rectangle { background: green;}
        }
        // Elements with a bigger min-width are given a bigger size before they expand
        HorizontalLayout {
            Rectangle { background: cyan; min-width: 100px;}
            Rectangle { background: magenta; min-width: 50px;}
            Rectangle { background: gold;}
        }
        // Stretch factor twice as big:  grows twice as much
        HorizontalLayout {
            Rectangle { background: navy; horizontal-stretch: 2;}
            Rectangle { background: gray; }
        }
        // All elements not having a maximum width have a stretch factor of 0 so they grow
        HorizontalLayout {
            Rectangle { background: red; max-width: 20px; }
            Rectangle { background: orange; horizontal-stretch: 0; }
            Rectangle { background: pink; horizontal-stretch: 0; }
        }
    }
}
```
![](20231222205558.png)

```slint
export component Example inherits Window {
    width: 200px;
    height: 50px;
    HorizontalLayout {
        Rectangle { background: green; }
        for t in [ "Hello", "World", "!" ] : Text {
            text: t;
        }
        Rectangle { background: blue; }
    }
}
```
![](20231222205536.png)

## 表格对齐
GridLayout将元素放置在网格中。每个元素获得row、col、rowspan和colspan属性。可以使用Row子元素，也可以显式设置Row属性。这些属性在编译时必须是静态已知的，因此不可能使用算术运算或依赖属性。到目前为止，在网格布局中不允许使用for或if。
```slint
export component Foo inherits Window {
    width: 200px;
    height: 200px;
    GridLayout {
        spacing: 5px;
        Row {
            Rectangle { background: red; }
            Rectangle { background: blue; }
        }
        Row {
            Rectangle { background: yellow; }
            Rectangle { background: green; }
        }
    }
}
```
![效果图](img/slint/20231222205302.png)

```slint
export component Foo inherits Window {
    width: 200px;
    height: 150px;
    GridLayout {
        spacing: 0px;
        Rectangle { background: red; }
        Rectangle { background: blue; }
        Rectangle { background: yellow; row: 1; }
        Rectangle { background: green; }
        Rectangle { background: black; col: 2; row: 0; }
    }
}
```

![[20231222205507.png]]

##  一个综合实例
```slint
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
            }
            Rectangle {
                width: 50px;
                height: 50px;
                background: #FFDDEE;
            }
        }
    }
}

export component MainWindow inherits Window {
    in property <int> window_width;
    in property <int> window_height;

    preferred-width: 640px;
    preferred-height: 480px;

    max-height: 640px;
    max-width: 480px;

    Rectangle {
        width: parent.width;
        height: parent.height;
        background: #FFEEFF;

        HorizontalLayout {
            alignment: space-between;
            GridRectangle{}
            Rectangle {
                height: parent.height;
                width: 15%;
                background: #FFCCBB;
            }
        }
    }
}

```
![效果图](img/slint/20231222204439.png)
