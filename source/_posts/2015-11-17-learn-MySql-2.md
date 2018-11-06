---
layout: post
title:  "MySql学习笔记（2）"
date:   2015-11-17 20:00:00
tags: MySql
---

## 查询某列最大值所在的行：
显示price这一列最大值对应的行：  
<pre><code> 
mysql> SELECT article, dealer, price
    -> FROM   shop
    -> WHERE  price=(SELECT MAX(price) FROM shop);  
</code></pre>	
另外一个方法是对所有行进行价格的降序排序，然后使用MySQL特有的LIMIT子句显示其中一行（这里是指定第一行显示最高价格）： 
<pre><code> 
mysql> SELECT article, dealer, price
    -> FROM shop
    -> ORDER BY price DESC
    -> LIMIT 1;
</code></pre>	
按组显示列的最大值： 
<pre><code>
mysql> SELECT article, MAX(price) AS price
    -> FROM shop
    -> GROUP BY article;
</code></pre>	
通过使用用户变量找出价格最高或者最低的物品：  
<pre><code>
mysql> SELECT @min_price:=MIN(price),@max_price:=MAX(price) FROM shop;
mysql> SELECT * FROM shop WHERE price=@min_price OR price=@max_price;
</code></pre>  
---	

## 字符串：  

字符串指用单引号(‘'’)或双引号(‘"’)引用起来的字符序列。  
如果SQL服务器模式启用了NSI_QUOTES，可以只用单引号引用字符串，此时用双引号引用的字符串被解释为一个识别符。
    
---

## 十六进制:  

十六进制值的默认类型是字符串。如果想要确保该值能作为数字处理，可以使用CAST(...AS UNSIGNED)：  
<pre><code>
mysql> SELECT 0x41，CAST(0x41 AS UNSIGNED)；
    -> 'A'，65
</code></pre>

---

## NULL值:  

NULL值表示“没有数据”。NULL可以任意大小写。
请注意NULL值不同于数字类型的0或字符串类型的空字符串。
大多数Unix中数据库名和表名对大小写敏感，而在Windows中对大小写不敏感。  
  
--- 

## 用户自定义变量：  

可以先在用户自己定义的变量中存储一个值然后在后面引用它；这样可以将该值从一个语句传递到另一个语句。用户自定义变量具有会话专一性，也就是说，一个客户端定义的变量不能被其它客户端看到或使用，当客户端退出时，该客户端连接的所有变量将自动释放。
设置用户变量的一个途径是执行SET语句：  
<pre><code>
SET @var_name = expr [, @var_name = expr] ...
</code></pre>  

---

## 注释语句：  

MySQL服务器支持3种注释风格：  

1.以#字符开始到行尾。  
2.以--序列开始到行尾。请注意--(双破折号)注释风格要求第2个破折号后面至少跟一个空格符(例如空格、tab、换行符等等)。该语法与标准SQL注释语法稍有不同。  
3.以/*序列开始到*/序列结束。结束序列不一定在同一行中，因此该语句允许注释跨越多行。    

---

## 参考资料

* [实验楼](https://www.shiyanlou.com/)

