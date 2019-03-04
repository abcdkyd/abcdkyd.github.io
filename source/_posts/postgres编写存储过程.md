---
title: PostgreSQL pl/pgsql 编写存储过程
date: 2018-06-15 00:49:43
tags: 
    - PostgreSQL
    - 存储过程
categories: 数据库
---

### 基本结构

```sql
create or replace function somefunc() 
returns varchar as 
$$
declare
   name varchar := 'wangzhen';
begin
   return name;
end   
$$ language plpgsql;
```

### \$\$作用

- 函数代码在function中实际上为一个字符串，代码1和代码2等价，但代码中字符串的单引号需要写两个进行转义。

	```sql
	-- 代码1
	create or replace function somefunc() 
	returns varchar as 
	$$
	declare
	   name varchar := 'wangzhen';
	begin
	   return name;
	end   
	$$ language plpgsql;
	```
	<!-- more -->
	```sql
	--代码2
	create or replace function somefunc() 
	returns varchar as 
	'
	declare
	   name varchar := ''wangzhen'';
	begin
	   return name;
	end   
	' language plpgsql;
	```
- “美元符引用”书写字符串常量，使单引号、反斜线、\$符等按照字面值进行解释，不需要写两个或在4个或着更多进行转义，代码3和代码4等价。

	```sql
	--代码3
	create or replace function somefunc() 
	returns varchar as 
	$$
	declare
	   name varchar := 'wangzhen''blog';
	begin
	   return name;
	end   
	$$ language plpgsql;
	```
	```sql
	--代码4
	create or replace function somefunc() 
	returns varchar as 
	$$
	declare
	   name varchar := $tag$wangzhen'blog$tag$;
	begin
	   return name;
	end   
	$$ language plpgsql;
	```

- $中间可以包含可选的标签，但是标签要成对出现，且大小写敏感。代码5和代码6和代码7等价。

	```sql
	--代码5
	create or replace function somefunc() 
	returns varchar as 
	$body$
	declare
	   name varchar := $tag$wangzhen'blog$tag$;
	begin
	   return name;
	end   
	$body$ language plpgsql ;
	```
	```sql
	--代码6
	create or replace function somefunc() 
	returns varchar as 
	$func$
	declare
	   name varchar := $$wangzhen'blog$$;
	begin
	   return name;
	end   
	$func$ language plpgsql ;
	```
	```sql
	--代码7
	create or replace function somefunc() 
	returns varchar as 
	$$
	declare
	   name varchar := 'wangzhen''blog';
	begin
	   return name;
	end   
	$$ language plpgsql ;
	```
	
### 代码块

- 块名可选，代码8和9等价

	```sql
	--代码8
	create or replace function somefunc() 
	returns varchar as
	$$
	<<outblock>>
	declare 
	   name varchar := 'wangzhen';
	begin
	   return outblock.name;
	end;        
	$$ language plpgsql; 
	```
	```sql
	--代码9
	create or replace function somefunc() 
	returns varchar as
	$$
	declare 
	   name varchar := 'wangzhen';
	begin
	   return name;
	end;        
	$$ language plpgsql; 
	```

-  块可嵌套，内层同名变量覆盖外层同名变量，可通过指定块名引用。

	```sql
	--代码10
	create or replace function somefunc() 
	returns varchar as
	$$
	<<outblock>>
	declare 
	   name varchar := 'wangzhen';
	begin
	   <<innerblock>>
	   declare 
	      name varchar := 'xiaozhang';
	   begin
	      outblock.name = innerblock.name;
	   end;   
	   return outblock.name;
	end;        
	$$ language plpgsql; 
	```
	```sql
	abase=# select somefunc();
	 somefunc  
	-----------
	 xiaozhang
	```
	
