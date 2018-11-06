---
layout: post
title:  "MySql学习笔记（1）"
date:   2015-11-16 22:00:00
tags: MySql
---

## 开始使用MySql: 

Linux下启动mysql服务器：
<pre><code> 
$ sudo service mysql start
</code></pre>
这里我们使用root用户连接服务器（密码环境设置为空，无需我们输入密码）,输入以下命令连接服务器：  
<pre><code> 
$ mysql -u root
</code></pre>
成功地连接后，可以在mysql>提示下输入QUIT (或\q)随时退出。  
当显示'>和">时，要结束输入'\c。  
使用SHOW语句找出服务器上当前存在什么数据库：
<pre><code> 
mysql> SHOW DATABASES;
</code></pre> 
创建数据库：
<pre><code> 
mysql> CREATE DATABASE test;
</code></pre> 
创建数据库并不表示选定并使用它，你必须明确地操作。为了使test成为当前的数据库，使用这个命令：  
<pre><code> 
mysql> USE test
</code></pre> 

 


---

## 创建表:

使用一个CREATE TABLE语句指定你的数据库表的布局：
<pre><code> 
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
	-> species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
</code></pre> 
创建了数据库表后，SHOW TABLES应该产生一些输出：
<pre><code> 
mysql> SHOW TABLES;
</code></pre> 
为了验证你的表是按你期望的方式创建，使用一个DESCRIBE语句：
<pre><code> 
mysql> DESCRIBE pet;
</code></pre> 

---

## 从表检索信息： 

从表中检索所有记录的最简单方式是使用SELECT语句：
<pre><code> 
mysql> SELECT * FROM pet;
</code></pre> 
用一个UPDATE语句就可以修正错误记录：
<pre><code> 
mysql> UPDATE pet SET birth = '1989-08-31' WHERE name = 'Bowser';
</code></pre>
从表中只选择特定的行：
<pre><code> 
mysql> SELECT * FROM pet WHERE name = 'Bowser';
mysql> SELECT * FROM pet WHERE birth > '1998-1-1';
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f';
mysql> SELECT * FROM pet WHERE species = 'snake' OR species = 'bird';
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm')
	-> OR (species = 'dog' AND sex = 'f');
</code></pre> 
选择特殊列：  
<pre><code> 
mysql> SELECT name, birth FROM pet;
mysql> SELECT owner FROM pet;
</code></pre> 
行分类：
升序
<pre><code> 
mysql> SELECT name, birth FROM pet ORDER BY birth;
</code></pre> 
降序 
<pre><code> 
mysql> SELECT name, birth FROM pet ORDER BY birth DESC;
</code></pre> 

---

## 日期计算：  

<pre><code> 
mysql> SELECT name, birth, CURDATE(),
	-> TIMESTAMPDIFF(YEAR,birth,CURDATE()) AS age
	-> FROM pet;

---

# 比较这两个查询语句的结果

mysql> SELECT name, birth, CURDATE(),
	-> (YEAR(CURDATE())-YEAR(birth))
</code></pre> 

---

## NULL值操作：  

不能通过NULL比较得到有意义的结果，因为任何使用算数比较操作符对NULL进行比较的结果都是NULL。因此使用IS NULL和IS NOT NULL操作符：  
<pre><code> 
mysql> SELECT 1 IS NULL, 1 IS NOT NULL;
mysql> SELECT 0 IS NULL, 0 IS NOT NULL, '' IS NULL, '' IS NOT NULL;
</code></pre>  

---

## 模式匹配:  
  
使用SQL模式时，不能使用=或!=；而应使用LIKE或NOT LIKE比较操作符。  
<pre><code> 
mysql> SELECT * FROM pet WHERE name LIKE 'b%';
mysql> SELECT * FROM pet WHERE name LIKE '%fy';
mysql> SELECT * FROM pet WHERE name LIKE '%w%';
mysql> SELECT * FROM pet WHERE name LIKE '_____';
</code></pre> 

---

## 计算行数  

<pre><code> 
mysql> SELECT COUNT(*) FROM pet;
mysql> SELECT owner, COUNT(*) FROM pet GROUP BY owner;
mysql> SELECT species, COUNT(*) FROM pet GROUP BY species;
mysql> SELECT species, sex, COUNT(*) FROM pet
	-> WHERE species = 'dog' OR species = 'cat'
	-> GROUP BY species, sex;
mysql> SELECT species, sex, COUNT(*) FROM pet
	-> WHERE sex IS NOT NULL
	-> GROUP BY species, sex;
</code></pre> 
---

## 参考资料

* [实验楼](https://www.shiyanlou.com/)

