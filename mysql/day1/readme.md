#### 关系型数据库与非关系型数据库

关系型数据库，采用关系模型来组织数据，简单来说，**关系模型指的就是二维表格模型**。类似于Excel工作表。

**非关系型数据库**，可看成传统关系型数据库的功能阉割版本，基于键值对存储数据，通过减少很少用的功能，来提高性能。



#### 表的关系

在关系数据库管理系统中，很多表之间是有关系的，表之间的关系分为一对一关系、一对多关系和多对多关系。



#### 整数类型

int(M)这个M在字段的属性中指定了unsigned（无符号）和zerofill（零填充）的情况下才有意义。表示当整数值不够M位时，用0填充。（无符号只能存放正数和零）





#### bit类型

bit类型，如果没有指定(M)，默认是1位。这个1位，那么表示只能存1位的二进制值。这里(M)是表示二进制的位数。M范围从1到64。

对于位类型字段，之前版本直接使用SELECT语句将不会看到结果，而在MySQL8版本中默认以“0X”开头的十六进制形式显示，可以通过BIN()函数显示为二进制格式。





#### 小数类型

MySQL中使用浮点数和定点数来表示小数。浮点数有两种类型：单精度浮点数（FLOAT）和双精度浮点数（DOUBLE），定点数只有DECIMAL。浮点数和定点数都可以用(M，D)来表示。

- M是精度，表示该值总共显示M位，包括整数位和小数位，对于FLOAT和DOUBLE类型来说，M取值范围为0~255，而对于DECIMAL来说，M取值范围为0~65。
- D是标度，表示小数的位数，取值范围为0~30，同时必须<=M。



* 小数位数不够用可以使用DECIMAL

DECIMAL实际是以字符串形式存放的，在对精度要求比较高的时候（如货币、科学数据等）使用DECIMAL类型会比较好。

DECIMAL类型在不指定（M，D）时，默认为（10，0），即只保留整数部分。例如，定义DECIMAL（5,2）的类型，表示该列取值范围是-999.99~999.99。如果用户插入数据的小数部分位数超过D位，MySQL会四舍五入处理，但是如果用户插入数据的整数部分位数超过“M-D”位，则会报“Out of range”的错误。



#### char与varchar

![image-20211127210547156](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211127210547156.png)

* varchar必须指定长度，且需要额外的空间来存储字节长度
* ![image-20211127210826463](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211127210826463.png)



#### enum与set

字段名ENUM（‘值1’，‘值2’，…‘值n’）

字段名 SET（‘值1’，‘值2’，…‘值n’）

ENUM类型的字段在赋值时，只能在指定的枚举列表中取值，而且一次只能取一个。枚举列表最多可以有65535个成员。ENUM值在内部用整数表示，每个枚举值均有一个索引值， MySQL存储的就是这个索引编号。例如，定义ENUM类型的列(‘first’, ‘second’, ‘third’)。

![image-20211127211743925](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211127211743925.png)

SET类型的字段在赋值时，可从定义的值列表中选择1个或多个值的组合。SET列最多可以有64个成员。SET值在内部也用整数表示，分别是1，2，4，8……，都是2的n次方值，因为这些整数值对应的二进制都是只有1位是1，其余是0。

![image-20211127211806889](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211127211806889.png)

```mysql
create table t_enum_set(
	gender enum('男','女'),
	hobby set('游戏','睡觉','打代码','运动')
);
insert into t_enum_set
values(2, 15);
mysql> select * from t_enum_set;
+--------+-----------------------+
| gender | hobby                 |
+--------+-----------------------+
| 女     | 游戏,睡觉,打代码,运动 |
+--------+-----------------------+
6 rows in set (0.00 sec)
```





#### binary与varbinary

BINARY (M)为固定长度的二进制字符串，M表示最多能存储的字节数，取值范围是0~255个字节，如果未指定(M)表示只能存储1个字节。例如BINARY (8)，表示最多能存储8个字节，如果字段值不足(M)个字节，**将在右边填充'00'以补齐指定长度。**

