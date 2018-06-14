---
title: 拼接多个字段的值
date: 2018-06-15 01:48:48
tags: 
    - 字符串函数
categories: 数据库
---

### GROUP_CONCAT（）函数
> GROUP_CONCAT函数返回一个字符串结果，该结果由分组中的值连接组合而成。

```sql
select stylist_id,real_name,group_concat(is_show,m2_stylist_show_product.id,pic_url) as show_id_pic_url,max(is_show) as is_show
```

### CONCAT（）函数
> CONCAT（）函数用于将多个字符串连接成一个字符串。

### +号连接

```sql
select studentID+‘-’+studentName+'-'+studentScore AS studentInfo from student
```
<!-- more -->
### stuff()
> 将param1中自startIndex(SQL中都是从1开始，而非0)起，删除length个字符，然后用param2替换删掉的字符。

`stuff(param1, startIndex, length, param2)`
```php
select stuff((select '|'+studentName from student for xml path ('')),1,1,'');
```

- 参数
	- `param1`：一个字符数据表达式。param1可以是常量、变量，也可以是字符列或二进制数据列。
	- `startIndex`：一个整数值，指定删除和插入的开始位置。如果 startIndex或 length 为负，则返回空字符串。如果startIndex比param1长，则返回空字符串。startIndex可以是 bigint 类型。
	- `length`：一个整数，指定要删除的字符数。如果 length 比param1长，则最多删除到param1 中的最后一个字符。length 可以是 bigint 类型。
	
- 返回类型

	如果param1是受支持的字符数据类型，则返回字符数据。如果param1是一个受支持的 binary 数据类型，则返回二进制数据。
	
- 备注

	如果结果值大于返回类型支持的最大值，则产生错误。
	
	`for xml path`有的人可能知道有的人可能不知道，其实它就是将查询结果集以XML形式展现，有了它我们可以简化我们的查询语句实现一些以前可能需要借助函数活存储过程来完成的工作。
   
  
## posgreSQL

- 多字段值根据连接符拼接 `concat_ws(':',col_1,col_2)`
- 单字段值根据连接符拼接 `string_agg(col_1,' \r\n ')`
- 如果要将多个字段的值拼接成一个：`string_agg(concat_ws(':',aaa,bbb),' \r\n ' order by aaa asc) as xxx`