---
layout: post
title:  "MySql优化分析"
date:   2018-11-02 21:27:00
tags: MySql
---

# MySql优化分析
## 原理
-  MYSQL逻辑分层 ：连接层 服务层 引擎层 存储层
-  InnoDB(默认) ：事务优先 （适合高并发操作；行锁）
-  MyISAM ：性能优先  （表锁）
## SQL优化
- 编写过程：
        ```sql
			select dinstinct  ..from  ..join ..on ..where ..group by ...having ..order by ..limit ..
        ```


- 解析过程：
		```sql
			from .. on.. join ..where ..group by ....having ...select dinstinct ..order by limit ...
        ```


## 索引分类
- 主键索引  ： 不能重复。id    不能是null
- 唯一索引  ： 不能重复。id    可以是null
- 单值索引  ： 单列， age ;一个表可以多个单值索引,name。
- 复合索引  ： 多个列构成的索引 （相当于 二级目录 ：  z: zhao）  (name,age)   (a,b,c,d,...,n)
## 分析SQL
- 分析SQL的执行计划  : explain   ，可以模拟SQL优化器执行SQL语句，从而让开发人员 知道自己编写的SQL状况
- MySQL查询优化其会干扰我们的优化
- explain字段


| 参数           | 说明                |
| -------------  |:-------------------:|
| id             | 编号                |                    
| select_type    | 查询类型            |                    
| table          |  表                 |                    
| type           | 类型                |             
| possible_keys  | 预测用到的索引      |              
| key            | 实际使用的索引      |           
| key_len        | 实际使用索引的长度  | 
| ref            | 表之间的引用        | 
| rows           | 通过索引查询到的数据量 |   
| Extra          | 额外的信息              |   


    
- 例子数据
    ```sql
    create table course
    (
    cid int(3),
    cname varchar(20),
    tid int(3)
    );
    create table teacher
    (
    tid int(3),
    tname varchar(20),
    tcid int(3)
    );

    create table teacherCard
    (
    tcid int(3),
    tcdesc varchar(200)
    );

    insert into course values(1,'java',1);
    insert into course values(2,'html',1);
    insert into course values(3,'sql',2);
    insert into course values(4,'web',3);

    insert into teacher values(1,'tz',1);
    insert into teacher values(2,'tw',2);
    insert into teacher values(3,'tl',3);

    insert into teacherCard values(1,'tzdesc') ;
    insert into teacherCard values(2,'twdesc') ;
    insert into teacherCard values(3,'tldesc') ;
    ```


- id（编号）:

    - id值相同，从上往下 顺序执行
        
        ```sql
        --查询教授SQL课程的老师的描述（desc）
        explain select tc.tcdesc from teacherCard tc,course c,teacher t where c.tid = t.tid
        and t.tcid = tc.tcid and c.cname = 'sql' ;
        ```

    - id值不同：id值越大越优先查询 (本质：在嵌套子查询时，先查内层 再查外层)

         ```sql
        --将以上 多表查询 转为子查询形式：
        explain select tc.tcdesc from teacherCard tc where tc.tcid = 
        (select t.tcid from teacher t where  t.tid =  
            (select c.tid from course c where c.cname = 'sql')
        );
        ```

    - id值有相同，又有不同： id值越大越优先；id值相同，从上往下 顺序执行

         ```sql
        --子查询+多表：
        explain select t.tname ,tc.tcdesc from teacher t,teacherCard tc where t.tcid= tc.tcid
        and t.tid = (select c.tid from course c where cname = 'sql') ;
        ```

- select_type（查询类型）:

    - PRIMARY:包含子查询SQL中的 主查询 （最外层）
    - SUBQUERY：包含子查询SQL中的 子查询 （非最外层）
    - simple:简单查询（不包含子查询、union）
    - derived:衍生查询(使用到了临时表)
    - union:
        - a.在from子查询中只有一张表      
        ```sql
            explain select  cr.cname 	from ( select * from course where tid in (1,2) ) cr ;
        ```
        - b.在from子查询中， 如果有table1 union table2 ，则table1 就是derived,table2就是union
        ```sql
            explain select  cr.cname 	from ( select * from course where tid = 1  union select * from course where tid = 2 ) cr ;
        ```
    - union result :那些表之间存在union查询

- type（索引类型、类型）

    - 例子数据
    ```sql
        create table test01
        (
            tid int(3),
            tname varchar(20)
        );

        insert into test01 values(1,'a') ;
        commit;
        --增加索引
        alter table test01 add constraint tid_pk primary key(tid) ;
        explain select * from (select * from test01 )t where tid =1 ;
    ```
    - 执行效率：system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
    - 常用的类型：system>const>eq_ref>ref>range>index>all
    - 要对type进行优化的前提：有索引,其中：system,const只是理想情况；实际能达到 ref>range;
    - system（忽略）: 只有一条数据的系统表 ；或 衍生表只有一条数据的主查询
    - const:仅仅能查到一条数据的SQL ,用于Primary key 或unique索引  （类型 与索引类型有关）
    - eq_ref:唯一性索引：对于每个索引键的查询，返回匹配唯一行数据（有且只有1个，不能多 、不能0）
    - ref：非唯一性索引，对于每个索引键的查询，返回匹配的所有行（0，多）
    - range：检索指定范围的行 ,where后面是一个范围查询(between   ,> < >=,     特殊:in有时候会失效 ，从而转为 无索引all)
    - index：查询全部索引中数据
    - all：查询全部表中的数据 

- possible_keys

    - 可能用到的索引，是一种预测，不准
    - 如果 possible_key/key是NULL，则说明没用索引

- key

    - 实际使用到的索引

- key_len

    - 作用：用于判断复合索引是否被完全使用  （a,b,c）
- ref

    - 注意与type中的ref值区分，作用： 指明当前表所 参照的 字段

- rows

    - 被索引优化查询的 数据个数 (实际通过索引而查询到的 数据个数)
- Extra

    - using filesort ： 性能消耗大；需要“额外”的一次排序（查询）  。常见于 order by 语句中

## MySql最大连接数
mysql的最大连接数默认是100, 最大可以达到16384，可以通过max_connections设置

## 对MySQL语句性能优化的16条经验
- 为查询缓存优化查询
- EXPLAIN 我们的SELECT查询(可以查看执行的行数)
- 当只要一行数据时使用LIMIT 1
- 为搜索字段建立索引
- 在Join表的时候使用相当类型的列，并将其索引
- 千万不要 ORDER BY RAND  ()
- 避免SELECT *
- 永远为每张表设置一个ID
- 可以使用ENUM 而不要VARCHAR
- 尽可能的使用NOT NULL
- 固定长度的表会更快
- 垂直分割
- 拆分打的DELETE或INSERT语句
- 越小的列会越快
- 选择正确的存储引擎
- 小心 "永久链接"

## MyISAM和InnoDB比较
|                | MyISAM              |InnoDB               |
| -------------  |:-------------------:|:-------------------:|
| 事务           | 不支持              | 支持                |
| 数据行锁定     | 不支持，只有表锁定  | 支持                |
| 外键约束       | 不支持              | 支持                |
| 表空间大小     | 相对小              | 相对大              |
| 全文索引       | 支持                | 不支持              |
| 关注点         | 性能（select）      | 事务                |
| MVCC(多版本并发控制)| 不支持         | 支持                |
 