VARBINARY (M)为可变长度的二进制字符串，M表示最多能存储的字节数，总字节数不能超过行的字节长度限制65535，另外还要考虑额外字节开销，VARBINARY类型的数据除了存储数据本身外，**还需要1或2个字节来存储数据的字节数**。VARBINARY类型和VARCHAR类型一样**必须指定(M)**，否则报错。





#### blob与text

BLOB是一个二进制大对象，用来存储可变数量的二进制字符串，分为TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB四种类型。TINYTEXT、TEXT、MEDIUMTEXT和LONGTEXT四种文本类型，它们分别对应于以上四种BLOB类型，具有相同的最大长度和存储要求。

BLOB类型与TEXT类型的区别如下：

（1）BLOB类型存储的是二进制字符串，TEXT类型存储的是文本字符串。BLOB类型还可以存储图片和声音等二进制数据。

（2）BLOB类型没有字符集，并且排序和比较基于列值字节的数值，TEXT类型有一个字符集，并且根据字符集对值进行排序和比较。



#### 日期类型

![image-20211127212047635](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211127212047635.png)

- 如果仅仅是表示年份信息，可以只使用YEAR类型，这样更节省空间，格式为“YYYY”，例如“2022”。YEAR允许的值范围是1901~2155。YEAR还有格式为“YY”2位数字的形式，值是00~69，表示2000~2069年，值是70~99，表示1970~1999年，从MySQL5.5.27开始，2位格式的YEAR已经不推荐使用。YEAR默认格式就是“YYYY”，没必要写成YEAR(4)，从MySQL 8.0.19开始，不推荐使用指定显示宽度的YEAR(4)数据类型。这个0年，如果是以整数的0添加的话，那么是0000年，如果是以日期/字符串的'0'添加的话，是2000年。
- 如果要表示年月日，可以使用DATE类型，格式为“YYYY-MM-DD”，例如“2022-02-04”。
- 如果要表示时分秒，可以使用TIME类型，格式为“HH:MM:SS”，例如“10:08:08”。
- 如果要表示年月日时分秒的完整日期时间，可以使用DATATIME类型，格式为“YYYY-MM-DD HH:MM:SS”，例如“2022-02-04 10:08:08”。
- 如果需要经常插入或更新日期时间为系统日期时间，则通常使用TIMESTAMP类型，格式为“YYYY-MM-DD HH:MM:SS”，例如“2022-02-04 10:08:08”。TIMESTAMP与DATETIME的区别在于TIMESTAMP的取值范围小，只支持1970-01-01 00:00:01 UTC至2038-01-19 03:14:07 UTC范围的日期时间值，其中UTC是世界标准时间，并且**TIMESTAMP类型的日期时间值在存储时会将当前时区的日期时间值转换为时间标准时间值，检索时再转换回当前时区的日期时间值**，这会更友好。而**DATETIME则只能反映出插入时当地的时区，其他时区的人查看数据必然会有误差的。**







#### sql的分类

DDL语句：数据定义语句（Data Define Language），例如：创建（create），修改（alter），删除（drop）等

DML语句：数据操作语句，例如：增（insert)，删（delete），改（update）

DQL语句  :数据查询语言， 查（select）

因为查询语句使用的非常的频繁，所以很多人把查询语句单拎出来一类，DQL（数据查询语言），DR（获取）L

DCL语句：数据控制语句，例如：grant，commit，rollback等

其他语句：USE语句，SHOW语句，SET语句等。这类的官方文档中一般称为命令。





#### DDL

###### 创建数据库

```mysql
create database 数据库名;
```

###### 查看所有的数据库

```mysql
show databases; #有一个s，代表多个数据库
```

###### 查看某个数据库的详细定义语句

```mysql
show create database 数据库名;
```

```mysql
show create database 数据库名\G
```

###### 修改数据库编码

```mysql
#修改数据库字符集和校对规则
ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称 COLLATE 字符集对应校对规则;
```

```mysql
ALTER DATABASE atguigu_chapter3_two CHARACTER SET utf8 COLLATE utf8_general_ci;
```

###### 删除数据库

