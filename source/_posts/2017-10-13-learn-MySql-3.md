---
layout: post
title:  "常用MySQL语句"
date:   2017-10-13 20:59:00
tags: MySql
---

常用MySQL语句。  

---

## 多表查询：
```sql  
select  
a.name as `a_name`,
b.name as `b_brand`
FROM  table_a AS a
LEFT JOIN table_b AS b ON b.a_id=b.id

```

## 查每天单数与总额：
```sql  
SELECT
    DAY (FROM_UNIXTIME(a.time)) AS `day`,
    sum(
        CASE
        WHEN a.order_status = 4 THEN
            1
        ELSE
            0
        END
    ) AS `order_num`,
    sum(
        CASE
        WHEN a.order_status = 4 THEN
            a.order_cost
        ELSE
            0
        END
    ) AS `cost_amount`,
FROM
    table_a AS a
WHERE
    a.time BETWEEN 1472659200 AND 1475251200
AND a.status = 1
GROUP BY
    DAY (FROM_UNIXTIME(a.time))
ORDER BY
    `day`

```

## 修改表内容：
```sql  
UPDATE table_order as o
SET o.type=3,o.status=1
WHERE o.type=0 OR o.type=null

```


## 多表联合修改内容：
```sql  
UPDATE table_a as a
INNER JOIN table_b as b
ON b.a_id=a.id
SET a.`name`=b.`name`
WHERE  b.`name` is not null
AND a.`name` is null

```

## 累加会员统计：
```sql  
SELECT
    a.`month` as `month`,
    a.new_user as `new_user`,
    sum(lt.new_user) as `user_amount`
FROM
    (SELECT
        month( FROM_UNIXTIME( u.created_time ) ) as `month`,
        sum(case when  u.status=1 then  1 else 0 end) as `new_user`
    FROM
        user AS u
    WHERE
        u.created_time BETWEEN 1451577600 AND 1471589599
    GROUP BY
        month) AS a
JOIN
(SELECT
  month( FROM_UNIXTIME( u.created_time ) ) as `month`,
  sum(case when  u.status=1 then  1 else 0 end) as `new_user`
FROM
    user AS u
WHERE
    u.created_time BETWEEN 1451577600 AND 1471589599
GROUP BY
    month) As lt ON a.`month`>=lt.`month`
GROUP BY `month`

```

## 清空表并重置自增值：
```sql  
delete from user;
ALTER TABLE `user` AUTO_INCREMENT=10000;

```

## 字符串补0：
```sql  
UPDATE user
set user.vipcode=LPAD(user.vipcode, 6, 0)

```

## 删除重复数据：
```sql  
DELETE FROM user WHERE uid in (
select uid from
  (
    SELECT
        *
    FROM
        user
    GROUP BY
        vipcode
    HAVING
        count(vipcode) > 1
    ) as user_tmp
)

```

## 查询重复内容：
```sql  
SELECT
od.order_id,
o.order_no,
count(*) as x
FROM order_delivery as od
LEFT JOIN order as o on o.id=od.order_id
WHERE `status`=1
GROUP BY od.order_id
HAVING count(*)>1

```

## 查数据表字段并以，隔开：
```sql  
SELECT
    GROUP_CONCAT(COLUMN_NAME SEPARATOR ",")
FROM
    information_schema. COLUMNS
WHERE
    TABLE_SCHEMA = 'Database'
AND TABLE_NAME = 'table'

```

## 向新数据表导入数据:
```sql
INSERT INTO user(
 	uid,name,`status`
 )
 select
   uid,name,`status`
 from old_user

 ```

## 检查重复数据:
```sql
SELECT c1.name,c1.code,f1.phone,f1.member_name,f1.role_name
FROM children as c1
LEFT JOIN family as f1 on c1.`code`=f1.children_code
WHERE 1<(
       SELECT count(*) 
        FROM children as c2  INNER JOIN family as f2 on f2.children_code=c2.code
        WHERE c2.`name`=c1.`name`
          AND c2.`code`<>c1.`code`  
          AND f2.`phone`=f1.`phone`
   )

 ```

## 横向统计:
```sql
SELECT
res.code as code,
res.`name` as name
-- 循环项目
max(
    CASE WHEN resd.item_id=1 
    THEN  resd.score
    ELSE 0
    END
    ) as score_1,
max(
    CASE WHEN resd.item_id=1 
    THEN  resd.point
    ELSE 0
    END
    ) as point_1

FROM
result as res 
LEFT JOIN result_detail as resd on resd.res_id=res.id AND res.`status`=1
WHERE res.`status`=1
GROUP BY res.code
ORDER BY code DESC 

 ```

