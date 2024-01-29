---
title: Slint 属性
date: 2024-01-11 09:57:49
tags:
  - Slint
---
# Slint 属性
所有元素均有属性。内置元素具有常见特性，如颜色或尺寸特性。你可以为它们设定一个值或者一个完整的表达式。
```Slint
export component Example inherits Window {
    // 简单表达式：以分号结尾
    width: 42px;
    // 或者用一个代码块包裹，这样就没有分号了
    height: { 42px }
}
```
***（一股浓郁的Rust语法味道扑面而来）***

属性的默认值一般是属性的类型的默认值，比如说布尔的默认是`false`，整数`int`的默认值是`0`。

除了现有属性外，还可以通过指定名称、类型和默认值（可选）来定义额外的属性：
```Slint
export component Example {
    // 定义了一个叫做my-property的int类型的扩展属性
    property<int> my-property;

    // 定义了一个叫做my-second-property的int类型的属性，并设置其默认值为42
    property<int> my-second-property: 42;
}
```

用限定符注释额外的属性，指定如何读取和写入属性：
- `private` ：默认为此，属性仅能在当前组件内被访问
- `in`：这个属性能被输入，它可以由该组件的用户设置和修改，例如通过绑定或回调中的赋值。组件可以提供默认绑定，但不能通过赋值覆盖它
- `out`：只能由组件设置的输出属性。对于组件的用户来说，它是只读的。
- `in-out`：任何人都可以对此属性进行读写
```Slint
export component Button {
    // 这意味着通过用户设置组件
    in property <string> text;
    // 表明了该属性将由组件的用户读取。
    out property <bool> pressed;
    // 此属性意味着用户和组件本身都可以更改。
    in-out property <bool> checked;

    // 此属性是该组件的内部属性。
    private property <bool> has-mouse;
}
```
当使用组件作为元素时，或者通过业务逻辑的语言绑定，可以从外部访问在组件顶层声明的所有非私有属性。

# 绑定
当表达式中访问的属性发生更改时，会自动重新评估绑定表达式。

在以下示例中，当用户按下按钮时，按钮的文本会自动更改。增加计数器属性会自动使绑定到文本的表达式无效并触发重新计算。
```Slint
import { Button } from "std-widgets.slint";
export component Example inherits Window {
    preferred-width: 50px;
    preferred-height: 50px;
    Button {
        property <int> counter: 3;
        clicked => { self.counter += 3 }
        text: self.counter * 2;
    }
}
```
在查询属性时惰性地进行重新计算。

在内部，在计算绑定时访问的任何属性都要注册依赖项。当属性更改时，会通知依赖项，并将所有依赖绑定标记为脏绑定。***(脏绑定是由于其所依赖的某个属性发生更改而被标记为无效的绑定。当查询属性时，脏绑定将被重新计算)***

默认情况下，本机代码中的回调不依赖于任何属性，除非它们查询本机代码中的属性。

## 双向绑定
使用<=>语法在属性之间创建双向绑定。这些属性将被链接在一起，并且总是包含相同的值。

<=>的右侧必须是对同一类型属性的引用。对于双向绑定，属性类型是可选的，如果没有指定，它将被推断出来。

```Slint 
export component Example  {
    in property<brush> rect-color <=> r.background;
    // It's allowed to omit the type to have it automatically inferred
    in property rect-color2 <=> r.background;
    r:= Rectangle {
        width: parent.width;
        height: parent.height;
        background: blue;
    }
}
```

## 相对长度
有时用相对百分比来表示长度属性之间的关系是很方便的。例如，下面的内部蓝色矩形的大小是外部绿色窗口的一半
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    background: green;
    Rectangle {
        background: blue;
        width: parent.width * 50%;
        height: parent.height * 50%;
    }
}
```
这种以百分比表示父元素同名属性的宽度或高度的模式很常见。为了方便起见，这里有一种简写的语法:
- 属性是`width`或者`height`
- 绑定的表达式求值是百分比

如果满足这些条件，那么就没有必要指定parent属性，而是可以简单地使用百分比。前面的例子如下所示:
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    background: green;
    Rectangle {
        background: blue;
        width: 50%;
        height: 50%;
    }
}
```

## 示例
```Slint
import { LineEdit, Button } from "std-widgets.slint";  
  
component InputWithLabel inherits HorizontalLayout{  
    in property <string> label_str;  
    in-out property <string> intput_src <=>  input.text;  
    spacing: 2px;  
    Rectangle {  
        border-color: grey;  
        border-width: 2px;  
        border-radius: 5px;  
        Text{  
            text: label-str;  
            font-size: 14px;  
        }  
    }  
    Rectangle {  
        border-color: grey;  
        border-width: 2px;  
        border-radius: 5px;  
        input := LineEdit {  
            font-size: 14px;  
            input-type: number;  
        }  
  
    }  
}  
  
export component MainWindow inherits Window {  
    preferred-height: 480px;  
    preferred-width: 640px;  
          
    VerticalLayout {  
        spacing: 2px;  
        alignment: start;  
        input1 := InputWithLabel {  
            label-str: "number 1";  
        }  
        input2 := InputWithLabel {  
            label-str: "number 2";  
        }  
  
        VerticalLayout {  
            Button {  
                text: "sum";  
                clicked => {  
                    result.text = add(input1.intput_src.to-float(), input2.intput_src.to-float());  
                }  
            }  
            result := TextInput{  
                enabled: false;  
            }  
        }  
    }  
  
    pure function add(a: int, b: int) -> int {  
        return a + b;  
    }  
}
```