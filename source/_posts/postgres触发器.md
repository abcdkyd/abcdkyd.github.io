---
title: postgres触发器
date: 2019-01-08 14:28:48
tags: 
    - Postgres
    - 触发器
categories: 数据库
---

###  触发器的创建语法

```
CREATE [CONSTRAINT] TRIGGER name 
{ BEFORE | AFTER | INSTEAD OF } { event [ OR ...] } 
ON table_name
[ FROM referenced_table_name ]
{[ NOT DEFERRABLE ][ DEFERRABLE ]{[ INITIALLY IMMEDIATE ][ INITIALLY DEFERRED]}} 
[ FOR [ EACH ] { ROW | STATEMENT } ]
[ WHEN (condition) ]
EXECUTE PROCEDURE function_name ( arguments )
```

### 触发器创建步骤

先为触发器创建一个执行函数，返回类型为触发器类型，然后创建相应的触发器

```
create or replace function trigger_loans_tmp_del() returns TRIGGER as $trigger$
begin
	...
	return null;
end;
$trigger$ LANGUAGE plpgsql;

CREATE trigger tr_loans_tmp
before delete on loans_tmp
for each row
execute procedure trigger_loans_tmp_del();
```

### 触发器的分类如下：

- 语句级触发器：一条SQL语句，触发器只执行一次，即使是修改了零行数据的SQL，也会导致相应的触发器执行。
- 行级触发器：每行发生变化，就会执行一次触发器。
- BEFORE触发器：在触发事件之前执行触发器。
- AFTER触发器：在触发事件之后执行触发器。

### 触发器中的一些特殊变量

- `NEW` 数据类型是RECORD；该变量为行级触发器中的INSERT/UPDATE操作保持新数据行。在语句级别的触发器以及DELETE操作，这个变量未被赋值。
- `OLD`  数据类型是RECORD；该变量为行级触发器中的UPDATE/DELETE操作保持新数据行。在语句级别的触发器以及INSERT操作，这个变量未被赋值。
- `TG_NAME`  数据类型是name；该变量包含实际触发的触发器名。
- `TG_WHEN`  数据类型是text；是值为BEFORE、AFTER或INSTEAD OF的一个字符串，取决于触发器的定义。
- `TG_LEVEL`  数据类型是text；是值为ROW或STATEMENT的一个字符串，取决于触发器的定义。
- `TG_OP` 数据类型是text；是值为INSERT、UPDATE、DELETE或TRUNCATE的一个字符串，它说明触发器是为哪个操作引发。
- `TG_RELID`  数据类型是oid；是导致触发器调用的表的对象 ID。
- `TG_RELNAME`  数据类型是name；是导致触发器调用的表的名称。现在已经被废弃，并且可能在未来的一个发行中消失。使用TG_TABLE_NAME替代。
- `TG_TABLE_NAME` 数据类型是name；是导致触发器调用的表的名称。
- `TG_TABLE_SCHEMA` 数据类型是name；是导致触发器调用的表所在的模式名。
- `TG_NARGS`  数据类型是integer；在CREATE TRIGGER语句中给触发器过程的参数数量。
- `TG_ARGV[]` 数据类型是text数组；来自CREATE TRIGGER语句的参数。索引从 0 开始记数。非法索引（小于 0 或者大于等于tg_nargs）会导致返回一个空值。


### 触发器要点

- FOR EACH ROW触发器被标记的操作修改的每一行被称为一次
- FOR EACH STATEMENT触发器为只执行一次对于任何给定的操作，不管它有多少行修改。
- WHEN子句和触发器动作可能访问的行元素被插入，删除或更新使用的形式NEW.column-name和OLD.column-name，其中列名是从表中的列名的引用该触发器相关联的。
- 如果提供WHEN子句，PostgreSQL的报表只执行WHEN子句为true的行。如果没有提供WHEN子句，PostgreSQL的语句执行的所有行。
- 如果有多个相同类型的触发器定义了相同的事件，他们将被触发名称是按字母顺序排列。
- BEFORE，AFTER或INSTEAD OF关键字决定何时触发动作将被执行，相对于插入，修改或移除相关的行。 
- 触发器表，它们与丢弃时自动删除。
- 要修改的表必须存在，在同一数据库中的表或视图，触发器被附加，必须使用表名而不使用database.tablename。
- 约束时指定的选项创建一个约束触发器。这是一个普通的触发器除外，可以调整使用SET（设定）约束的触发器触发的定时相同。预计约束触发器违反他们所实施的限制时引发异常