- 函数体是一个隐藏的块，块名是函数名。

	```sql
	--代码11
	create or replace function somefunc(name varchar) 
	returns varchar as
	$$
	<<outblock>>
	declare 
	   name varchar := somefunc.name;
	begin
	   return outblock.name;
	end;        
	$$ language plpgsql; 
	```
	```sql
	abase=# select somefunc('xiaoli');
	 somefunc 
	----------
	 xiaoli
	(1 row)
	```

### 命名参数

- 在函数声明时直接命名参数

	```sql
	--代码12
	create or replace function somefunc(newname varchar) 
	returns varchar as
	$$
	declare 
	   name varchar := newname;
	begin
	   return name;
	end;        
	$$ language plpgsql; 
	```
	
- 使用别名和美元符号
	
	```sql
	--代码13
	create or replace function somefunc(varchar,varchar) 
	returns varchar as
	$$
	declare 
	   newname alias for $1;
	   name varchar := newname;
	   name2 varchar := $2;
	begin
	   return name;
	end;        
	$$ language plpgsql; 
	```

### 执行sql

- 没有返回结果的SQL可以直接执行

	```sql
	--代码14
	create or replace function somefunc2() 
	returns void as
	$$
	begin
	   create table test1 (n_id integer,c_mc varchar(300));
	   insert into test1 (n_id,c_mc) values (1,'wangzhen');
	end;        
	$$ language plpgsql; 
	```
	
- 不需要返回结果的select可以通过perform执行。perform替换select
	
	```sql
	--代码15
	create or replace function somefunc3() 
	returns void as
	$$
	begin
	   perform * from test1;
	end;        
	$$ language plpgsql; 
	```
	```sql
	--代码16
	create or replace function somefunc3() 
	returns void as
	$$
	begin
	   perform somefunc();
	   --perform 怎么处理with和其他复杂查询？
	end;        
	$$ language plpgsql; 
	```

- 执行动态拼接SQL

	```sql
	--代码17
	create or replace function somefunc3() 
	returns void as
	$$
	declare
	var_sql varchar := 'insert into test1 values (0,'||quote_literal('admin')||')';
	begin
	   execute var_sql;
	   execute format('insert into %I values (%L,%L)','test1',2,'xiaoniu');
	end;        
	$$ language plpgsql; 
	```

### 返回结果

> 返回结果分为返回标量（单行）和返回结果集合（多行）两种情况。

- 返回标量

	```sql
	-- 代码18
	-- 返回单行int
	create or replace function somefunc4()
	returns int as
	$$
	begin
	   return 100;
	end   
	$$ language plpgsql;
	```
	```sql
	-- 代码19
	-- 通过out参数返回
	create or replace function somefunc4(out v1 integer) 
	as
	$$
	begin
	   v1 := 100;
	end  
	$$ language plpgsql;
	
	-- OUT 返回
	DROP FUNCTION if exists update_products(character varying,character varying);
	create or replace function update_products(main_table_name varchar, child_table_name varchar, OUT success int, OUT error int) returns setof record as
	$body$
	declare
	  rec_record record;
	BEGIN
	success := 0;
	  for rec_record in select * from loans_category loop
	    success := success + 1;
	  end loop;
	  return next;
	  // r := row(main_table_name, child_table_name);  返回行数据
	END;
	
	$body$ language plpgsql;
	```
	```sql
	-- 代码20
	-- 返回单行record 复合类型
	create or replace function somefunc4(id integer,mc varchar) 
	returns record as
	$$
	declare 
	   r record;
	begin
	   r := row(id,mc);
	   return r;
	end  
	$$ language plpgsql;
	```

- SETOF 作用
	
	> 返回结果集合（多行）时，需要使用SETOF指定
	
	```sql
	--代码21
	--返回单列多行
	create or replace function somefunc5()
	returns setof int as
	$$
	declare
	id int;
	begin
	   for id in select n_id from test1 
	   loop
	      return next id;
	   end loop;
	   return;  
	end   
	$$ language plpgsql;
	```

	结果：

	```sql
	abase=# select somefunc5();
	 somefunc5 
	-----------
	         1
	         1
	         0
	         0
	         2
	(5 rows)
	```
	
