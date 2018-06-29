---
title: Mysql数据库查询技巧
date: 2018-06-29 11:57:43
tags:
---


# 判断为空 和 不为空
```mysql
select  case when customer.pay_password is null then 0 else 1 end as hasPwd
```

# 时间比较大小 需要进行转义
```mysql
 <![CDATA[
  and shop.end_time >= (select now())
 ]]>
```

# 一对多关联查询
## 对主表进行分页问题
```mysql
select a.*,b.* from table_a a left join table_b b on (a.b_id = b.id) where a.type=1 limit 0,10;
```
本意 是查询主表的 10 条数据，并且查询出其关联的子数据；

但是假如 table_a表的一条数据关联了 table_b的3条数据

最终能查询出10条数据，但是有 三条 table_a 表数据是重复的

## 解决方案
```mysql
select a.*,b.* from (select * from table_a where type = 1 limit 0,10)a left join table_b b on(a.b_id=b.id)
```
此方案存在的问题是 当条件关联查询中有子表的条件存在问题。