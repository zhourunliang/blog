---
layout: post
title:  "PHP函数式编程"
date:   2019-04-02
tags: PHP
---

# 什么是函数式编程
- 与面向对象编程（Object-oriented programming）和过程式编程（Procedural programming）并列的编程范式。
- 最主要的特征是，函数是第一等公民。
- 强调将计算过程分解成可复用的函数，典型例子就是map方法和reduce方法组合而成 MapReduce 算法。
- 只有纯的、没有副作用的函数，才是合格的函数。

# PHP中的函数
例子：
```php
$grade = array(
   array('id' => 1, 'subject' => 'chinese', 'score' => 82),
   array('id' => 2, 'subject' => 'math', 'score' => 98),
   array('id' => 3, 'subject' => 'english', 'score' => 78),
);
```

## array_filter — 用回调函数过滤数组中的单元
```php
//获取成绩大于80分的学科
$arrayFilter = array_filter($grade, function($item){
    return $item['score']  > 80 ;
});
```

## array_map() - 为数组的每个元素应用回调函数
```php
// 不影响原数组，返回一个新数组
$arrayMap = array_map(function($item){
    return array(
        'id' => $item['id'],
        'subject' => $item['subject'],
        'score' => $item['score'],
        'grade' => $item['score'] >= 60 ? '合格' : '不合格',
    );
}, $grade);
```

## array_reduce() - 用回调函数迭代地将数组简化为单一的值
```php
// 求分数最高的科目,返回最大科目信息
$maxScore = array_reduce($grade, function($init, $val){
    return $init['score'] > $val['score'] ? $init : $val;
}, array('score' => 0));
 
// 求平均成绩
$avgScore = array_reduce($grade, function($init, $item){
    return $init + $item['score'];
}, 0) / count($grade);
```

## array_walk() - 使用用户自定义函数对数组中的每个元素做回调处理
```php 
// 修改原数组
array_walk($grade, function(&$item, $index){
    $item['grade'] = $item['score'] >= 60 ? '合格' : '不合格';
});
```


# 参考资料
* [函数式编程入门教程](http://www.ruanyifeng.com/blog/2017/02/fp-tutorial.html)
* [PHP手册](https://www.php.net/manual/zh/index.php)