- 返回结果集

	```sql
	-- 代码22
	-- 通过out参数返回
	-- returns setof record  可以省略
	create or replace function somefunc6(out id integer,out name varchar)
	returns setof record as 
	$$
	declare 
	   r record;
	begin
	   for r in select n_id,c_mc from test1 
	   loop
	      id := r.n_id;
	      name := r.c_mc;
	      return next;
	   end loop; 
	end
	$$ language plpgsql;
	```

	结果：

	```sql
	abase=# select * from somefunc6() ;
	 id |   name    
	----+-----------
	  1 | wangzhen
	  1 | xiaozhang
	  0 | admin
	  0 | admin
	  2 | xiaoniu
	(5 rows)
	```
	```sql
	-- 代码23
	-- 通过return next 返回自定义类型
	create type test_rs as (id int,mc varchar);
	create or replace function somefunc7()
	returns setof test_rs as 
	$$
	declare
	   r test_rs%rowtype;
	begin
	   for r in select n_id,c_mc from test1
	   loop
	      return next r;
	   end loop;
	   return;
	end
	$$ language plpgsql;
	```
	```sql
	-- 代码24
	-- 通过return query 返回自定义类型
	create or replace function somefunc7()
	returns setof test_rs as 
	$$
	declare
	   r test_rs%rowtype;
	begin
	   return query select n_id,c_mc from test1;
	   return;
	end
	$$ language plpgsql;
	```
	```sql
	-- 代码25
	-- 通过return next返回表类型
	create or replace function somefunc7()
	returns setof test1 as 
	$$
	declare
	   r test1%rowtype;
	begin
	   for r in select n_id,c_mc from test1
	   loop
	      return next r;
	   end loop;
	   return;
	end
	$$ language plpgsql;
	```
	```sql
	-- 代码26
	-- 通过拼接sql  return next 返回自定义类型
	create or replace function somefunc7()
	returns setof test_rs as 
	$$
	declare
	   r test_rs%rowtype;
	begin
	   for r in execute 'select n_id,c_mc from test1'
	   loop
	      return next r;
	   end loop;
	   return;
	end
	$$ language plpgsql;
	```
	```sql
	-- 代码26
	-- 通过拼接sql  return query 返回自定义类型
	create or replace function somefunc7()
	returns setof test_rs as 
	$$
	declare
	   r test_rs%rowtype;
	begin
	   return query execute 'select n_id,c_mc from test1';
	   return;
	end
	$$ language plpgsql;
	```
	
> 返回结果集时PostgreSQL 9.5.4，需要使用自定义类型或表类型制定返回类型，不能用record匿名类型。

### 循环判断

- LOOP循环
	
	```sql
	create or replace function testloop1() 
	returns void as
	$$
	declare
	    count int :=0;
	begin
	    loop
	       exit when count >=100;
	       count := count + 1;
	       raise notice 'count is %',count;
	    end loop;
	end
	$$ language plpgsql ;
	```

- WHILE循环

	```sql
	create or replace function testloop2() 
	returns void as
	$$
	declare
	    count int :=0;
	begin
	    while count <100 loop
	       count := count + 1;
	       raise notice 'count is %',count;
	    end loop;
	end
	$$ language plpgsql ;
	```

- FOR循环

	```sql
	create or replace function testloop3() 
	returns void as
	$$
	begin
	  for i in 1..100 loop
	     raise notice 'count is %',i;
	  end loop;
	end
	$$ language plpgsql;
	```
	```sql
	create or replace function testloop4() 
	returns void as
	$$
	begin
	  for i in reverse 100..1 by 2 loop 
	     raise notice 'count is %',i;
	  end loop;
	end
	$$ language plpgsql;
	```

