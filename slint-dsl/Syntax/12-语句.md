---
title: Slint 语句（statements）
date: 2024-01-13 10:36:05
tags:
  - Slint
---
# Slint Statements
在编程中，表达式是值、变量、操作符和函数调用的组合，其计算结果为单个值。例如，' 2 + 3 '是一个求值为' 5 '的表达式。表达式可以用作较大表达式的一部分，也可以用作函数的参数。

另一方面，语句是执行一个操作的完整代码行。语句可以包括表达式，但也可以包括关键字、循环、条件和其他编程结构。例如，' if (x > 0) {y = 1;}是一个检查' x '是否大于' 0 '的语句，如果大于' 0 '，则将' y '设置为' 1 '。

表达式和语句之间的主要区别在于表达式的计算结果是一个值，而语句执行一个操作。表达式可以用作语句的一部分，但语句不能用作表达式的一部分。

---

回调函数能够包含复杂的语句：
赋值：`clicked => { some-property = 42; }`

自赋值运算：`clicked => { some-property += 42; }`

调用一个回调函数：`clicked => { root.some-callback(); }`

条件语句：
```Slint
clicked => {
    if (condition) {
        foo = 42;
    } else if (other-condition) {
        bar = 28;
    } else {
        foo = 4;
    }
}
```

空表达式：
```Slint
clicked => {}
clicked => {;}
```