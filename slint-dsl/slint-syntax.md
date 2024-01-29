---
title: Slint DSL 类型
date: 2024-01-10 13:58:41
tags:
  - Slint
---
# 注释
Slint支持C类风格的注释：
1. 使用两个斜杠(`//`) 的方式注释此行中在//之后的所有内容
2. 块注释(`/* ... */ `) ， 注释处于`/*` 到 `*/`之间的所有内容
例：
```Slint
component MyRectangle inherits Rectangle{  // 这是一个注释
  /*
	这是一个注释
	这也是一个注释
  */
}
```

# 标识符
标识符可以由字母 `(a-zA-Z)`、数字` (0-9)`、下划线` (_) `或破折号 `(-)` 组成。它们**不能以数字或破折号开头**（但可以以下划线开头）下划线被标准化为破折号。这意味着这两个标识符是相同的：foo_bar 和 foo-bar。
```Slint
hello_world  // ok
hello-world  // ok
helloWorld   // ok
HelloWorld   // ok
_hello_world // ok
123Apple     // error
-apple       // error
```

# 类型
在Slint中所有属性都是具有类型的，下表展示了Slint中的基本类型：

|类型 |描述 |默认值 |
|---|---|---|
|`angle`|角度单位，对应于文字如 90deg、1.2rad、0.25turn |0deg|
|`bool`|布尔值，`true`和`false` |false|
|`brush`|画笔是一种特殊类型，可以从颜色或渐变规范初始化。有关详细信息，请参阅颜色和画笔部分。 [Colors and Brushes Section](https://slint.dev/releases/1.3.2/docs/slint/src/language/syntax/types#colors-and-brushes)  |transparent|
|`color`|具有 Alpha 通道的 RGB 颜色，每个通道具有 8 位精度。支持 CSS 颜色名称以及十六进制颜色编码，例如 `#RRGGBBAA` 或` #RGB`。 |transparent|
|`duration`|输入动画的持续时间。 ms（毫秒）或 s（秒）等后缀用于指示精度。 |0ms|
|`easing`|属性动画允许指定缓动曲线。有效值是线性的（值是线性插值的），CSS 中已知四个常见的立方贝塞尔函数：`ease`、`ease_in`、`ease_in_out`、`ease_out`。<br>[four common cubiz-bezier functions known from CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/easing-function#Keywords_for_common_cubic-bezier_easing_functions) |linear|
|`float`|有符号的 32 位浮点数。带有 % 后缀的数字会自动除以 100，因此例如 30% 等于 0.30。 |0|
|`image`|对图像的引用可以使用 @image-url("...") 构造进行初始化 |empty image|
|`int`|带符号的整数。 |0|
|`length`|用于 x、y、宽度和高度坐标的类型。对应于 1px、1pt、1in、1mm 或 1cm 等文字。如果绑定在可以访问设备像素比的上下文中运行，则可以将其与长度进行转换。 |0px|
|`percent`|有符号的 32 位浮点数，可以理解为百分比。分配给该类型属性的文字数字必须带有 % 后缀。 |0%|
|`physical-length`|这是物理像素的数量。要将整数转换为长度单位，只需乘以 1px 即可。或者要将长度转换为浮点数，可以除以 1phx。 |0phx|
|`relative-font-size`|相对字体大小系数，与 `Window.default-font-size` 相乘，可以转换为长度。 |0rem|
|`string`|UTF-8 编码、引用计数字符串。 |`""`|

## 字符串
任何一个使用utf8编码字符组成的系列都可以称为一个字符串(`string`) 比如："foo"
转义序列可以嵌入到字符串中以插入难以直接插入的字符（就是很难直接打出来的字符）：

| 转移字符 | 结果 |
| :--- | :--- |
| `\"` | `"` |
| `\\` | `\` |
| `\n` | 换到新一行 |
| `\u{x}` | 其中 x 是一个十六进制数字，扩展为该数字表示的 unicode 代码点 |
| `\{expression}` | 表达式的求值结果 |
**非转义字符 \ 后面的任何其他内容都是错误。**

```Slint
export component Example inherits Text {
    text: "hello";
}
```
**注意** 这种`\{...}`的语法在Rust语言中的`slint!`宏中使用是无效的

## 颜色和笔刷（画笔）
颜色文字遵循 CSS 语法：
```Slint
export component Example inherits Window {
    background: blue;
    property<color> c1: #ffaaff;
    property<brush> b2: Colors.red;
}
```
除了纯色之外，许多元素都有类型为brush而不是color的属性。画笔是一种可以是颜色也可以是渐变的类型。然后使用画笔填充元素或绘制轮廓。

CSS颜色名称仅在类型Color或brush表达式的作用域内。否则，您可以从colors名称空间访问颜色。

### 方法
所以的`colors`和`brushed`遵从以下方法的定义：

**`brighter(factor: float) -> brush`**
返回从该颜色派生的新颜色，但其亮度增加指定的因子。例如，如果因子为0.5(或例如50%)，则返回的颜色要亮50%。负面因素会降低亮度。

**`darker(factor: float) -> brush`**
返回从该颜色派生的新颜色，但其亮度降低指定的因子。例如，如果因子为0.5(或例如50%)，则返回的颜色暗50%。负面因素增加了亮度。

**`mix(other: brush, factor: float) -> brush`**
返回一种新颜色，它是这种颜色和其他颜色的混合，混合的比例由参数`factor`决定(将被限制在0.0和1.0之间)。

**`transparentize(factor: float) -> brush`**
返回不透明度降低的新颜色。通过将alpha通道乘以(1 -`factor`)获得透明度。

**`with_alpha(alpha: float) -> brush`**
返回一个新的颜色，alpha值设置为alpha(介于0和1之间)

### 线性渐变
线性梯度描述光滑、彩色的表面。它们是用一个角度和一系列颜色点来指定的。颜色将在止光点之间线性内插，与以指定角度旋转的假想线对齐。这被称为线性梯度，并使用带有以下签名的`@linear-gradient`宏指定:
**`@linear-gradient(angle, color percentage, color percentage, ...)`**

宏的第一个参数是一个角度(参见类型)。梯度线的起始点将以指定的值旋转。

在初始角度之后是一个或多个颜色停止，描述为一个颜色值和百分比的空格分隔对。颜色指定线性颜色插值在沿梯度轴的指定百分比处应达到的值。

下面的例子显示了一个矩形，填充了一个线性渐变，以浅蓝色开始，在中心插入一个非常浅的阴影，并以橙色色调结束:
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    Rectangle {
        background: @linear-gradient(90deg, #3f87a6 0%, #ebf8e1 50%, #f69d3c 100%);
    }
}
```
### 径向渐变
线性渐变就像真正的渐变，但颜色是在一个圆内插入而不是沿着一条线。要描述一个径向渐变，使用以下签名`@radial-gradient`宏：
**`@radial-gradient(circle, color percentage, color percentage， ...) `**
该宏的第一个参数总是circle，因为只支持圆形渐变。
语法是基于CSS `radial-gradient`函数。
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;
    Rectangle {
        background: @radial-gradient(circle, #f00 0%, #0f0 50%, #00f 100%);
    }
}
```

## 图片
图像类型是对图像的引用。它是使用 `@image-url("...")` 构造定义的。` @image-url` 函数中的地址必须在编译时已知。
Slint 在以下位置查找图像：
- 绝对路径或相对于当前 .slint 文件的路径。
- 编译器用来查找 .slint 文件的包含路径。

使用图像的宽度和高度属性访问图像的尺寸。
```Slint
export component Example inherits Window {
    preferred-width: 150px;
    preferred-height: 50px;

    in property <image> some_image: @image-url("https://slint.dev/logo/slint-logo-full-light.svg");

    Text {
        text: "The image is " + some_image.width + "x" + some_image.height;
    }
}
```

## 结构体
使用关键字`struct`来定义结构体
```Slint
export struct Player  {
    name: string,
    score: int,
}

export component Example {
    in-out property<Player> player: { name: "Foo", score: 100 };
}
```
结构体的默认值是通过将其所有字段设置为其默认值来初始化的。

### 匿名结构体
使用 `{identifier1: type2,identifier1:type2 } `语法声明匿名结构，并使用 `{identifier1: expression1,identifier2: expression2 } `对其进行初始化。
```Slint
export component Example {
    in-out property<{name: string, score: int}> player: { name: "Foo", score: 100 };
    in-out property<{a: int, }> foo: { a: 3 };
}
```

## 枚举类型
使用关键字`enum`定义枚举类型
```Slint
export enum CardSuit { clubs, diamonds, hearts, spade }

export component Example {
    in-out property<CardSuit> card: spade;
    out property<bool> is-clubs: card == CardSuit.clubs;
}
```
枚举值可以通过使用枚举的名称和值的名称，中间用点分开来引用。 (例如：CardSuit.spade)
 在该枚举类型的绑定中，或者如果回调函数的返回值是该枚举，则可以省略枚举的名称。
 每个枚举类型的默认值总是第一个值。

## 数组和模型  
  
数组是通过将数组元素的类型括在` [` 和 `]` 方括号中来声明的。  
  
数组文字以及保存数组的属性充当 for 表达式中的模型。
```Slint
export component Example {
    in-out property<[int]> list-of-int: [1,2,3];
    in-out property<[{a: int, b: string}]> list-of-structs: [{ a: 1, b: "hello" }, {a: 2, b: "world"}];
}
```

数组定义了以下操作：  
`array.length`：可以使用内置的 .length 属性查询数组和模型的长度。  
`array[index]`：索引运算符检索数组的各个元素。
对数组的越界访问将返回默认构造的值。
```Slint
export component Example {
    in-out property<[int]> list-of-int: [1,2,3];

    out property <int> list-len: list-of-int.length;
    out property <int> first-int: list-of-int[0];
}
```

## 类型转换

Slint支持不同类型之间的转换。为了使UI描述更健壮，需要显式转换，但是为了方便，在某些类型之间允许隐式转换。

可以进行以下转换:
- Int可以隐式地转换为float，反之亦然
- Int和float可以隐式转换为string
- 物理长度和长度只能在像素比已知的情况下隐式地相互转换。
- 单位类型(长度、物理长度、持续时间等)不能转换为数字(float或int)，但它们可以自己除以得到一个数字。同样，一个数字可以乘以这些单位中的一个。这个想法是，一个人将乘以1px或除以1px来进行这样的转换
- 字面值0可以转换为具有相关单位的任何这些类型。
- 如果结构类型具有相同的属性名，并且它们的类型可以转换，那么它们将与另一个结构类型转换。源结构可以有缺失的属性，也可以有额外的属性。但不是两者都有。
- 数组通常不会相互转换。如果元素类型是可转换的，则可以转换数组字面值。
- 使用to-float函数可以将String转换为float。如果字符串不是一个有效的数字，该函数返回0。您可以使用is-float()检查字符串是否包含有效数字

```Slint
export component Example {
    // OK: int converts to string
    property<{a: string, b: int}> prop1: {a: 12, b: 12 };
    // OK: even if a is missing, it will just have the default value ("")
    property<{a: string, b: int}> prop2: { b: 12 };
    // OK: even if c is too many, it will be discarded
    property<{a: string, b: int}> prop3: { a: "x", b: 12, c: 42 };
    // ERROR: b is missing and c is extra, this doesn't compile, because it could be a typo.
    // property<{a: string, b: int}> prop4: { a: "x", c: 42 };

    property<string> xxx: "42.1";
    property<float> xxx1: xxx.to-float(); // 42.1
    property<bool> xxx2: xxx.is-float(); // true
}
```