- FOREACH循环

	```sql
	create or replace function testloop5()
	returns void as
	$$
	declare
	  v_arr int[]:= array[1,2,3,4,5];
	  i int;
	begin
	  foreach i in array v_arr loop
	      raise notice 'count is %',i;
	  end loop;
	end
	$$ language plpgsql;
	```

### 参考资料


```
-- record/%ROWTYPE类型返回
-- 返回结果集合（多行）时，需要使用SETOF指定. 
-- returns record || returns setof record

DROP FUNCTION if exists update_products(character varying,character varying);
create or replace function update_products(main_table_name varchar, child_table_name varchar) returns setof record as
$body$
declare
  rec_row loans_category%rowtype;
  rec_record record;
BEGIN
  for rec_row in select * from loans_category loop
    return next rec_row;
  end loop;
  for rec_record in select * from loans_category loop
    return next rec_record;
  end loop;
  return next;
END;
$body$ language plpgsql;

select * from update_products();
```

```
to_char(timestamp, text)	text	把时间戳转换成字串  to_char(current_timestamp, 'HH12:MI:SS')
to_char(interval, text)	text	把时间间隔转为字串  to_char(interval '15h 2m 12s', 'HH24:MI:SS')
to_char(int, text)	text	把整数转换成字串  to_char(125, '999')
to_char(double precision, text)	text	把实数/双精度数转换成字串  to_char(125.8::real, '999D9')
to_char(numeric, text)	text	把 numeric 转换成字串  to_char(-125.8, '999D99S')
to_date(text, text)	date	把字串转换成日期  to_date('05 Dec 2000', 'DD Mon YYYY')
to_timestamp(text, text)	timestamp with time zone  把字串转换成时间戳  to_timestamp('05 Dec 2000', 'DD Mon YYYY')
to_timestamp(double precision)	timestamp with time zone  把 UNIX 纪元转换成时间戳	to_timestamp(200120400)
to_number(text, text)	numeric	把字串转换成 numeric  to_number('12,454.8-', '99G999D9S')
```

1. decode 用 case when a=1 then b else c end
2. 最后一天  to_date(? +'1 mons'::interval,'yyyy-mm') -1
3. 第一天 to_date(?,'yyyy-mm') , date_trunc('month',?)
4. 字段别名 加上 as
5. 子查询一定要用别名 
6. 取子树 ,postgresql需人自己写函数，或者用一些有结构特性字段如1.1,1.1.1,1.1.2  来代替
7. trunc(im.createdate) 可改为date_trunc('day',createdate) ，date_trunc 与oracle的trunc很像,还可以
  `SELECT date_trunc('hour', TIMESTAMP '2001-02-16 20:38:40');
  Result: 2001-02-16 20:00:00+00`
  `SELECT date_trunc('year', TIMESTAMP '2001-02-16 20:38:40');
  Result: 2001-01-01 00:00:00+00`
8. postgres-当日, 带时分秒,now()  oracle- sysdate
9. postgres-当日,不带时分秒current_date,oracle  to_char(sysdate,'YYYY-MM-DD) 
10. nvl 全部替换成 coalesce  如 coalesce(im.invoiceamount,0)
11. 小数据位数round,例保留一位小数 用select round(2.16,1)  Result: 2.2 会四舍五入
12. 转志数据类型用::数据类型,如to_number() 改用::numeric 
13. 如果取子树,不包含自身,使用函数时,第二个参数取2,如,getorgantree(?,2)
14. 项目树函数第一个参数是id,其他的如果地区编码,税务机关分别有参数为编码的,如get..tree(code,level),参数为id的,如get..treebyid(id,level)
15. 修改表时,表名不能带别名,如update taxpayer_cognizance_invoice tc   这里taxpayer_cognizance_invoice 不能带别名tc

资料链接：
[PL/pgSQL 控制结构](http://www.postgres.cn/docs/9.4/plpgsql-control-structures.html#PLPGSQL-ERROR-TRAPPING)
