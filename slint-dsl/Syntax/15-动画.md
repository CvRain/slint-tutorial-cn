---
title: Slint 动画效果
date: 2024-01-14 12:09:22
tags:
  - Slint
---
# Slint 动画效果
定义具有动画效果的属性需要使用`animate`关键字，就像这样：
```Slint
export component Example inherits Window {
    preferred-width: 100px;
    preferred-height: 100px;

    background: area.pressed ? blue : red;
    animate background {
        duration: 250ms;
    }

    area := TouchArea {}
}
```
效果是当鼠标点击窗口，背景颜色会从红色变成蓝色名，变化的过程为250毫秒

使用以下参数微调动画：
- `delay`：在动画开始前的等待时间。
- `duration`：动画完成所需要用到的时间。
- `iteration-count`：动画应运行的次数。负值指定无限重新运行。分形值是可能的。对于永久运行的动画，请参阅[`animation-tick()`](https://slint.dev/releases/1.3.2/docs/slint/src/language/builtins/functions.html#animation-tick-duration)。
- `easing`：可以是下方任意一个，从[`easings.net`](https://easings.net/)网站可以查看视觉效果。
	- `linear`
	- `ease-in-quad`
	- `ease-out-quad`
	- `ease-in-out-quad`
	- `ease`
	- `ease-in`
	- `ease-out`
	- `ease-in-out`    
	- `ease-in-quart`	    
	- `ease-out-quart`	    
	- `ease-in-out-quart`	    
	- `ease-in-quint`	    
	- `ease-out-quint`	    
	- `ease-in-out-quint`	    
	- `ease-in-expo`	    
	- `ease-out-expo`	    
	- `ease-in-out-expo`	    
	- `ease-in-sine`	    
	- `ease-out-sine`	    
	- `ease-in-out-sine`	    
	- `ease-in-back`	    
	- `ease-out-back`	    
	- `ease-in-out-back`	    
	- `ease-in-circ`	    
	- `ease-out-circ`	    
	- `ease-in-out-circ`	    
	- `ease-in-elastic`	    
	- `ease-out-elastic`	    
	- `ease-in-out-elastic`	    
	- `ease-in-bounce`	    
	- `ease-out-bounce`	    
	- `ease-in-out-bounce`	    
	- `cubic-bezier(a, b, c, d)`

还可以在图库示例的“缓动”选项卡上找到缓动示例。

---
还可以使用相同的动画为多个属性设置动画，比如这样：
`animate x, y { duration: 100ms; easing: ease-out-bounce; }`

这样编写也可以达到一样的效果：
```Slint
animate x { duration: 100ms; easing: ease-out-bounce; }
animate y { duration: 100ms; easing: ease-out-bounce; }
```