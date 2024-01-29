---
title: Slint 条件元素
date: 2024-01-13 13:43:59
tags:
  - Slint
---
# Slint 条件元素
仅当给定条件为 true 时，if 构造才会实例化元素。语法为 if 条件 : id := Element { ... }
***（这东西让我有一种看rust里 if let的感觉）***

```Slint
export component Example inherits Window {
    preferred-width: 50px;
    preferred-height: 50px;
    if area.pressed : foo := Rectangle { background: blue; }
    if !area.pressed : Rectangle { background: red; }
    area := TouchArea {}
}
```

推荐if语句从右往左读，然后拆一下紧密布局的代码
```Slint
export component Example inherits Window {
    preferred-width: 50px;
    preferred-height: 50px;


    if area.pressed : foo := Rectangle { 
	    background: blue; 
    }
    
    if !area.pressed : Rectangle { 
	    background: red; 
	    }
    area := TouchArea {}
}
```
效果就是有一个红色的矩形，点击的时候就成了蓝色，markdown里展示效果不太理想，这里贴上原文链接
https://slint.dev/releases/1.3.2/docs/slint/src/language/syntax/conditions
