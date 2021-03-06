---
layout: post
title:  "数据结构和算法分析"
date:   2018-12-14
tags: 算法
---

# 数据结构和算法分析

## 算法的时间复杂度 
大 O 记法，是描述算法复杂度的符号
### O(1)
- 常数复杂度，最快速的算法。
- 取数组第 1000000 个元素
- 字典和集合的存取都是 O(1)
- 数组的存取是 O(1)

### O(logN)
- 对数复杂度
- 假设有一个有序数组，以二分法查找

### O(n)
- 线性复杂度
- 假设有一个数组，以遍历的方式在其中查找元素

### O(nlogn)
- 求两个数组交集，其中一个是有序数组
- A 数组每一个元素都要在 B 数组中进行查找操作
- 每次查找如果使用二分法则复杂度是 lgN

### O(N^2)
- 平方复杂度
- 求两个无序数组的交集

## 数据类型
```
# str
name = 'abc'    

# float
height = 1.78   

# int
age = 28       

#list
scores = [90, 99, 100, 100] 

# dict
student = {
    'name': 'run',
    'score': 99,
}   

# class
# 类是高级字典，本质还是字典
class Student():
    def __init__(self):
        self.name = ''
        self.score = -1

```

## 常用的数据结构
### 1，数组
    连续的一块内存
    存取元素时间是 O(1)
    插入、删除是 O(n)
### 2，链表
    手拉手的盒子，一个盒子只能访问左右手的盒子
    以下标方式读取元素的时间是 O(n)
    插入、删除是 O(1)
    栈和队列是链表的改良型
    栈是 先进后出
    队列是 先进先出
### 3，字典(哈希表 对象 Map)
    把字符串转为数字作为下标存储到数组中
    字符串转化为数字的算法是 O(1)
    所以字典的存取操作都是 O(1)
    除非对数据有顺序要求，否则字典永远是最佳选择
    字符串转化为数字的算法
        1，确定数据规模，这样可以确定容器数组的大小 Size
        2，把字符当作 N 进制数字得到结果
            'abc' 被视为 a * 1 + b * 10 + c * 100 得到结果 n
            n % Size 作为字符串在数组中的下标
            通常 Size 会选一个 素数
        3, 当下标冲突(冲突属于叫做碰撞)的时候, 我们有标准解决碰撞的办法
            如： 链接法
### 4，搜索树
