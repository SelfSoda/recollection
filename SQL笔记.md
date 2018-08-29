# SQL笔记

### SQL执行顺序

```sql
SELECT DISTINCT <select_list>
FROM <left_table>
<join_type> JOIN <right_table>
ON <join_condition>
WHERE <where_condition>
GROUP BY <group_by_list>
HAVING <having_condition>
ORDER BY <order_by_condition>
LIMIT <limit_number>
```

-----------

### 去重

表内容：

id	word	c1	c2	c3 ...

1	我		0	1	0 ...

2	我		0	1	0 ...

3	你		1	0	1 ...

```sql
SELECT *, COUNT(DISTINCT word)
FROM word_table
GROUP BY name
```

-----------

### 字符串连接

concat函数或concat_ws函数，concat_ws第一个参数是分隔符，其他参数两者一致。

concat中若出现null，则返回结果为null；

concat_ws会忽略null。

```sql
select concat_ws(':','1','2',NULL,NULL,NULL,'3') from test;

output:
+-------------------------------------------+  
| concat_ws(':','1','2',NULL,NULL,NULL,'3') |  
+-------------------------------------------+  
| 1:2:3 |  
+-------------------------------------------+
```

-----------

