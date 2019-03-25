---
layout: post
title:  "MySQL存储过程和函数"
date:   2019-03-25
tags: MySQL
---

# 存储过程和函数的区别
- 函数只能通过return语句返回单个值或者表对象。而存储过程不允许执行return，但是通过out参数返回多个值。 函数是可以嵌入在sql中使用的，可以在select中调用，而存储过程不行。
- 函数限制比较多，比如不能用临时表，只能用表变量．还有一些函数都不可用等等．而存储过程的限制相对就比较少
- 一般来说，存储过程实现的功能要复杂一点，而函数的实现的功能针对性比较强。

# procedure cache
- 当存储过程和函数被执行的时候，SQL Manager会到procedure cache中去取相应的查询语句，如果在procedure cache里没有相应的查询语句，SQL Manager就会对存储过程和函数进行编译。

- Procedure cache中保存的是执行计划 (execution plan) ，当编译好之后就执行procedure cache中的execution plan，之后SQL SERVER会根据每个execution plan的实际情况来考虑是否要在cache中保存这个plan，评判的标准一个是这个execution plan可能被使用的频率；其次是生成这个plan的代价，也就是编译的耗时。保存在cache中的plan在下次执行时就不用再编译了

# 优点
- 存储过程能实现较快的执行速度
- 存储过程允许标准组件是编程
- 存储过程可以用流控制语句编写，有很强的灵活性，可以完成复杂的判断和较复杂的运算
- 存储过程可被作为一种安全机制来充分利用
- 存储过程能过减少网络流量

# 存储过程
## 语法
```sql
    create procedure 存储过程名字() 
    ( 
        [in|out|inout] 参数 datatype 
    ) 
    begin 
        MySQL 语句; 
    end; 
```

## 例子
```sql
    drop procedure if exists pr_add; -- 如果这前存在就删除
    -- 计算两个数之和
    create PROCEDURE pr_add
    (
        in a int,
        in b int
    )
    begin
        declare c int; -- declare是声明变量的关键字
        if a is null THEN
            set a=1;
        end if;
        if b is null THEN  
            set b=1;
        end if;
        set c=a+b;
        select c as sum;
    end;

    call pr_add( null,null ); -- 这是调用存储过程pr_add
```
## 使用存储过程插入1千万条数据
```sql
    create procedure myproc()
    begin   
        declare num int;   
        set num=1;   
        while num <= 10000000 do   
            insert into test_user(username,email,password) values(CONCAT('username_',num), CONCAT(num ,'@qq.com'), MD5(num));   
            set num=num+1;  
        end while;  
    end;
    call myproc();
```

## 插入学生数据
```sql
    DELIMITER $$
    DROP PROCEDURE IF EXISTS `proc_auto_insertdata`$$
    CREATE PROCEDURE `proc_auto_insertdata`()
    BEGIN
            
            DECLARE init_data INTEGER DEFAULT 1;
        
            WHILE init_data <= 5000000 DO 
            
            INSERT INTO student VALUES(init_data, CONCAT('测试', init_data), 1);
            
            SET init_data = init_data + 1;
            
            END WHILE; 
    END$$
    DELIMITER ;
    CALL proc_auto_insertdata();
```

## 插入关系数据
```sql
    DELIMITER $$
    DROP PROCEDURE IF EXISTS `proc_auto_insertdata`$$
    CREATE PROCEDURE `proc_auto_insertdata`()
    BEGIN
            
            DECLARE init_data INTEGER DEFAULT 1;
        
            WHILE init_data <= 2000000 DO 
            
            INSERT INTO student_subject_relation VALUES(init_data, FLOOR(1 + (RAND() * 5000000)), FLOOR(1 + (RAND() * 3)), 1);
            
            SET init_data = init_data + 1;
            
            END WHILE; 
    END$$
    DELIMITER ;
    CALL proc_auto_insertdata();
```


# 存储函数
1. 存储方法的参数列表只允许IN类型的参数，而且没必要也不允许指定IN关键字 
2. 存储方法返回一个单一的值，值的类型在存储方法的头部定义 
3. 存储方法可以在SQL语句内部调用 
4. 存储方法不能返回结果集 

## 语法
```sql
　create function 函数([函数参数[,….]]) Returns 返回类型
　　Begin
　　　　If
　　　　　　Return (返回的数据)
　　　　Else 
　　　　　　Return (返回的数据)
　　　　end if;
　　end;
```

## 例子
```sql
    drop function if exists purchase_and_redeem_function;
    CREATE  function  purchase_and_redeem_function(date int)  
            returns varchar(80)
            BEGIN  
                return (SELECT  tbalance    
                FROM  user_purchase_and_redeem  
                WHERE  report_date=date);  -- 这里面的SQL语句根据自己数据库表编写   
            END;

    select  purchase_and_redeem_function(20190325); --这是调用存储函数
```

# 触发器 
- 触发器在INSERT、UPDATE或DELETE等DML语句修改数据库表时触发 
- 触发器的典型应用场景是重要的业务逻辑、提高性能、监控表的修改等 
- 触发器可以在DML语句执行前或后触发
## 例子
```sql
    DROP TRIGGER sales_trigger;
    CREATE TRIGGER sales_trigger   
        BEFORE INSERT ON sales   
        FOR EACH ROW   
    BEGIN   
        IF NEW.sale_value > 500 THEN   
            SET NEW.free_shipping = 'Y';   
        ELSE   
            SET NEW.free_shipping = 'N';   
        END IF;   

        IF NEW.sale_value > 1000 THEN   
            SET NEW.discount = NEW.sale_value * .15;   
        ELSE   
            SET NEW.discount = 0;   
        END IF;   
    END;   
```