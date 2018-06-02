---
title: php面试基础整理
date: 2017-04-03 19:16:04
tags: 
	- php
	- 面试
categories: 后端
---

### php类型转换

1. (int)$abc
2. intvel($abc)
3. settype($abc,”float”)

### php 闭包 类似匿名函数

### echo和print区别

两者都不是函数，echo没有返回值，print只支持一个参数，print会返回true或false
<!-- more -->
### php数组函数

* [array_map()](http://www.w3school.com.cn/php/func_array_map.asp)	把数组中的每个值发送到用户自定义函数，返回新的值
* [array_key_exists()](http://www.w3school.com.cn/php/func_array_key_exists.asp)	检查指定的键名是否存在于数组中
* [array_merge()](http://www.w3school.com.cn/php/func_array_merge.asp)	把一个或多个数组合并为一个数组。
* [array_walk()](http://www.w3school.com.cn/php/func_array_walk.asp)	对数组中的每个成员应用用户函数
* [array_filter()](http://www.w3school.com.cn/php/func_array_filter.asp)	用回调函数过滤数组中的元素。
* [array_reverse()](http://www.w3school.com.cn/php/func_array_reverse.asp)	以相反的顺序返回数组。
* [in_array()](http://www.w3school.com.cn/php/func_array_in_array.asp)	检查数组中是否存在指定的值。
* [sort()](http://www.w3school.com.cn/php/func_array_sort.asp)	对数组排序。
* [rsort()](http://www.w3school.com.cn/php/func_array_rsort.asp)	对数组逆向排序。
* [array_unique()](http://www.w3school.com.cn/php/func_array_unique.asp)	删除数组中的重复值
* [extract()](http://www.w3school.com.cn/php/func_array_extract.asp)	数组(对数字索引数组无效)每对key和value，生成以key为变量名、value为对应值的多组新变量。

### 正则

```
*  重复0次或多次
+  重复一次或多次
？ 重复0次或一次

/<(.*)>.*<\/\1>|<(.*) \/>/    匹配HTML标记的正则表达式

/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/   匹配邮箱格式

非负整数：^\d+$ 
正整数：^[0-9]*[1-9][0-9]*$ 
非正整数：^((-\d+)|(0+))$ 
负整数：^-[0-9]*[1-9][0-9]*$ 
整数：^-?\d+$ 
非负浮点数：^\d+(\.\d+)?$ 
正浮点数：^((0-9)+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$ 
非正浮点数：^((-\d+\.\d+)?)|(0+(\.0+)?))$ 
负浮点数：^(-((正浮点数正则式)))$ 
英文字符串：^[A-Za-z]+$ 
英文大写串：^[A-Z]+$ 
英文小写串：^[a-z]+$ 
英文字符数字串：^[A-Za-z0-9]+$ 
英数字加下划线串：^\w+$ 
E-mail地址：^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$ 
URL：^[a-zA-Z]+://(\w+(-\w+)*)(\.(\w+(-\w+)*))*(\?\s*)?$ 
或：^http:\/\/[A-Za-z0-9]+\.[A-Za-z0-9]+[\/=\?%\-&_~`@[\]\':+!]*([^<>\"\"])*$ 
邮政编码：^[1-9]\d{5}$ 
中文：^[\u0391-\uFFE5]+$ 
电话号码：^((\(\d{2,3}\))|(\d{3}\-))?(\(0\d{2,3}\)|0\d{2,3}-)?[1-9]\d{6,7}(\-\d{1,4})?$ 
手机号码：^((\(\d{2,3}\))|(\d{3}\-))?13\d{9}$ 
双字节字符(包括汉字在内)：^\x00-\xff 
匹配首尾空格：(^\s*)|(\s*$)（像vbscript那样的trim函数） 
匹配HTML标记：<(.*)>.*<\/\1>|<(.*) \/> 
匹配空行：\n[\s| ]*\r 
提取信息中的网络链接：(h|H)(r|R)(e|E)(f|F) *= *('|")?(\w|\\|\/|\.)+('|"| *|>)? 
提取信息中的邮件地址：\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)* 
提取信息中的图片链接：(s|S)(r|R)(c|C) *= *('|")?(\w|\\|\/|\.)+('|"| *|>)? 
提取信息中的IP地址：(\d+)\.(\d+)\.(\d+)\.(\d+) 
提取信息中的中国手机号码：(86)*0*13\d{9} 
提取信息中的中国固定电话号码：(\(\d{3,4}\)|\d{3,4}-|\s)?\d{8} 
提取信息中的中国电话号码（包括移动和固定电话）：(\(\d{3,4}\)|\d{3,4}-|\s)?\d{7,14} 
提取信息中的中国邮政编码：[1-9]{1}(\d+){5} 
提取信息中的浮点数（即小数）：(-?\d*)\.?\d+ 
提取信息中的任何数字 ：(-?\d*)(\.\d+)? 
IP：(\d+)\.(\d+)\.(\d+)\.(\d+) 
电话区号：/^0\d{2,3}$/ 
腾讯QQ号：^[1-9]*[1-9][0-9]*$ 
帐号(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$ 
中文、英文、数字及下划线：^[\u4e00-\u9fa5_a-zA-Z0-9]+$
```

### php异常处理

> error错误码1，warning错误码2，notice错误码8  出现error了系统是挂掉了，但是warning和notice是我们可以捕捉并处理的

php.ini中的配置指令修改：

```
1. error_reporting  =  E_ALL;		//将会向PHP报告发生的每个错误   
2. display_errors = Off;	//不显示满足上条 指令所定义规则的所有错误报告   
3. log_errors = On;		//决定日志语句记录的位置   
4. log_errors_max_len = 1024;		//设置每个日志项的最大长度   
5. error_log = /usr/local/error.log;		//指定产生的 错误报告写入的日志文件位置
```

### php日期处理

```php
date_default_timezone_set(); 函数设置用在脚本中所有日期/时间函数的默认时区。
date(‘Y-m-d H:i:s’);
strtotime(‘2017-3-8 00:00:00’);
date('w', strtotime('2017-3-5'));  // 0  0是星期天
date('l', strtotime('2017-3-5'));   // Sunday
```

### php文件处理

文件类型

> * char: 字符设备，I/O （输入输出中）以字符为单位的设备， 例如键盘，打印机等
> * dir:  目录也是文件的一种/目录文件
> * fifo: 信息管道，从一个程序传输到另一个进程
> * file: 普通的文件类型如文本文件，可执行文件
> * link: 链接文件，相当于windows下的快捷方式
> * unknown  ：未知类型

文件属性处理函数

```php
filetype("目录或文件名") 获取类型
is_dir() -- 判断给定文件名是否是一个目录
is_file() -- 判断给定文件名是否为一个正常的文件
is_link() -- 判断给定文件名是否为一个符号连接
is_executable(); -- 判断给定文件名是否可执行
file_exists();--文件是否存在
filesize();--返回文件大小
is_readable();--文件是否可读
is_writeable();--文件是否可写
filectime();--文件创建时间
filemtime();--文件修改时间
fileactime();--文件最后访问时间
stat();--文件状态，返回关于给定文件的信息的数组
bool ftruncate ( resource handle, int size );接受文件指针 handle 作为参数，并将文件大小截取为 size。如果成功则返回 TRUE，失败则返回 FALSE。
bool rename ( string oldname, string newname [, resource context] );
```

目录

```php
basename(url[,扩展名])     //返回文件名
dirname(url)         //返回文件目录名
pathinfo(url)       //路径信息
opendir(url);
readdir(url);       //返回当前目录指针只为的一个文件名，并将目录指针向后移动一位
closedir(url);
rewinddir(url);    //把目录指针重置到开始处

disk_free_space(url);和 disk_total_space(url);   统计磁盘大小  统计目录的大小只能建立递归函数把目录的文件都加起来；

mkdir(url);		//建立目录
rmdir(url);		//删除空目录
unlink(url);	//删除文件

copy($scrfile,$to);	//复制文件
```

写入文件

```php
int fwrite(resoure handle,strint string[,int length]);	//写入文件
int file_put_contents ( string filename, string data [, int flags [, resource context]] );	//和依次调用 fopen()，fwrite() 以及 fclose() 功能一样
```

读取文件

```php
string fread (resource handle, int length);
string file_get_contents ( string filename [, bool use_include_path [, resource context [, int offset [, int maxlen]]]]);
array file (string filename [, int use_include_path [, resource context]]);

string fgets (resource handle [, int length]);
string fgetc (resource handle);
int readfile (string filename [, bool use_include_path [, resource context]]);
```

文件上传

获取文件后缀

```php
substr(strrchr($file, '.'), 1); 
substr($file, strrpos($file, '.')+1); 
end(explode('.', $file)); 

$info = pathinfo($file);   return $info['extension'];

pathinfo($file, PATHINFO_EXTENSION);
PATHINFO_DIRNAME - 目录 
PATHINFO_BASENAME - 文件名（含扩展名） 
PATHINFO_EXTENSION - 扩展名 
PATHINFO_FILENAME - 文件名（不含扩展名，PHP>5.2） 
//这四个常量的值分别是1、2、4、8
```

会话控制
> #### cookie 和 session 
> PHP的session会话是通过唯一的会话ID来驱动的，PHP的会话ID一般保存在用户机器的cookie中。客户端浏览器禁用cookie后会使session失效，这时可以通过改写URL的方法，就是在URL上添加会话ID来进行会话控制。

```
Setcookie(string name, string value, int expire,string path, string domain, int secure); 
SetCookie("Cookie", "cookievalue",time()+3600, "/forum", ".learnphp.cn", 1);
SetCookie("Cookie", "");		//删除cookie
SetCookie("Cookie", "value" , time()-1 / time() );		//删除cookie
```

其中name是cookie变量名称标识，你在php中将能象使用普通变量名相同来用他引用cookie变量。value是cookie变量的初始值，expire 表示该cookie变量的有效时间；path 为该cookie变量的相关路径；domain 表示cookie变量的网站；secure 则需在 https 的安全传输时才有效。

```
session_start();        //初始化session.需在文件头部
$_SESSION[name]=value;  //配置Seeeion
echo $_SESSION[name];    //使用session
isset($_SESSION[name]);   // 判断
unset($_SESSION[name]);   //删除
session_destroy()；       //消耗所有session
```

### php安全处理

防Sql注入，防止Xss攻击，防盗链，防CSRF等

### php字符串处理函数

```php
int strlen(string str)			// 获取字符长度
string strtolower(string str)		// 转换成小写
string strtoupper(string str)	// 转换成大写
string ucfirst(string str)		// 第一个字符大写
string ucword(string str)		// 每个单词变成大写
array explode(string separator,string str[,int limit])	// 字符转数组
string implode(string delimiter, array array)		// 数组拼接成字符
int strrpos(string str,char substr[,offset])			// 字符最后一次出现的位置
mixed str_replace(string occurrence,mixed replacement,mixed str[,int count])	// 字符替换
string substr(string str,int start[,ing length])			// 字符截取
string trim(string str[,string charliset])				// 去掉两端字符
```

### 网络状态码

**1xx（临时响应）表示临时响应并需要请求者继续执行操作的状态代码。**

* 100 （继续） 请求者应当继续提出请求。 服务器返回此代码表示已收到请求的第一部分，正在等待其余部分
* 101 （切换协议） 请求者已要求服务器切换协议，服务器已确认并准备切换。

**2xx （成功）表示成功处理了请求的状态代码。**

* 200 （成功） 服务器已成功处理了请求。 通常，这表示服务器提供了请求的网页。
* 201 （已创建） 请求成功并且服务器创建了新的资源。
* 202 （已接受） 服务器已接受请求，但尚未处理。
* 203 （非授权信息） 服务器已成功处理了请求，但返回的信息可能来自另一来源。
* 204 （无内容） 服务器成功处理了请求，但没有返回任何内容。

**3xx （重定向）表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向。**

* 300 （多种选择） 针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择。
* 301 （永久移动） 请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置。
* 302 （临时移动） 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
* 303 （查看其他位置） 请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。
* 304 （未修改） 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容。

**4xx（请求错误）这些状态代码表示请求可能出错，妨碍了服务器的处理。**

* 400 （错误请求） 服务器不理解请求的语法。
* 401 （未授权） 请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。
* 403 （禁止） 服务器拒绝请求。
* 404 （未找到） 服务器找不到请求的网页。
* 405 （方法禁用） 禁用请求中指定的方法。
* 408 （请求超时） 服务器等候请求时发生超时。
* 409 （冲突） 服务器在完成请求时发生冲突。 服务器必须在响应中包含有关冲突的信息。

**5xx（服务器错误）这些状态代码表示服务器在尝试处理请求时发生内部错误。 这些错误可能是服务器本身的错误，而不是请求出错。**

* 500 （服务器内部错误） 服务器遇到错误，无法完成请求。
* 501 （尚未实施） 服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码。
* 502 （错误网关） 服务器作为网关或代理，从上游服务器收到无效响应。
* 503 （服务不可用） 服务器目前无法使用（由于超载或停机维护）。 通常，这只是暂时状态。
* 504 （网关超时） 服务器作为网关或代理，但是没有及时从上游服务器收到请求。
* 505 （HTTP 版本不受支持） 服务器不支持请求中所用的 HTTP 协议版本。

<http://tool.oschina.net/commons?type=5>

### URL的处理函数

array parse_url (string url)  
数组中的键值有：

* scheme - 如 http
* host
* port
* user
* pass
* path
* query - 在问号 ? 之后
* fragment - 在散列符号 # 之后

string parse_url(string url, PHP_URL_PATH);

`PHP_URL_SCHEME, PHP_URL_HOST, PHP_URL_PORT, PHP_URL_USER, PHP_URL_PASS, PHP_URL_PATH, PHP_URL_QUERY or PHP_URL_FRAGMENT`
