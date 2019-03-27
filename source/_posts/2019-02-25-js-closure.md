---
layout: post
title:  "JavaScript中的闭包"
date:   2019-02-25
tags: JavaScript
---

# JavaScript中的闭包
各种专业文献上的"闭包"（closure）定义非常抽象，很难看懂。我的理解是，闭包就是能够读取其他函数内部变量的函数。
由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。
所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

## 闭包的例子
```javascript
    // 在函数定义的外面加上圆括号就可以直接调用
    var a = (function() {
    var _foo = 1
    return {
        // 返回的这个 object 中, 有 get 和 set 两个函数
        // 这两个函数都引用了 _foo 变量
        // 所以 _foo 变量不会消失, 但别人也访问不到, 这就是所谓的闭包
        get: function(){
        return _foo
        },
        set: function(v){
        _foo = v
        }
    }
    })()

    console.log('闭包 get', a.get())
    a.set('2')
    console.log('闭包 get', a.get())
```

## 参考资料

* [学习Javascript闭包（Closure） - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)