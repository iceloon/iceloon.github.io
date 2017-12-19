---
layout: post
title: Oracle跨库定时增量更新
description: Windows定时任务 dblink merge into 批处理
category: blog
---

##DBLINK

Oracle dblink 是干啥的：比如现在有俩个数据库，都是单独的，如果我们现在登陆当前数据库orcl，想访问另一个数据库orcl2，如果我们建了DBLINK我们就可以直接在当前数据库取访问另一个数据库。 如select * from user@orcl2 这样我们就可以直接访问另一个数据库的user表。dblink是单向的。

**语法：**

```sql
CREATE [SHARED][PUBLIC] database link link_name

      [CONNECT TO [user][current_user] IDENTIFIED BY password]
      [AUTHENTICATED BY user IDENTIFIED BY password]
      [USING 'connect_string']
```

**建立database link之前需要确认的事项：**

​	确认从local database到remote database的网络连接是正常的，tnsping要能成功。
​	确认在remote database上面有相应的访问权限。

```sql
Create public database link dblink_name
Connect to zhanghao identified by "mima" using 'xxx.xxx.x.xx:端口/服务';
```

测试：

```sql
select * from dual@dblink_name;
```

##MERGE INTO

MERGE INTO能实现在Oracle中用一条SQL语句直接进行Insert/Update操作。

MERGE语句是Oracle9i新增的语法，用来合并UPDATE和INSERT语句。

通过MERGE语句，根据一张表或子查询的连接条件对另外一张表进行查询，

连接条件匹配上的进行UPDATE，无法匹配的执行INSERT。

这个语法仅需要一次全表扫描就完成了全部工作，执行效率高高于INSERT+UPDATE。

**语法：**

```SQL
MERGE INTO table_name alias1 
USING (table|view|sub_query) alias2
ON (join condition) 
WHEN MATCHED THEN 
    UPDATE table_name 
    SET col1 = col_val1, 
        col2 = col2_val 
WHEN NOT MATCHED THEN 
    INSERT (column_list) VALUES (column_values); 
```

## **Windows计划任务程序**

![](/images/copy.png))