```mysql
drop database 数据库名;
```

###### 使用数据库

```mysql
use 数据库名;
```

###### 查看某个数据库的所有表格

```mysql
use 数据库名;

show tables;
```

```mysql
show tables from 数据库名;
```

###### 创建表格

```mysql
create table 数据表名称(
	字段名  数据类型,
    字段名  数据类型
);
```

###### 查看表结构

```mysql
desc 表名称;
```

###### 删除表格，包括表结构和里面的数据

```mysql
drop table 表名称;
```

###### 修改表结构：删除字段

```mysql
alter table 表名称 drop 【column】 字段名称; 
```

```mysql
alter table teacher drop column weight;
```

###### 修改表结构：增加字段

```mysql
alter table 表名称 add 【column】 字段名称 数据类型; 
alter table 表名称 add 【column】 字段名称 数据类型 first;
alter table 表名称 add 【column】 字段名称 数据类型 after 另一个字段;
```

###### 修改表结构：修改字段的数据类型

```mysql
alter table 表名称 modify 【column】 字段名称 新的数据类型; 
```

###### 修改表结构：修改字段的名称

```mysql
alter table 表名称 change 【column】 旧字段名称 新的字段名称 新的数据类型;
```

###### 修改表结构：修改字段位置

```mysql
alter table 表名称 modify 【column】 字段名称 数据类型 first;
alter table 表名称 modify 【column】 字段名称 数据类型 after 另一个字段;
```

###### 修改表名称（重命名表）

```mysql
alter table 旧表名 rename 【to】 新表名;
rename table 旧表名称 to 新表名称;
```





#### DML

###### 添加一条记录到某个表中

```mysql
insert into 表名称 values(值列表); #值列表中的值的顺序、类型、个数必须与表结构一一对应
```

###### 添加一条记录到某个表中

```mysql
insert into 表名称 (字段列表) values(值列表); #值列表中的值的顺序、类型、个数必须与(字段列表)一一对应
```

```mysql
insert into teacher(tid,tname,salary,phone) values(3,'王五',16000,'15789546586');
```

###### 添加多条记录到某个表中

```mysql
insert into 表名称 values(值列表),(值列表),(值列表); #值列表中的值的顺序、类型、个数必须与表结构一一对应
```

```mysql
insert into 表名称 (字段列表) values(值列表),(值列表),(值列表); #值列表中的值的顺序、类型、个数必须与(字段列表)一一对应
```

###### 修改所有行

```mysql
update 表名称 set 字段名 = 值, 字段名 = 值; #给所有行修改
```

```mysql
#修改所有人的薪资，都涨了1000
update teacher set salary = salary + 1000 ; 
```

###### 修改部分行

```mysql
update 表名称 set 字段名 = 值, 字段名 = 值 where 条件; #给满足条件的行修改
```

```mysql
#修改天琪的薪资降低5000
update teacher set salary = salary-5000 where tname = '天琪';
```

###### 查询表中的所有数据

```mysql
select * from 表名称;
```

###### 查询表中的部分列的数据

```mysql
select 字段名,字段名 from 表名称;
```

###### 查询表中的部分行的数据

```mysql
select * from 表名称 where 条件;
```

###### 查询表中的部分行和部分列的数据

```mysql
select 字段名,字段名 from 表名称 where 条件;
```

```mysql
#查询薪资低于15000的员工的编号和姓名
select tid, tname from teacher where salary<15000;
```

###### 删除部分行的数据

```mysql
delete from 表名称 where 条件;
```

###### 删除整张表的数据，但表结构留下

```mysql
delete from 表名称;
```

```mysql
delete from teacher where tname = '天琪';
```

```mysql
delete from teacher;
```

###### 截断表，清空表中的数据，只有表结构

```mysql
truncate 表名称;
```

```mysql
truncate teacher;
```

truncate表和delete表的区别：

delete是一条一条删除记录的。如果在事务中，事务提交之前支持回滚。（后面会讲事务）

truncate是把整个表drop，新建一张，效率更高。就算在事务中，也无法回滚。
