# 约束

## 1. 约束的作用

约束是为了保证数据的完整性，数据完整性（Data Integrity）是指数据的精确性（Accuracy）和可靠性（Reliability）。它是应防止数据库中存在不符合语义规定的数据和防止因错误信息的输入输出造成无效操作或错误信息而提出的。

数据的完整性要从以下四个方面考虑：

* 实体完整性（Entity Integrity）：例如，同一个表中，不能存在两条完全相同无法区分的记录
* 域完整性（Domain Integrity）：例如：年龄范围0-120，性别范围“男/女”
* 引用完整性（Referential Integrity）：例如：员工所在部门，在部门表中要能找到这个部门
* 用户自定义完整性（User-defined Integrity）：例如：用户名唯一、密码不能为空等，本部门经理的工资不得高于本部门职工的平均工资的5倍。

## 2. 约束的类型

### 2.1 约束类型

* 键约束：主键约束、外键约束、唯一键约束
* Not NULL约束：非空约束
* Check约束：检查约束
* Default约束：默认值约束

自增是键约束字段的一个额外的属性。

### 2.2 表级约束和列级约束

其中键约束和检查约束是表级约束，即不仅要看约束字段当前单元格的数据，还要看其他单元格的数据。

非空约束和默认值约束都是列级约束，即约束字段只看当前单元格的数据即可，和其他单元格无关。

所有的表级约束都可以在“information_schema.table_constraints”表中查看。

```mysql
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';
```

### 2.3 约束和索引

在MySQL中键约束会自动创建索引，提高查询效率。索引的详细讲解在高级部分。

MySQL高级会给大家讲解索引、存储引擎等，因为高级要给大家分析SQL性能。而基础阶段先不管效率，只要能查出来就行。



约束和索引不同：

约束是一个逻辑概念，它不会单独占用物理空间，

索引是一个物理概念，它是会占用物理空间。

例如：字典

字典里面有要求，不能有重复的字（字一样，读音也一样），这是约束。

字典里面有“目录”，它可以快速的查找某个字，目录需要占用单独的页。





## 3.非空约束

### 3.1、作用

限定某个字段/某列的值不允许为空

### 3.2、关键字：not null

### 3.3、特点

（1）只能某个列单独限定非空，不能组合非空

（2）一个表可以有很多列都分别限定了非空

### 3.4、如何指定非空约束

#### （1）建表时

```mysql
create table 表名称(
	字段名 数据类型 not null,
	字段名 数据类型 not null,
	字段名 数据类型
);
```

#### （2）建表后（了解）

```mysql
alter table 表名称 modify 【column】 字段名 数据类型 not null;

/*
如何该字段已经有值了，给该字段增加非空约束，要求该字段的值不能有NULL值，否则需要先处理NULL值才能加上非空约束
*/
```

### 3.5、如何删除非空约束（了解）

```mysql
alter table 表名称 modify 【column】 字段名 数据类型;

/*
说明：
如果某个字段有not null非空约束，使用modify修改字段的数据类型、位置、字符集和校对规则、默认值约束时，
想要保留非空约束，必须把not null带上，否则会在修改字段的数据类型、位置、字符集和校对规则时，会把非空约束给丢掉。
*/
```

### 



## 4. 唯一键约束

### 4.1、唯一键约束的作用

单列唯一：用来限制某个字段/某列的值不能重复。

组合唯一：用来限定几个字段的值组合不能重复。

### 4.2、关键字：unique key

### 4.3、特点

```mysql
（1）一个表可以有很多个唯一键约束，
（2）每一个唯一键约束字段都会自动创建索引。
（3）唯一键约束允许为空
（4）唯一键约束也可以是复合唯一
（5）删除唯一键约束的索引来删除唯一键约束
索引名默认是字段名，复合唯一默认是第一个字段名。
```

### 4.4、如何指定唯一键约束

#### （1）建表时

```mysql
#在建表时，可以指定唯一键约束
create table 表名称(
	字段名 数据类型 unique key,
	字段名 数据类型 unique key,
	字段名 数据类型
);

create table 表名称(
	字段名 数据类型,
	字段名 数据类型,
	字段名 数据类型,
	unique key(字段名),
	unique key(字段名)
);
```

#### （2）建表后指定唯一键约束（了解）

```mysql
#如何在建表后添加唯一键约束
alter table 表名称 add unique 【key】(字段名);
```

### 4.4、如何查看唯一键约束？（了解）

```mysql
desc 表名称;

show create table 表名称;

show index from 表名称; #查看表的索引信息
```

### 4.5、复合唯一

```mysql
create table 表名称(
	字段名  数据类型,
    字段名  数据类型,  
    字段名  数据类型,
    unique key(字段列表) #字段列表中写的是多个字段名，多个字段名用逗号分隔，表示那么是复合唯一，即多个字段的组合是唯一的
);
```

### 4.6、删除唯一键约束（了解）

```mysql
alter table 表名称 drop index 索引名;
#删除唯一键约束需要手动删除对应的索引
```

### 4.7、如何查看表的索引

```mysql
show index from 表名称;
```

### 

## 5. 主键约束（重要）

### 5.1、主键约束的作用

用来唯一的确定一条记录

### 5.2、关键字：primary key

### 5.3、特点

（1）唯一并且非空

（2）一个表最多只能有一个主键约束

（3）如果主键是由多列组成，可以使用复合主键

（4）主键列会自动创建索引（能够根据主键查询的，就根据主键查询，效率更高）

主键列的唯一并且非空是约束的概念，但是mysql会给每个表的主键列创建索引，会开辟单独的物理空间来存储每一个主键的目录表（Btree结构）。这样设计的意义，可以根据主键快速查询到某一行的记录。

（5）如果删除主键约束了，主键约束对应的索引就自动删除了。

### 5.4、唯一键约束和主键约束区别

```mysql
4、唯一键约束和主键约束的区别
（1）唯一键约束一个表可以有好几个，
但是主键约束只有一个
（2）唯一键约束本身不带非空限制，如果需要非空，需要单独定义。
主键约束不用再定义NOT NULL，自身就带非空限制。
```

### 5.5、如何指定主键约束

#### （1）建表时指定主键约束

```mysql
create table 表名称(
	字段名  数据类型  primary key,
    字段名  数据类型,  
    字段名  数据类型  
);
create table 表名称(
	字段名  数据类型,
    字段名  数据类型,  
    字段名  数据类型,
    primary key(字段名)
);
```

#### （2）建表后增加主键约束（了解）

```mysql
alter table 表名称 add primary key(字段列表); #字段列表可以是一个字段，也可以是多个字段，如果是多个字段的话，是复合主键
```

### 5.6、复合主键

```mysql
create table 表名称(
	字段名  数据类型,
    字段名  数据类型,  
    字段名  数据类型,
    primary key(字段名1,字段名2)  #表示字段1和字段2的组合是唯一的，也可以有更多个字段
);
```

### 5.7、如何删除主键约束？（了解）

```mysql
alter table 表名称 drop primary key;
```

### 

## 6. 默认值约束

### 6.1、作用

给某个字段/某列指定默认值，当添加时或修改时，可以使用默认值。

### 6.2、关键字：default

### 6.3、如何给字段加默认值

#### （1）建表时

```mysql
create table 表名称(
	字段名  数据类型  primary key,
    字段名  数据类型  unique key not null,  
    字段名  数据类型  unique key,
    字段名  数据类型  not null default 默认值, 
);
create table 表名称(
	字段名  数据类型 default 默认值 ,
    字段名  数据类型 not null default 默认值,  
    字段名  数据类型 not null default 默认值,,
    primary key(字段名),
    unique key(字段名)
);

说明：默认值约束一般不在唯一键和主键列上加
```

#### （2）建表后（了解）

```mysql
alter table 表名称 modify 字段名 数据类型 default 默认值;

#如果这个字段原来有非空约束，你还保留非空约束，那么在加默认值约束时，还得保留非空约束，否则非空约束就被删除了
#同理，在给某个字段加非空约束也一样，如果这个字段原来有默认值约束，你想保留，也要在modify语句中保留默认值约束，否则就删除了
alter table 表名称 modify 字段名 数据类型 default 默认值 not null;
```



### 6.4、如何删除默认值约束（了解）

```mysql
alter table 表名称 modify 字段名 数据类型 ;#删除默认值约束，也不保留非空约束

alter table 表名称 modify 字段名 数据类型  not null; #删除默认值约束，保留非空约束
```

### 



## 7.自增约束

### 7.1、作用

作用：给某个字段自动赋值，这个值是一直往上增加，如果没有特意干扰的，每次自增1.

### 7.2、关键字：auto_increment

### 7.3、特点和要求

（1）一个表只能有一个自增字段，因为一个表只有一个AUTO_INCREMENT属性记录自增字段值
（2）并且自增字段只能是key字段，即定义了主键、唯一键等键约束的字段。
一般都是给主键和唯一键加自增。
（3）自增字段应该是数值类型，一般都是整数类型。
（4）AUTO_INCREMENT属性值 必须 > 当前自增字段的最大值
（5）如果自增列指定了 0 和 null，会在当前最大值的基础上自增，
如果自增列手动指定了具体值，直接赋值为具体值。

### 7.4、如何指定自增约束

#### （1）建表时 

```mysql
create table 表名称(
	字段名  数据类型  primary key auto_increment,
    字段名  数据类型  unique key not null,  
    字段名  数据类型  unique key,
    字段名  数据类型  not null default 默认值, 
);
create table 表名称(
	字段名  数据类型 default 默认值 ,
    字段名  数据类型 unique key auto_increment,  
    字段名  数据类型 not null default 默认值,,
    primary key(字段名)
);
```



#### （2）建表后（了解）

```mysql
alter table 表名称 modify 字段名 数据类型 auto_increment;
```

### 7.5、如何删除自增约束（了解）

```mysql
alter table 表名称 modify 字段名 数据类型 auto_increment;#给这个字段增加自增约束
alter table 表名称 modify 字段名 数据类型; #去掉auto_increment相当于删除
```

### 7.6、演示错误

```mysql
create table employee(
	eid int auto_increment,
    ename varchar(20)
);
ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key   
```

```mysql
create table employee(
	eid int primary key,
    ename varchar(20) unique key auto_increment
);
ERROR 1063 (42000): Incorrect column specifier for column 'ename'  因为ename不是整数类型
```

### 

## 8. 检查约束

### 8.1、作用

检查（CHECK） 约束用于限制字段中的值的范围。如果对单个字段定义 CHECK 约束，那么该字段只允许特定范围的值。如果对一个表定义 CHECK 约束，那么此约束会基于行中其他字段的值在特定的字段中对值进行限制。

在MySQL 8.0.16版本之前， CREATE TABLE语句支持给单个字段定义CHECK约束的语法，但是不起作用。

### 8.2、关键字：check

例如MySQL8.0之前，就算给表定义了检查约束，也不起作用。在MySQL8.0.16版本之后，CREATE TABLE语句既支持给单个字段定义列级CHECK约束的语法，还支持定义表级CHECK约束的语法。

### 8.3、如何定义检查约束

#### （1）建表时

```mysql
#在建表时，可以指定检查约束
create table 表名称(
	字段名1 数据类型 check(条件),  #在字段后面直接加检查约束
	字段名2 数据类型,
	字段名3 数据类型,
	check (条件)  #可以限定两个字段之间的取值条件
);

#在建表时，可以指定检查约束
create table 表名称(
	字段名1 数据类型 check(条件) enforced,  #在字段后面直接加检查约束
	字段名2 数据类型,
	字段名3 数据类型,
	check (条件)  enforced #可以限定两个字段之间的取值条件
);
```

如果省略或指定为ENFORCED，则会创建检查约束并强制执行约束，不满足约束的数据行不能插入成功。

如果写的是not ENFORCED，则不满足检查约束也没关系。

#### （2）建表后（了解）

```mysql
#如何在建表后添加检查约束，使用add check
alter table 表名称 add check(条件);
```

### 



## 9. 外键约束

外键约束会影响性能，效率，所以很多人不愿意加外键约束。

学生问题：

（1）如果两个表之间有关系（一对一、一对多），比如：员工表和部门表（一对多），它们之间是否一定要建外键约束？

答：不是的

（2）建和不建外键约束有什么区别？

答：

建外键约束，你的操作（创建表、删除表、添加、修改、删除）会受到限制，从语法层面受到限制。例如：在员工表中不可能添加一个员工信息，它的部门的值在部门表中找不到。

不建外键约束，你的操作（创建表、删除表、添加、修改、删除）不受限制，要保证数据的引用完整性，只能依靠程序员的自觉，或者是在Java程序中进行限定。例如：在员工表中，可以添加一个员工的信息，它的部门指定为一个完全不存在的部门。

（3）那么建和不建外键约束和查询有没有关系？

答：没有

### 9.1、作用

限定某个表的某个字段的引用完整性，

比如：员工表的员工所在部门的选择，必须在部门表能找到对应的部分。

### 9.2、关键字：foreign key



### 9.3、主表和从表/父表和子表

主表（父表）：被引用的表，被参考的表

从表（子表）：引用别人的表，参考别人的表

例如：员工表的员工所在部门这个字段的值要参考部门表，

​		部门表是主表，员工表是从表。

例如：学生表、课程表、选课表

​		选课表的学生和课程要分别参考学生表和课程表，

​		学生表和课程表是主表，选课表是从表。

### 9.4、特点

（1）在“从表”中指定外键约束，并且一个表可以建立多个外键约束

（2）创建(create)表时就指定外键约束的话，先创建主表，再创建从表

（3）删表时，先删从表（或先删除外键约束），再删除主表

（4）从表的外键列，必须引用/参考主表的键列（主键或唯一键）

为什么？因为被依赖/被参考的值必须是唯一的

（5）从表的外键列的数据类型，要与主表被参考/被引用的列的数据类型一致，并且逻辑意义一致。

例如：都是表示部门编号，都是int类型。

（6）外键列也会自动建立索引（根据外键查询效率很高，很多）

（7）外键约束的删除，所以不会自动删除，如果要删除对应的索引，必须手动删除

### 9.5、如何指定外键约束

#### （1）建表时

```mysql
create table 主表名称(
	字段1  数据类型  primary key,
    字段2  数据类型
);

create table 从表名称(
	字段1  数据类型  primary key,
    字段2  数据类型,
    foreign key （从表的某个字段) references 主表名(被参考字段)
);
#(从表的某个字段)的数据类型必须与主表名(被参考字段)的数据类型一致，逻辑意义也一样
#(从表的某个字段)的字段名可以与主表名(被参考字段)的字段名一样，也可以不一样
```

#### （2）建表后（了解）

```mysql
alter table 从表名称 add foreign key (从表的字段) references 主表（被引用字段) 【on update xx】【on delete xx】;
```

### 9.6、如何查看外键约束名

```mysql
desc 从表名称; #可以看到外键约束，但看不到外键约束名

show create table 从表名称; #可以看到外键约束名

SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';
#information_schema数据库名（系统库）
#table_constraints表名称（专门存储各个表的约束）
#WHERE条件
#table_name = '表名称'条件是指定表名称
```

### 9.7、如何查看外键字段索引

```mysql
show index from 表名称; #查看某个表的索引名
```



### 9.8、如何删除外键约束（了解）

删除外键约束，不会自动删除外键约束列的索引，需要单独删除。

```mysql
(1)第一步先查看约束名和删除外键约束
SELECT * FROM information_schema.table_constraints WHERE table_name = '表名称';#查看某个表的约束名

alter table 从表名 drop foreign key 外键约束名;

（2）第二步查看索引名和删除索引
show index from 表名称; #查看某个表的索引名

alter table 从表名 drop index 索引名;
```

### 9.9、演示问题

（1）失败：不是键列

```mysql
create table dept(
	did int ,		#部门编号
    dname varchar(50)			#部门名称
);

create table emp(
	eid int primary key,  #员工编号
    ename varchar(5),     #员工姓名
    deptid int,				#员工所在的部门
    foreign key (deptid) references dept(did)
);ERROR 1215 (HY000): Cannot add foreign key constraint  原因是dept的did不是键列
```

（2）失败：数据类型不一致

```mysql
create table dept(
	did int primary key,		#部门编号
    dname varchar(50)			#部门名称
);

create table emp(
	eid int primary key,  #员工编号
    ename varchar(5),     #员工姓名
    deptid char,				#员工所在的部门
    foreign key (deptid) references dept(did)
);ERROR 1215 (HY000): Cannot add foreign key constraint  原因是从表的deptid字段和主表的did字段的数据类型不一致，并且要它俩的逻辑意义一致
```

（3）成功，两个表字段名一样

```mysql
create table dept(
	did int primary key,		#部门编号
    dname varchar(50)			#部门名称
);

create table emp(
	eid int primary key,  #员工编号
    ename varchar(5),     #员工姓名
    did int,				#员工所在的部门
    foreign key (did) references dept(did)  
    #emp表的deptid和和dept表的did的数据类型一致，意义都是表示部门的编号
    #是否重名没问题，因为两个did在不同的表中
);
```

### 9.10、设置外键约束等级

* Cascade方式：在父表上update/delete记录时，同步update/delete掉子表的匹配记录 

* Set null方式：在父表上update/delete记录时，将子表上匹配记录的列设为null，但是要注意子表的外键列不能为not null  

* No action方式：如果子表中有匹配的记录,则不允许对父表对应候选键进行update/delete操作  

* Restrict方式：同no action, 都是立即检查外键约束

* Set default方式（在可视化工具SQLyog中可能显示空白）：父表有变更时,子表将外键列设置成一个默认的值，但Innodb不能识别

如果没有指定等级，就相当于Restrict方式。

```mysql
create table emp(
	id int primary key auto_increment,
	name varchar(20) not null,
	departmentid int,
	foreign key (departmentid) references dept(did) on update cascade on delete set null
	#on delete set null要求departmentid字段没有not null约束
);

#添加子表时记录和  定义on update cascade on delete set null无关。还是要在主表能找到对应的记录。
```



## 10.运算符

## 10.1 导入数据

在命令行客户端登录mysql，使用source指令导入

```mysql
mysql> source d:\练习脚本.sql
```



## 10.2 算术运算符（掌握）

```mysql
加：+
减：-
乘：*
除：/   div（只保留整数部分）
模：%   mod

mysql中没有 +=等运算符
```

## 10.3 比较运算符（掌握）

```mysql
大于：>
小于：<
大于等于：>=
小于等于：>=
等于：=   不能用于null判断
不等于：!=  或 <>  不能用于null判断
```

```mysql
#查询薪资比15000高的员工
select * from t_employee where salary > 15000;

#查询薪资等于15000的员工
select * from t_employee where salary = 15000; #注意Java中判断用==，mysql判断用=
select * from t_employee where salary = 16888;

#查询籍贯native_place不是北京的
select * from t_employee where native_place != '北京';
select * from t_employee where native_place <> '北京';
```

## 10.4 逻辑运算符（掌握）

```mysql
逻辑与：&& 或 and
逻辑或：|| 或 or
逻辑非：! 或 not
逻辑异或： xor
```

## 10.5 区间范围或集合范围比较运算符（掌握）

```mysql
区间范围：between x  and  y
	    not between x  and  y
集合范围：in (x,x,x)
	    not  in(x,x,x)
```

```mysql
#查询薪资在[10000,15000]
select * from t_employee where salary>=10000 && salary<=15000;
select * from t_employee where salary between 10000 and 15000;

#查询籍贯在这几个地方的
select * from t_employee where native_place in ('北京', '浙江', '江西');

#查询薪资不在[10000,15000]
select * from t_employee where salary not between 10000 and 15000;

#查询籍贯不在这几个地方的
select * from t_employee where native_place not in ('北京', '浙江', '江西');
```

## 10.6 模糊比较运算符（掌握）

%：代表任意个字符

_：代表一个字符，如果两个下划线代表两个字符

```mysql
#查询名字中包含'冰'字
select * from t_employee where ename like '%冰%';

#查询名字以‘雷'结尾的
select * from t_employee where ename like '%雷';

#查询名字以’李'开头
select * from t_employee where ename like '李%';

#查询名字有冰这个字，但是冰的前面只能有1个字
select * from t_employee where ename like '_冰%';
```

```mysql
#查询当前mysql数据库的字符集情况
show variables like '%character%';
```

## 10.7 关于null值的问题（掌握）

```mysql
#（1）判断时
xx is null
xx is not null
xx <=> null

#(2)计算时
ifnull(xx,代替值)  当xx是null时，用代替值计算
```

```mysql
#查询奖金比例为null的员工
select * from t_employee where commission_pct = null;  #失败
select * from t_employee where commission_pct = NULL; #失败
select * from t_employee where commission_pct = 'NULL'; #失败

select * from t_employee where commission_pct is null;   #成功
select * from t_employee where commission_pct <=> null;  #成功  <=>安全等于
```

```mysql
#查询员工的实发工资，实发工资 = 薪资 + 薪资 * 奖金比例
select ename , salary + salary * commission_pct "实发工资" from t_employee; #失败，当commission_pct为null，结果都为null

select ename ,salary , commission_pct, salary + salary * ifnull(commission_pct,0) "实发工资" from t_employee;
```

## 10.8 位运算符（了解）

基本不用，知道一下

```mysql
左移：<<
右移：>>
按位与：&
按位或：|
按位异或：^
按位取反：~
```

## 



## 11.系统预设函数

函数：代表一个独立的可复用的功能。

和Java中的方法有所不同，不同点在于：MySQL中的函数必须有返回值，参数可以有可以没有。

MySQL中函数分为：

（1）系统预定义函数：MySQL数据库管理软件给我提供好的函数，直接用就可以，任何数据库都可以用公共的函数。

- 分组函数：或者又称为聚合函数，多行函数，表示会对表中的多行记录一起做一个“运算”，得到一个结果。
  - 求平均值的avg，求最大值的max，求最小值的min，求总和sum，求个数的count等
- 单行函数：表示会对表中的每一行记录分别计算，有n行得到还是n行结果
  - 数学函数、字符串函数、日期时间函数、条件判断函数、窗口函数等

![image-20211201084442467](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201084442467.png)

（2）用户自定义函数：由开发人员自己定义的，通过CREATE FUNCTION语句定义，是属于某个数据库的对象。

![image-20211201083845550](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201083845550.png)

### 11.1 分组函数

调用完函数后，结果的行数变少了，可能得到一行，可能得到少数几行。

分组函数有合并计算过程。

**分组函数不统计null**

**常用分组函数类型**

* **AVG(x)** ：求平均值
* **SUM(x)**：求总和
* **MAX(x)** ：求最大值
* **MIN(x)** ：求最小值
* **COUNT(x) **：统计记录数，使用主键效率会更高，因为主键带索引
* ....

```mysql
#演示分组函数，聚合函数，多行函数
#统计t_employee表的员工的数量
SELECT COUNT(*) FROM t_employee;
SELECT COUNT(1) FROM t_employee;
SELECT COUNT(eid) FROM t_employee;
SELECT COUNT(commission_pct) FROM t_employee;

/*
count(*)或count(常量值)：都是统计实际的行数。
count(字段/表达式)：只统计“字段/表达式”部分非NULL值的行数。
*/

#找出t_employee表中最高的薪资值
SELECT MAX(salary) FROM t_employee;

#找出t_employee表中最低的薪资值
SELECT MIN(salary) FROM t_employee;

#统计t_employee表中平均薪资值
SELECT AVG(salary) FROM t_employee;

#统计所有人的薪资总和，财务想看一下，一个月要准备多少钱发工资
SELECT SUM(salary) FROM t_employee; #没有考虑奖金
SELECT SUM(salary+salary*IFNULL(commission_pct,0)) FROM t_employee; 

#找出年龄最小、最大的员工的出生日期
SELECT MAX(birthday),MIN(birthday) FROM t_employee;

#查询最新入职的员工的入职日期
SELECT MAX(hiredate) FROM t_employee;
```

### 11.2 单行函数（了解，用的时候查，太多了，演示一小部分）

调用完函数后，记录数不变，一行计算完之后还是一行。

#### 1、数学函数

以下表格中也只是列出了一部分

| 函数          | 用法                                 |
| ------------- | ------------------------------------ |
| ABS(x)        | 返回x的绝对值                        |
| CEIL(x)       | 返回大于x的最小整数值                |
| FLOOR(x)      | 返回小于x的最大整数值                |
| MOD(x,y)      | 返回x/y的模                          |
| RAND()        | 返回0~1的随机值                      |
| ROUND(x,y)    | 返回参数x的四舍五入的有y位的小数的值 |
| TRUNCATE(x,y) | 返回数字x截断为y位小数的结果         |
| SQRT(x)       | 返回x的平方根                        |
| POW(x,y)      | 返回x的y次方                         |

```mysql
#单行函数
#演示数学函数
#在“t_employee”表中查询员工无故旷工一天扣多少钱，
#分别用CEIL、FLOOR、ROUND、TRUNCATE函数。
#假设本月工作日总天数是22天，
#旷工一天扣的钱=salary/22。
SELECT ename,salary/22,CEIL(salary/22),
FLOOR(salary/22),ROUND(salary/22,2),
TRUNCATE(salary/22,2) FROM t_employee; 


#查询公司平均薪资，并对平均薪资分别
#使用CEIL、FLOOR、ROUND、TRUNCATE函数
SELECT AVG(salary),CEIL(AVG(salary)),
FLOOR(AVG(salary)),ROUND(AVG(salary)),
TRUNCATE(AVG(salary),2) FROM t_employee;
```



#### 2、字符串函数

下面列出部分字符串函数：

| 函数                                                         | 功能描述                                               |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| CONCAT(S1,S2,……Sn)                                           | 连接S1,S2,……Sn为一个字符串                             |
| CONCAT_WS(s,S1,S2,……Sn)                                      | 同CONCAT(S1,S2,…)函数，但每个字符串之间要加上s         |
| CHAR_LENGTH(s)                                               | 返回字符串s的**字符**数                                |
| LENGTH(s)                                                    | 返回字符串s的**字节**数，和字符集有关                  |
| LOCATE(str1,str)或  POSITION(str1 in str)或  INSTR(str,str1) | 返回子字符串str1在str中的开始位置                      |
| UPPER(s)或UCASE(s)                                           | 将字符串s的所有字母转成大写字母                        |
| LOWER(s)或LCASE(s)                                           | 将字符串s的所有字母转成小写字母                        |
| LEFT(s,n)                                                    | 返回字符串s最左边的n个字符                             |
| RIGHT(s,n)                                                   | 返回字符串s最右边的n个字符                             |
| LPAD(str,len,pad)                                            | 用字符串pad对str最左边进行填充直到str的长度达到len     |
| RPAD(str,len,pad)                                            | 用字符串pad对str最右边进行填充直到str的长度达到len     |
| LTRIM(s)                                                     | 去掉字符串s左侧的空格                                  |
| RTRIM(s)                                                     | 去掉字符串s右侧的空格                                  |
| TRIM(s)                                                      | 去掉字符串s开始与结尾的空格                            |
| TRIM([BOTH] s1 FROM s)                                       | 去掉字符串s开始与结尾的s1                              |
| TRIM([LEADING] s1 FROM s)                                    | 去掉字符串s开始处的s1                                  |
| TRIM([TRAILING]s1 FROM s)                                    | 去掉字符串s结尾处的s1                                  |
| INSERT(str,index,len,instr)                                  | 将字符串str从index位置开始len个字符的替换为字符串instr |
| REPLACE(str,a,b)                                             | 用字符串b替换字符串str中所有出现的字符串a              |
| REPEAT(str,n)                                                | 返回str重复n次的结果                                   |
| REVERSE(s)                                                   | 将字符串反转                                           |
| STRCMP(s1,s2)                                                | 比较字符串s1,s2                                        |
| SUBSTRING(s,index,len)                                       | 返回从字符串s的index位置截取len个字符                  |

```mysql
#字符串函数
#mysql中不支持 + 拼接字符串，需要调用函数来拼接
#（1）在“t_employee”表中查询员工姓名ename和电话tel，
#并使用CONCAT函数，CONCAT_WS函数。
SELECT CONCAT(ename,tel),CONCAT_WS('-',ename,tel) FROM t_employee;


#（2）在“t_employee”表中查询薪资高于15000的男员工姓名，
#并把姓名处理成“张xx”的样式。
#LEFT（s，n）函数表示取字符串s最左边的n个字符，
#而RPAD（s，len，p）函数表示在字符串s的右边填充p使得字符串长度达到len。
SELECT  RPAD(LEFT(ename,1),3,'x'),salary
FROM t_employee
WHERE salary>15000 AND gender ='男';

#（3）在“t_employee”表中查询薪资高于10000的男员工姓名、
#姓名包含的字符数和占用的字节数。
SELECT ename,CHAR_LENGTH(ename) AS 占用字符数,LENGTH(ename) AS 占用字节数量
FROM t_employee
WHERE salary>10000 AND gender ='男';


#（4）在“t_employee”表中查询薪资高于10000的男员工姓名和邮箱email，
#并把邮箱名“@”字符之前的字符串截取出来。
SELECT ename,email,
SUBSTRING(email,1, POSITION('@' IN email)-1)
FROM t_employee
WHERE salary > 10000 AND gender ='男';

#mysql中 SUBSTRING截取字符串位置，下标从1开始，不是和Java一样从0开始。
#mysql中 position等指定字符串中某个字符，子串的位置也不是从0开始，都是从1开始。

SELECT TRIM('    hello   world   '); #默认是去掉前后空白符
SELECT CONCAT('[',TRIM('    hello   world   '),']'); #默认是去掉前后空白符
SELECT TRIM(BOTH '&' FROM '&&&&hello   world&&&&'); #去掉前后的&符号
SELECT TRIM(LEADING '&' FROM '&&&&hello   world&&&&'); #去掉开头的&符号
SELECT TRIM(TRAILING '&' FROM '&&&&hello   world&&&&'); #去掉结尾的&符号
```



#### 3、日期时间函数

| 函数                                                         | 功能描述                                            |
| ------------------------------------------------------------ | --------------------------------------------------- |
| CURDATE()或CURRENT_DATE()                                    | 返回当前系统日期                                    |
| CURTIME()或CURRENT_TIME()                                    | 返回当前系统时间                                    |
| NOW()/SYSDATE()/CURRENT_TIMESTAMP()/  LOCALTIME()/LOCALTIMESTAMP() | 返回当前系统日期时间                                |
| UTC_DATE()/UTC_TIME()                                        | 返回当前UTC日期值/时间值                            |
| UNIX_TIMESTAMP(date)                                         | 返回一个UNIX时间戳                                  |
| YEAR(date)/MONTH(date)/DAY(date)/  HOUR(time)/MINUTE(time)/SECOND(time) | 返回具体的时间值                                    |
| EXTRACT(type FROM date)                                      | 从日期中提取一部分值                                |
| DAYOFMONTH(date)/DAYOFYEAR(date)                             | 返回一月/年中第几天                                 |
| WEEK(date)/WEEKOFYEAR(date)                                  | 返回一年中的第几周                                  |
| DAYOFWEEK()                                                  | 返回周几，注意，周日是1，周一是2，…周六是7          |
| WEEKDAY(date)                                                | 返回周几，注意，周一是0，周二是1，…周日是6          |
| DAYNAME(date)                                                | 返回星期，MONDAY,TUESDAY,…SUNDAY                    |
| MONTHNAME(date)                                              | 返回月份，January,…                                 |
| DATEDIFF(date1,date2)/TIMEDIFF(time1,time2)                  | 返回date1-date2的日期间隔/返回time1-time2的时间间隔 |
| DATE_ADD(date,INTERVAL expr type)或ADDDATE/DATE_SUB/SUBDATE  | 返回与给定日期相差INTERVAL时间段的日期              |
| ADDTIME(time,expr)/SUBTIME(time,expr)                        | 返回给定时间加上/减去expr的时间值                   |
| DATE_FORMAT(datetime,fmt)/  TIME_FORMAT(time,fmt)            | 按照字符串fmt格式化日期datetime值/时间time值        |
| STR_TO_DATE(str,fmt)                                         | 按照字符串fmt对str进行解析，解析为一个日期          |
| GET_FORMAT(val_type,format_type)                             | 返回日期时间字符串的显示格式                        |

now()此条语句执行的时间

sysdate()当前函数执行那一刹那的时间

函数中日期时间类型说明

| 参数类型 | 描述 | 参数类型      | 描述     |
| -------- | ---- | ------------- | -------- |
| YEAR     | 年   | YEAR_MONTH    | 年月     |
| MONTH    | 月   | DAY_HOUR      | 日时     |
| DAY      | 日   | DAY_MINUTE    | 日时分   |
| HOUR     | 时   | DAY_SECOND    | 日时分秒 |
| MINUTE   | 分   | HOUR_MINUTE   | 时分     |
| SECOND   | 秒   | HOUR_SECOND   | 时分秒   |
| WEEK     | 星期 | MINUTE_SECOND | 分秒     |
| QUARTER  | 一刻 |               |          |

函数中format参数说明

| 格式符 | 说明                                                      | 格式符 | 说明                                                    |
| ------ | --------------------------------------------------------- | ------ | ------------------------------------------------------- |
| %Y     | 4位数字表示年份                                           | %y     | 两位数字表示年份                                        |
| %M     | 月名表示月份（January,…）                                 | %m     | 两位数字表示月份（01,02,03，…）                         |
| %b     | 缩写的月名（Jan.,Feb.,…）                                 | %c     | 数字表示月份（1,2,3…）                                  |
| %D     | 英文后缀表示月中的天数（1st,2nd,3rd,…）                   | %d     | 两位数字表示表示月中的天数（01,02,…）                   |
| %e     | 数字形式表示月中的天数（1,2,3,…）                         | %p     | AM或PM                                                  |
| %H     | 两位数字表示小数，24小时制（01,02,03,…）                  | %h和%I | 两位数字表示小时，12小时制（01,02,03,…）                |
| %k     | 数字形式的小时，24小时制（1,2,3,…）                       | %l     | 数字表示小时，12小时制（1,2,3,…）                       |
| %i     | 两位数字表示分钟（00,01,02,…）                            | %S和%s | 两位数字表示秒（00,01,02,…）                            |
| %T     | 时间，24小时制（hh:mm:ss）                                | %r     | 时间，12小时制（hh:mm:ss）后加AM或PM                    |
| %W     | 一周中的星期名称（Sunday,…）                              | %a     | 一周中的星期缩写（Sun.,Mon.,Tues.,…）                   |
| %w     | 以数字表示周中的天数（0=Sunday,1=Monday,…）               | %j     | 以3位数字表示年中的天数（001,002,…）                    |
| %U     | 以数字表示的的第几周（1,2,3,…）  其中Sunday为周中的第一天 | %u     | 以数字表示年中的年份（1,2,3,…）  其中Monday为周中第一天 |
| %V     | 一年中第几周（01~53），周日为每周的第一天，和%X同时使用   | %X     | 4位数形式表示该周的年份，周日为每周第一天，和%V同时使用 |
| %v     | 一年中第几周（01~53），周一为每周的第一天，和%x同时使用   | %x     | 4位数形式表示该周的年份，周一为每周第一天，和%v同时使用 |
| %%     | 表示%                                                     |        |                                                         |

GET_FORMAT函数中val_type 和format_type参数说明

| 值类型   | 格式化类型 | 显示格式字符串    |
| -------- | ---------- | ----------------- |
| DATE     | EUR        | %d.%m.%Y          |
| DATE     | INTERVAL   | %Y%m%d            |
| DATE     | ISO        | %Y-%m-%d          |
| DATE     | JIS        | %Y-%m-%d          |
| DATE     | USA        | %m.%d.%Y          |
| TIME     | EUR        | %H.%i.%s          |
| TIME     | INTERVAL   | %H%i%s            |
| TIME     | ISO        | %H:%i:%s          |
| TIME     | JIS        | %H:%i:%s          |
| TIME     | USA        | %h:%i:%s %p       |
| DATETIME | EUR        | %Y-%m-%d %H.%i.%s |
| DATETIME | INTERVAL   | %Y%m%d %H%i%s     |
| DATETIME | ISO        | %Y-%m-%d %H:%i:%s |
| DATETIME | JIS        | %Y-%m-%d %H:%i:%s |
| DATETIME | USA        | %Y-%m-%d %H.%i.%s |

```mysql
#日期时间函数
/*
获取系统日期时间值
获取某个日期或时间中的具体的年、月等值
获取星期、月份值，可以是当天的星期、当月的月份
获取一年中的第几个星期，一年的第几天
计算两个日期时间的间隔
获取一个日期或时间间隔一定时间后的另个日期或时间
和字符串之间的转换
*/
#（1）获取系统日期。CURDATE（）和CURRENT_DATE（）函数都可以获取当前系统日期。将日期值“+0”会怎么样？
SELECT CURDATE(),CURRENT_DATE();

#（2）获取系统时间。CURTIME（）和CURRENT_TIME（）函数都可以获取当前系统时间。将时间值“+0”会怎么样？
SELECT CURTIME(),CURRENT_TIME();

#（3）获取系统日期时间值。CURRENT_TIMESTAMP（）、LOCALTIME（）、SYSDATE（）和NOW（）
SELECT CURRENT_TIMESTAMP(),LOCALTIME(),SYSDATE(),NOW();

#（4）获取当前UTC（世界标准时间）日期或时间值。
#本地时间是根据地球上不同时区所处的位置调整 UTC 得来的，
#例如，北京时间比UTC时间晚8个小时。
#UTC_DATE(),CURDATE(),UTC_TIME(), CURTIME()
SELECT UTC_DATE(),CURDATE(),UTC_TIME(), CURTIME();


#（5）获取UNIX时间戳。
SELECT UNIX_TIMESTAMP(),UNIX_TIMESTAMP('2022-1-1');

#（6）获取具体的时间值，比如年、月、日、时、分、秒。
#分别是YEAR（date）、MONTH（date）、DAY（date）、HOUR（time）、MINUTE（time）、SECOND（time）。
SELECT YEAR(CURDATE()),MONTH(CURDATE()),DAY(CURDATE());
SELECT HOUR(CURTIME()),MINUTE(CURTIME()),SECOND(CURTIME());


#（7）获取日期时间的指定值。EXTRACT（type FROM date/time）函数
SELECT EXTRACT(YEAR_MONTH FROM CURDATE());

#（8）获取两个日期或时间之间的间隔。
#DATEDIFF（date1，date2）函数表示返回两个日期之间间隔的天数。
#TIMEDIFF（time1，time2）函数表示返回两个时间之间间隔的时分秒。

#查询今天距离员工入职的日期间隔天数
SELECT ename,DATEDIFF(CURDATE(),hiredate) FROM t_employee;

#查询现在距离中午放学还有多少时间
SELECT TIMEDIFF(CURTIME(),'12:0:0');

#（9）在“t_employee”表中查询本月生日的员工姓名、生日。
SELECT ename,birthday
FROM t_employee
WHERE MONTH(CURDATE()) = MONTH(birthday);


#(10)#查询入职时间超过5年的
SELECT ename,hiredate,DATEDIFF(CURDATE(),hiredate) 
FROM t_employee
WHERE DATEDIFF(CURDATE(),hiredate)  > 365*5;
```



#### 4、加密函数

列出了部分的加密函数。

| 函数                  | 用法                                                         |
| --------------------- | ------------------------------------------------------------ |
| password(str)         | 返回字符串str的加密版本，41位长的字符串<font color='red'>（mysql8不再支持）</font> |
| md5(str)              | 返回字符串str的md5值，也是一种加密方式                       |
| SHA(str)              | 返回字符串str的sha算法加密字符串，40位十六进制值的密码字符串 |
| SHA2(str,hash_length) | 返回字符串str的sha算法加密字符串，密码字符串的长度是hash_length/4。hash_length可以是224、256、384、512、0，其中0等同于256。 |

```mysql
#加密函数
/*
当用户需要对数据进行加密时，
比如做登录功能时，给用户的密码加密等。
*/
#password函数在mysql8已经移除了
SELECT PASSWORD('123456');

#使用md5加密
SELECT MD5('123456'),SHA('123456'),sha2('123456',0);

SELECT CHAR_LENGTH(MD5('123456')),SHA('123456'),sha2('123456',0);


CREATE TABLE t_user(
id INT PRIMARY KEY AUTO_INCREMENT,
username VARCHAR(20),
PASSWORD VARCHAR(100)
);

INSERT INTO t_user VALUES(NULL,'chai',MD5('123456'));

SELECT * FROM t_user 
WHERE username='chai' AND PASSWORD =MD5('123456');


SELECT * FROM t_user 
WHERE username='chai' AND PASSWORD ='123456';
```

#### 5、系统信息函数

| 函数       | 用法               |
| ---------- | ------------------ |
| database() | 返回当前数据库名   |
| version()  | 返回当前数据库版本 |
| user()     | 返回当前登录用户名 |

```mysql
#其他函数
SELECT USER();
SELECT VERSION();
SELECT DATABASE();
```

#### 6、条件判断函数

| 函数                                                         | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| IF(value,t,f)                                                | 如果value是真，返回t,否则返回f                               |
| IFNULL(value1,value2)                                        | 如果value1不为空，返回value1,否则返回value2                  |
| CASE WHEN 条件1  THEN result1 WHEN 条件2  THEN result2 … ELSE resultn END | 依次判断条件，哪个条件满足了，就返回对应的result,所有条件都不满足就返回ELSE的result。如果没有单独的ELSE子句，当所有WHEN后面的条件都不满足时则返回NULL值结果。**等价于Java中if...else if....** |
| CASE expr WHEN 常量值1  THEN 值1  WHEN 常量值2  THEN 值2 …  ELSE 值n END | 判断表达式expr与哪个常量值匹配，找到匹配的就返回对应值，都不匹配就返回ELSE的值。如果没有单独的ELSE子句，当所有WHEN后面的常量值都不匹配时则返回NULL值结果。**等价于Java中switch....case** |

```mysql
#条件判断函数
/*
这个函数不是筛选记录的函数，
而是根据条件不同显示不同的结果的函数。
*/
#如果薪资大于20000，显示高薪，否则显示正常
SELECT ename,salary,IF(salary>20000,'高薪','正常')
FROM t_employee;

#计算实发工资
#实发工资 = 薪资 + 薪资 * 奖金比例
SELECT ename,salary,commission_pct,
salary + salary * commission_pct
FROM t_employee;
#如果commission_pct是，计算完结果是NULL

SELECT ename,salary,commission_pct,
salary + salary * IFNULL(commission_pct,0) AS 实发工资
FROM t_employee;


SELECT ename,salary,commission_pct,
ROUND(salary + salary * IFNULL(commission_pct,0),2) AS 实发工资
FROM t_employee;

#查询员工编号，姓名，薪资，等级，等级根据薪资判断，
#如果薪资大于20000，显示“羡慕级别”，
#如果薪资15000-20000，显示“努力级别”，
#如果薪资10000-15000，显示“平均级别”
#如果薪资10000以下，显示“保底级别”
/*mysql中没有if...elseif函数，有case 函数。
等价于if...elseif 
*/
SELECT eid,ename,salary,
CASE WHEN salary>20000 THEN '羡慕级别'
     WHEN salary>15000 THEN '努力级别'
     WHEN salary>10000 THEN '平均级别'
     ELSE '保底级别'
END AS "等级"
FROM t_employee;  

#在“t_employee”表中查询入职7年以上的
#员工姓名、工作地点、轮岗的工作地点数量情况。
/*
计算工作地点的数量，转换为求 work_place中逗号的数量+1。
 work_place中逗号的数量 = work_place的总字符数 -  work_place去掉,的字符数
 work_place去掉, ，使用replace函数
*/
SELECT work_place, 
CHAR_LENGTH(work_place)-CHAR_LENGTH(REPLACE(work_place,',',''))
FROM t_employee;
 
 #类似于Java中switch...case
SELECT ename,work_place,
CASE (CHAR_LENGTH(work_place)-CHAR_LENGTH(REPLACE(work_place,',',''))+1)
WHEN 1 THEN '只在一个地方工作'
WHEN 2 THEN '在两个地方来回奔波'
WHEN 3 THEN '在三个地方流动'
ELSE '频繁出差'
END AS "工作地点数量情况"
FROM t_employee
WHERE DATEDIFF(CURDATE(),hiredate)  > 365*7;
```

#### 7、其他函数

从5.7.8版本之后开始支持JSON数据类型，并提供了操作JSON类型的数据的相关函数。

MySQL提供了非常丰富的空间函数以支持各种空间数据的查询和处理。

这两类函数基础阶段不讲，如果项目中有用到查询API使用。

### 11.3 窗口函数

窗口函数也叫OLAP函数（Online Anallytical Processing，联机分析处理），可以对数据进行实时分析处理。窗口函数是每条记录都会分析，有几条记录执行完还是几条，因此也属于单行函数。  

| 函数分类 | 函数                 | 功能描述                                                     |
| -------- | -------------------- | ------------------------------------------------------------ |
| 序号函数 | ROW_NUMBER()         | 顺序排序，每行按照不同的分组逐行编号，例如：1,2,3,4          |
|          | RANK()               | 并列排序，每行按照不同的分组进行编号，同一个分组中排序字段值出现重复值时，并列排序并跳过重复序号，例如：1,1,3 |
|          | DENSE_RANK()         | 并列排序，每行按照不同的分组进行编号，同一个分组中排序字段值出现重复值时，并列排序不跳过重复序号，例如：1,1,2 |
| 分布函数 | PERCENT_RANK()       | 排名百分比，每行按照公式（rank-1）/ （rows-1）进行计算。其中，rank为RANK()函数产生的序号，rows为当前窗口的记录总行数 |
|          | CUME_DIST()          | 累积分布值，表示每行按照当前分组内小于等于当前rank值的行数 / 分组内总行数 |
| 前后函数 | LAG（expr，n）       | 返回位于当前行的前n行的expr值                                |
|          | LEAD（expr，n）      | 返回位于当前行的后n行的expr值                                |
| 首尾函数 | FIRST_VALUE（expr）  | 返回当前分组第一行的expr值                                   |
|          | LAST_VALUE（expr）   | 返回当前分组每一个rank最后一行的expr值                       |
| 其他函数 | NTH_VALUE（expr，n） | 返回当前分组第n行的expr值                                    |
|          | NTILE（n）           | 用于将分区中的有序数据分为n个等级，记录等级数                |

窗口函数的语法格式如下

```mysql
函数名([参数列表]) OVER ()
函数名([参数列表]) OVER (子句)
```

over关键字用来指定窗口函数的窗口范围。如果OVER后面是空（），则表示SELECT语句筛选的所有行是一个窗口。OVER后面的（）中支持以下4种语法来设置窗口范围。

- WINDOW：给窗口指定一个别名；
- PARTITION BY子句：一个窗口范围还可以分为多个区域。按照哪些字段进行分区/分组，窗口函数在不同的分组上分别处理分析；
- ORDER BY子句：按照哪些字段进行排序，窗口函数将按照排序后结果进行分析处理；
- FRAME子句：FRAME是当前分区的一个子集，FRAME子句用来定义子集的规则。



## 12. 关联查询

### 12.1 什么是关联查询

关联查询：两个或更多个表一起查询。

前提条件：这些一起查询的表之间是有关系的（一对一、一对多），它们之间一定是有关联字段，这个关联字段可能建立了外键，也可能没有建立外键。

比如：员工表和部门表，这两个表依靠“部门编号”进行关联。



### 12.2 关联查询结果分为几种情况

![image-20211201000449563](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201000449563.png)

### 12.3  关联查询的SQL有几种情况

1、内连接：inner join  ... on

结果：A表 ∩ B表

2、左连接：A left join B on

（2）A表全部

（3）A表- A∩B

3、右连接：A right join B on

（4）B表全部

（5）B表-A∩B

4、全外连接：full outer join ... on，但是**mysql不支持这个关键字**，mysql使用union（合并）结果的方式代替

（6）A表∪B表：    （2） A表结果  union （4）B表的结果

（7）A∪B - A∩B     （3）A表- A∩B结果 union （5）B表-A∩B结果 

![A∩B](https://dum1615.oss-cn-chengdu.aliyuncs.com/A%E2%88%A9B.png)



![A表](https://dum1615.oss-cn-chengdu.aliyuncs.com/A%E8%A1%A8.png)

![A表-（A∩B）](https://dum1615.oss-cn-chengdu.aliyuncs.com/A%E8%A1%A8-%EF%BC%88A%E2%88%A9B%EF%BC%89.png)

![B表](https://dum1615.oss-cn-chengdu.aliyuncs.com/B%E8%A1%A8.png)

![B表-（A∩B）](https://dum1615.oss-cn-chengdu.aliyuncs.com/B%E8%A1%A8-%EF%BC%88A%E2%88%A9B%EF%BC%89.png)

![A表∪B表](https://dum1615.oss-cn-chengdu.aliyuncs.com/A%E8%A1%A8%E2%88%AAB%E8%A1%A8.png)



![](https://dum1615.oss-cn-chengdu.aliyuncs.com/%EF%BC%88A%E8%A1%A8%E2%88%AAB%E8%A1%A8%EF%BC%89-%EF%BC%88A%E2%88%A9B%EF%BC%89.png)

```mysql
/*
（1）凡是联合查询的两个表，必须有“关联字段”，
关联字段是逻辑意义一样，数据类型一样，名字可以一样也可以不一样的两个字段。
比如：t_employee （A表）中did和t_department（B表）中的did。

发现关联字段其实就是“可以”建外键的字段。当然联合查询不要求一定建外键。

（2）联合查询必须写关联条件，关联条件的个数 = n - 1.
n是联合查询的表的数量。
如果2个表一起联合查询，关联条件数量是1，
如果3个表一起联合查询，关联条件数量是2，
如果4个表一起联合查询，关联条件数量是3，
。。。。
否则就会出现笛卡尔积现象，这是应该避免的。

（3）关联条件可以用on子句编写，也可以写到where中。
但是建议用on单独编写，这样呢，可读性更好。

每一个join后面都要加on子句
A inner|left|right join  B on 条件
A inner|left|right join  B on 条件 inner|left|right jon C on 条件
```



#### 1、内连接

```mysql
#演示内连接，结果是A∩B
/*
观察数据：
t_employee  看成A表
t_department 看成B表
此时t_employee （A表）中有 李红和周洲的did是NULL，没有对应部门，
    t_department（B表）中有 测试部，在员工表中找不到对应记录的。
*/

#查询所有员工的姓名，部门编号，部门名称
#如果员工没有部门的，不要
#如果部门没有员工的，不要
/*
员工的姓名在t_employee （A表）中
部门的编号，在t_employee （A表）和t_department（B表）都有
部门名称在t_department（B表）中
所以需要联合两个表一起查询。
*/
SELECT ename,did,dname
FROM t_employee INNER JOIN t_department;
#错误Column 'did' in field list is ambiguous
#因为did在两个表中都有，名字相同，它不知道取哪个表中字段了
#有同学说，它俩都是部门编号，随便取一个不就可以吗？
#mysql不这么认为，有可能存在两个表都有did，但是did的意义不同的情况。
#为了避免这种情况，需要在编写sql的时候，明确指出是用哪个表的did

SELECT ename,t_department.did,dname
FROM t_employee INNER JOIN t_department;
#语法对，结果不太对
#结果出现“笛卡尔积”现象， A表记录 * B表记录
/*
（1）凡是联合查询的两个表，必须有“关联字段”，
关联字段是逻辑意义一样，数据类型一样，名字可以一样也可以不一样的两个字段。
比如：t_employee （A表）中did和t_department（B表）中的did。

发现关联字段其实就是可以建外键的字段。当然联合查询不要求一定建外键。

（2）联合查询必须写关联条件，关联条件的个数 = n - 1.
n是联合查询的表的数量。
如果2个表一起联合查询，关联条件数量是1，
如果3个表一起联合查询，关联条件数量是2，
如果4个表一起联合查询，关联条件数量是3，
。。。。
否则就会出现笛卡尔积现象，这是应该避免的。

（3）关联条件可以用on子句编写，也可以写到where中。
但是建议用on单独编写，这样呢，可读性更好。

每一个join后面都要加on子句
A inner|left|right join  B on 条件
A inner|left|right join  B on 条件 inner|left|right jon C on 条件
*/


SELECT ename,t_department.did,dname
FROM t_employee INNER JOIN t_department 
ON t_employee.did = t_department.did;

SELECT *
FROM t_employee INNER JOIN t_department 
ON t_employee.did = t_department.did;


#查询部门编号为1的女员工的姓名、部门编号、部门名称、薪资等情况
SELECT ename,gender,t_department.did,dname,salary
FROM t_employee INNER JOIN t_department 
ON t_employee.did = t_department.did
WHERE t_department.did = 1 AND gender = '女';

#查询部门编号为1的员工姓名、部门编号、部门名称、薪资、职位编号、职位名称等情况
SELECT ename,gender,t_department.did,dname,salary,job_id,jname
FROM t_employee INNER JOIN t_department ON t_employee.did = t_department.did
 INNER JOIN t_job ON t_employee.`job_id` = t_job.`jid`
WHERE t_department.did = 1;

```

#### 2、左连接

```mysql
#演示左连接
/*
(2)A
（3）A-A∩B
*/
/*
观察数据：
t_employee  看成A表
t_department 看成B表
此时t_employee （A表）中有 李红和周洲的did是NULL，没有对应部门，
    t_department（B表）中有 测试部，在员工表中找不到对应记录的。
*/
#查询所有员工，包括没有指定部门的员工，他们的姓名、薪资、部门编号、部门名称
SELECT ename,salary,t_department.did,dname
FROM t_employee LEFT JOIN t_department
ON t_employee.did = t_department.did;
#查询的是A结果  A left join B

#查询没有部门的员工信息
SELECT ename,salary,t_department.did,dname
FROM t_employee LEFT JOIN t_department
ON t_employee.did = t_department.did
WHERE t_employee.did IS NULL;
#查询的结果是A - A∩B
#此时的where条件，建议写子表的关联字段is null，这样更准确一点。
#如果要建外键，它们肯定有子表和父表的角色，写子表的关联字段is null
#因为父表中这个字段一般是主键，不会为null。

```

#### 3、右连接

```mysql
/*
右连接
（4）B
（5）B - A∩B
*/
#演示右连接
/*
观察数据：
t_employee  看成A表
t_department 看成B表
此时t_employee （A表）中有 李红和周洲的did是NULL，没有对应部门，
    t_department（B表）中有 测试部，在员工表中找不到对应记录的。
*/
#查询所有部门，包括没有对应员工的部门，他们的姓名、薪资、部门编号、部门名称
SELECT ename,salary,t_department.did,dname
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did;
#查询的是B结果  A RIGHT join B

#查询没有员工部门的信息
SELECT ename,salary,t_department.did,dname
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
WHERE t_employee.did IS NULL;
#查询的结果是B - A∩B
#此时的where条件，建议写子表的关联字段is null，这样更准确一点。
#如果要建外键，它们肯定有子表和父表的角色，写子表的关联字段is null
#因为父表中这个字段一般是主键，不会为null。



#查询所有员工，包括没有指定部门的员工，他们的姓名、薪资、部门编号、部门名称
SELECT ename,salary,t_department.did,dname
FROM t_department RIGHT JOIN t_employee
ON t_employee.did = t_department.did;
#查询的是B结果  A RIGHT join B

#查询没有部门的员工信息
SELECT ename,salary,t_department.did,dname
FROM t_department RIGHT JOIN t_employee
ON t_employee.did = t_department.did
WHERE t_employee.did IS NULL;
#查询的结果是B - A∩B  A right join B
#此时的where条件，建议写子表的关联字段is null，这样更准确一点。
#如果要建外键，它们肯定有子表和父表的角色，写子表的关联字段is null
#因为父表中这个字段一般是主键，不会为null。
```

#### 4、union

```mysql
/*
union实现
（6）A∪B
（7）A∪B - A∩B
    A-A∩B  ∪  B-A∩B
*/
#演示用union合并两个查询结果实现A∪B 和A∪B - A∩B
/*
union合并时要注意：
（1）两个表要查询的结果字段是一样的
（2）UNION ALL表示直接合并结果，如果有重复的记录一并显示
   ALL去掉表示合并结果时，如果有重复记录，去掉。
（3）要实现A∪B的结果，那么必须是合并 查询是A表结果和查询是B表结果的select语句。
同样要实现A∪B - A∩B的结果，那么必须是合并查询是A-A∩B结果和查询是B-A∩B的select语句。
*/

#查询所有员工和所有部门，包括没有指定部门的员工和没有分配员工的部门。
SELECT *
FROM t_employee LEFT JOIN t_department
ON t_employee.did = t_department.did

UNION 

SELECT *
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did;


#以下union会报错
SELECT * FROM t_employee
UNION
SELECT * FROM t_department;
/*
错误代码： 1222
The used SELECT statements have a different number of columns
两个Select语句的列数是不同的。

column：列，表中的字段。
columns：很多的字段，即字段列表
select 字段列表 from 表名称;
*/


#联合 查询结果是A表的select 和查询结果是A∩B的select语句，是得不到A∪B
SELECT *
FROM t_employee LEFT JOIN t_department
ON t_employee.did = t_department.did

UNION 

SELECT *
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did;

#查询那些没有分配部门的员工和没有指定员工的部门，即A表和B表在对方那里找不到对应记录的数据。
SELECT *
FROM t_employee LEFT JOIN t_department
ON t_employee.did = t_department.did
WHERE t_employee.did IS NULL

UNION 

SELECT *
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
WHERE t_employee.did IS NULL;
```

### 12.4 联合查询字段列表问题

```mysql
#查询字段的问题
#查询每一个员工及其所在部门的信息
#要求：显示员工的编号，姓名，部门编号，部门名称
SELECT eid,ename,did,dname
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did;
/*
错误代码： 1052
Column 'did' in field list is ambiguous（模糊不清的；引起歧义的）
*/

SELECT eid,ename,t_employee.did,dname
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did;


#查询每一个员工及其所在部门的信息
#要求，显示员工的编号，姓名，部门表的所有字段
SELECT eid,ename,t_department.*
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did;
```

### 12.5 自连接

自己连接自己

```mysql
#演示特殊的联合查询/关联查询/多表查询：自连接
#物理上，是一张表
#逻辑上，是两张表
/*
分析表结构：t_employee表
mid：是表示存储员工的领导编号。即该员工归谁管。领导编号其实就是“领导”作为员工身份的员工编号
   例如：eid为3的员工邓超远，他的mid是7，表示他的领导是员工编号为7的员工。
   eid为7的员工是贾宝玉，他的eid是7，贾宝玉作为员工来说，他的编号是7，作为领导来说，他的编号也是7。

mid的取值范围受到eid字段的限制。mid的值选择必须是eid现有值范围。

可以理解为mid和eid是关联字段，如果要建外键，可以在mid字段上建外键。
foreign key(mid) references t_employee(eid)   

此时t_employee既是子表也是父表。
员工表t_employee建立了外键：
CONSTRAINT `t_employee_ibfk_3` FOREIGN KEY (`mid`) REFERENCES `t_employee` (`eid`) ON DELETE SET NULL ON UPDATE CASCADE
*/
#查询每一个员工自己的编号、名字、薪资和他的领导的编号、姓名、薪资。
SELECT emp.eid,emp.ename,emp.salary,  mgr.eid,mgr.ename,mgr.salary
FROM t_employee AS emp INNER JOIN t_employee AS mgr
ON emp.mid = mgr.eid; 
#把t_employee当成两张表，通过取别名的方式
#t_employee AS emp 把员工表 当成员工表
# t_employee AS mgr 把员工表  当成存储领导信息的领导表
#emp.mid = mgr.eid; 员工表的领导编号就是领导表的员工编号
```





# 13. select的7大字句

## 13.1 7大子句顺序

（1）from：从哪些表中筛选

（2）on：关联多表查询时，去除笛卡尔积

（3）where：从表中筛选的条件

（4）group by：分组依据

（5）having：在统计结果中再次筛选（with rollup)

（6）order by：排序

（7）limit：分页

必须按照（1）-（7）的顺序编写子句。

## 13.2 演示

### 13.2.1 from子句

```mysql
SELECT 1; #没有任何子句
SELECT 9/2; #没有任何子句

#1、from子句
SELECT * 
FROM t_employee; #表示从某个表中筛选数据
```

### 13.2.2 on子句

```mysql
#2、on子句
/*
（1）on必须配合join使用
（2）on后面只写关联条件
所谓关联条件是两个表的关联字段的关系
（3）有n张表关联，就有n-1个关联条件
两张表关联，就有1个关联条件
三张表关联，就有2个关联条件
*/
SELECT *
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did;  #1个关联条件

#查询员工的编号，姓名，职位编号，职位名称，部门编号，部门名称
#需要t_employee员工表，t_department部门表，t_job职位表
SELECT eid,ename,t_job.job_id,t_job.job_name, `t_department`.`did`,`t_department`.`dname`
FROM t_employee INNER JOIN t_department INNER JOIN t_job
ON t_employee.did = t_department.did AND t_employee.job_id = t_job.job_id;

```

### 13.2.3 where子句

```mysql
#3、where子句，在查询结果中筛选
#查询女员工的信息，以及女员工的部门信息
SELECT *
FROM t_employee INNER JOIN t_department
ON t_employee.did = t_department.did
WHERE gender = '女';
```

### 13.2.4 group by子句

```mysql
#4、group by分组
#查询所有员工的平均薪资
SELECT AVG(salary) FROM t_employee;

#查询每一个部门的平均薪资
SELECT did,ROUND(AVG(salary),2 )
FROM t_employee
GROUP BY did;

#查询每一个部门的平均薪资，显示部门编号，部门的名称，该部门的平均薪资
SELECT t_department.did,dname,ROUND(AVG(salary),2 )
FROM t_department LEFT JOIN t_employee
ON t_department.did = t_employee.did
GROUP BY t_department.did;

#查询每一个部门的平均薪资，显示部门编号，部门的名称，该部门的平均薪资
#要求，如果没有员工的部门，平均薪资不显示null，显示0
SELECT t_department.did,dname,IFNULL(ROUND(AVG(salary),2),0)
FROM t_department LEFT JOIN t_employee
ON t_department.did = t_employee.did
GROUP BY t_department.did;

#查询每一个部门的女员工的平均薪资，显示部门编号，部门的名称，该部门的平均薪资
#要求，如果没有员工的部门，平均薪资不显示null，显示0
SELECT t_department.did,dname,IFNULL(ROUND(AVG(salary),2),0)
FROM t_department LEFT JOIN t_employee
ON t_department.did = t_employee.did
WHERE gender = '女'
GROUP BY t_department.did;
```



问题1：合计，WITH ROLLUP，加在group by后面

```mysql
#问题1：合计，WITH ROLLUP，加在group by后面
#按照部门统计人数
SELECT did, COUNT(*) FROM t_employee GROUP BY did;
#按照部门统计人数，并合计总数
SELECT did, COUNT(*) FROM t_employee GROUP BY did WITH ROLLUP;
SELECT IFNULL(did,'合计'), COUNT(*) FROM t_employee GROUP BY did WITH ROLLUP;
SELECT IFNULL(did,'合计') AS "部门编号" , COUNT(*)  AS "人数" FROM t_employee GROUP BY did WITH ROLLUP;

```

问题2：是否可以按照多个字段分组统计

```mysql
#问题2：是否可以按照多个字段分组统计
#按照不同的部门，不同的职位，分别统计男和女的员工人数
SELECT did, job_id, gender, COUNT(*)
FROM t_employee 
GROUP BY did, job_id, gender;
```



问题4：分组统计时，select后面字段列表的问题

```mysql
#问题4：分组统计时，select后面字段列表的问题
SELECT eid,ename, did, COUNT(*) FROM t_employee; 
#eid,ename, did此时和count(*)，不应该出现在select后面

SELECT eid,ename, did, COUNT(*) FROM t_employee GROUP BY did; 
#eid,ename此时和count(*)，不应该出现在select后面

SELECT  did, COUNT(*) FROM t_employee GROUP BY did;
#分组统计时，select后面只写和分组统计有关的字段，其他无关字段不要出现，否则会引起歧义
```



### 13.2.5 having子句

```mysql
#5、having
/*
having子句也写条件
where的条件是针对原表中的记录的筛选。where后面不能出现分组函数。
having子句是对统计结果（分组函数计算后）的筛选。having可以加分组函数。
*/
#查询每一个部门的女员工的平均薪资，显示部门编号，部门的名称，该部门的平均薪资
#要求，如果没有员工的部门，平均薪资不显示null，显示0
#最后只显示平均薪资高于12000的部门信息
SELECT t_department.did,dname,IFNULL(ROUND(AVG(salary),2),0)
FROM t_department LEFT JOIN t_employee
ON t_department.did = t_employee.did
WHERE gender = '女'
GROUP BY t_department.did
HAVING IFNULL(ROUND(AVG(salary),2),0) >12000;


#查询每一个部门的男和女员工的人数
SELECT did,gender,COUNT(*)
FROM t_employee
GROUP BY did,gender;

#查询每一个部门的男和女员工的人数，显示部门编号，部门的名称，性别，人数
SELECT t_department.did,dname,gender,COUNT(eid)
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
GROUP BY t_department.did,gender;

#查询每一个部门薪资超过10000的男和女员工的人数，显示部门编号，部门的名称，性别，人数
#只显示人数低于3人
SELECT t_department.did,dname,gender,COUNT(eid)
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
WHERE salary > 10000
GROUP BY t_department.did,gender
HAVING COUNT(eid) < 3;
```

### 13.2.6 order by子句

```mysql
#6、排序 order by
/*
升序和降序，默认是升序
asc代表升序
desc 代表降序
*/
#查询员工信息，按照薪资从高到低
SELECT * FROM t_employee
ORDER BY salary DESC;

#查询每一个部门薪资超过10000的男和女员工的人数，显示部门编号，部门的名称，性别，人数
#只显示人数低于3人，按照人数升序排列
SELECT t_department.did,dname,gender,COUNT(eid)
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
WHERE salary > 10000
GROUP BY t_department.did,gender
HAVING COUNT(eid) < 3
ORDER BY COUNT(eid);


#查询员工的薪资，按照薪资从低到高，薪资相同按照员工编号从高到低
SELECT *
FROM t_employee
ORDER BY salary ASC , eid DESC;
```

### 13.2.7 limit子句

```mysql
#演示limit子句
/*
limit子句是用于分页显示结果。
limit m,n
n：表示最多该页显示几行
m：表示从第几行开始取记录，第一个行的索引是0
m = (page-1)*n  page表示第几页

每页最多显示5条，n=5
第1页，page=1，m = (1-1)*5 = 0;  limit 0,5
第2页，page=2，m = (2-1)*5 = 5;  limit 5,5
第3页，page=3，m = (3-1)*5 = 10;  limit 10,5
*/
#查询员工表的数据，分页显示，每页显示5条记录
#第1页
SELECT * FROM t_employee LIMIT 0,5;
#第2页
SELECT * FROM t_employee LIMIT 5,5;
#第3页
SELECT * FROM t_employee LIMIT 10,5;
#第4页
SELECT * FROM t_employee LIMIT 15,5;
#第5页
SELECT * FROM t_employee LIMIT 20,5;
#第6页
SELECT * FROM t_employee LIMIT 25,5;


#查询所有的男员工信息，分页显示，每页显示3条，第2页
#limit m,n  n=3,page=2,m=(page-1)*n=3
SELECT *
FROM t_employee
WHERE gender ='男'
LIMIT 3,3

#查询每一个编号为偶数的部门，显示部门编号，名称，员工数量，
#只显示员工数量>=2的结果，按照员工数量升序排列，
#每页显示2条，显示第1页
SELECT t_department.did,dname,COUNT(eid)
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
WHERE t_department.did%2=0
GROUP BY t_department.did
HAVING COUNT(eid)>=2
ORDER BY COUNT(eid)
LIMIT 0,2;
```

# 

# 14、子查询

## 14.1 SELECT的SELECT中嵌套子查询

```mysql
/*
子查询：嵌套在另一个SQL语句中的查询。
SELECT语句可以嵌套在另一个SELECT中，UPDATE，DELETE，INSERT，CREATE语句等。

(1)SELECT的SELECT中嵌套子查询
*/

#（1）在“t_employee”表中查询每个人薪资和公司平均薪资的差值，
#并显示员工薪资和公司平均薪资相差5000元以上的记录。
SELECT ename AS "姓名",
	salary AS "薪资",
 ROUND((SELECT AVG(salary) FROM t_employee),2) AS "全公司平均薪资",
 ROUND(salary-(SELECT AVG(salary) FROM t_employee),2) AS "差值"
FROM t_employee
WHERE ABS(ROUND(salary-(SELECT AVG(salary) FROM t_employee),2))>5000;

#（2）在“t_employee”表中查询每个部门平均薪资和公司平均薪资的差值。
SELECT did,AVG(salary),
AVG(salary)-(SELECT AVG(salary) FROM t_employee)
FROM t_employee
GROUP BY did;
```



## 14.2 SELECT的WHERE或HAVING中嵌套子查询

当子查询结果作为外层另一个SQL的过滤条件，通常把子查询嵌入到WHERE或HAVING中。根据子查询结果的情况，分为如下三种情况。

- 当子查询的结果是单列单个值，那么可以直接使用比较运算符，如“<”、“<=”、“>”、“>=”、“=”、“!=”等与子查询结果进行比较。
- 当子查询的结果是单列多个值，那么可以使用比较运算符IN或NOT IN进行比较。
- 当子查询的结果是单列多个值，还可以使用比较运算符, 如“<”、“<=”、“>”、“>=”、“=”、“!=”等搭配ANY、SOME、ALL等关键字与查询结果进行比较。

```mysql
/*
子查询嵌套在where后面。
在where或having后面的子查询结果是：
（1）单个值，那么可以用=，>,<,>=,<=,!=这样的运算符和子查询的结果做比较
（2）多个值，那么需要用in,not in, >all,>any....形式做比较
 如“<”、“<=”、“>”、“>=”、“=”、“!=”等搭配ANY、SOME、ALL等关键字与查询结果进行比较

*/
#（1）在“t_employee”表中查询薪资最高的员工姓名（ename）和薪资（salary）。
#SELECT ename,MAX(salary) FROM t_employee;#错误
#取表中第一行员工的姓名和全公司最高的薪资值一起显示。

SELECT ename,salary
FROM t_employee
WHERE salary = (SELECT MAX(salary) FROM t_employee);

#（2）在“t_employee”表中查询比全公司平均薪资高的男员工姓名和薪资。
SELECT ename,salary
FROM t_employee
WHERE salary > (SELECT AVG(salary) FROM t_employee) AND gender = '男';

#（3）在“t_employee”表中查询和“白露”，“谢吉娜”同一部门的员工姓名和电话。
SELECT ename,tel,did
FROM t_employee
WHERE did IN(SELECT did FROM t_employee WHERE ename='白露' || ename='谢吉娜');

SELECT ename,tel,did
FROM t_employee
WHERE did =ANY(SELECT did FROM t_employee WHERE ename='白露' || ename='谢吉娜');


#（4）在“t_employee”表中查询薪资比“白露”，“李诗雨”，“黄冰茹”三个人的薪资都要高的员工姓名和薪资。
SELECT ename,salary
FROM t_employee
WHERE salary >ALL(SELECT salary FROM t_employee WHERE ename IN('白露','李诗雨','黄冰茹'));


#（5）查询“t_employee”和“t_department”表，按部门统计平均工资，
#显示部门平均工资比全公司的总平均工资高的部门编号、部门名称、部门平均薪资，
#并按照部门平均薪资升序排列。
SELECT t_department.did,dname,AVG(salary)
FROM t_employee RIGHT JOIN t_department
ON t_employee.did = t_department.did
GROUP BY t_department.did
HAVING AVG(salary) >(SELECT AVG(salary) FROM t_employee)
ORDER BY AVG(salary);
```

## 14.3 SELECT中的EXISTS型子查询

EXISTS型子查询也是存在外层SELECT的WHERE子句中，不过它和上面的WHERE型子查询的工作模式不相同，所以这里单独讨论它。

如果EXISTS关键字后面的参数是一个任意的子查询，系统将对子查询进行运算以判断它是否返回行，如果至少返回一行，那么EXISTS的结果为true，此时外层查询语句将进行查询；如果子查询没有返回任何行，那么EXISTS的结果为false，此时外层查询语句不进行查询。EXISTS和NOT EXISTS的结果只取决于是否返回行，而不取决于这些行的内容，所以这个子查询输入列表通常是无关紧要的。

如果EXISTS关键字后面的参数是一个关联子查询，即子查询的WHERE条件中包含与外层查询表的关联条件，那么此时将对外层查询表做循环，即在筛选外层查询表的每一条记录时，都看这条记录是否满足子查询的条件，如果满足就再用外层查询的其他WHERE条件对该记录进行筛选，否则就丢弃这行记录。

```mysql
#exist型子查询
/*
（1）exists()中的子查询和外面的查询没有联合的情况下，
如果exists()中的子查询没有返回任何行，那么外面的子查询就不查了。
（2）exists()中的子查询与外面的查询有联合工作的情况下，
循环进行把外面查询表的每一行记录的值，代入()中子查询，如果可以查到结果，
就留下外面查询的这条记录，否则就舍去。
*/

#（1）查询“t_employee”表中是否存在部门编号为NULL的员工，
#如果存在，查询“t_department”表的部门编号、部门名称。
SELECT * FROM t_department 
WHERE EXISTS(SELECT * FROM t_employee  WHERE did IS NULL);

#（2）查询“t_department”表是否存在与“t_employee”表相同部门编号的记录，
#如果存在，查询这些部门的编号和名称。
SELECT * FROM t_department
WHERE EXISTS(SELECT * FROM t_employee WHERE t_employee.did = t_department.did);

#查询结果等价于下面的sql
SELECT DISTINCT t_department.*
FROM t_department INNER JOIN t_employee
ON t_department.did = t_employee.did;
```

## 14.4 SELECT的FROM中嵌套子查询

当子查询结果是多列的结果时，通常将子查询放到FROM后面，然后采用给子查询结果取别名的方式，把子查询结果当成一张“动态生成的临时表”使用。

```mysql
#子查询嵌套在from后面
/*
当一个查询要基于另一个查询结果来筛选的时候，
另一个查询还是多行多列的结果，那么就可以把这个查询结果当成一张临时表，
放在from后面进行再次筛选。

*/

#（1）在“t_employee”表中，查询每个部门的平均薪资，
#然后与“t_department”表联合查询
#所有部门的部门编号、部门名称、部门平均薪资。

SELECT did,AVG(salary) FROM t_employee GROUP BY did;

+------+-------------+
| did  | AVG(salary) |
+------+-------------+
|    1 |  11479.3125 |
|    2 |       13978 |
|    3 |    37858.25 |
|    4 |       12332 |
|    5 |       11725 |
+------+-------------+
5 ROWS IN SET (0.00 sec)

#用上面的查询结果，当成一张临时表，与t_department部门表做联合查询
#要给这样的子查询取别名的方式来当临时表用，不取别名是不可以的。
#而且此时的别名不能加""
#字段的别名可以加""，表的别名不能加""

SELECT t_department.did ,dname,AVG(salary)
FROM t_department LEFT JOIN (SELECT did,AVG(salary) FROM t_employee GROUP BY did) temp
ON t_department.did = temp.did;
#错误，from后面的t_department和temp表都没有salary字段，
#SELECT t_department.did ,dname,AVG(salary)出现AVG(salary)是错误的

SELECT t_department.did ,dname,pingjun
FROM t_department LEFT JOIN (SELECT did,AVG(salary) AS pingjun FROM t_employee GROUP BY did) temp
ON t_department.did = temp.did;


#（2）在“t_employee”表中查询每个部门中薪资排名前2的员工姓名、部门编号和薪资。
SELECT * FROM (
SELECT ename,did,salary,
DENSE_RANK() over (PARTITION BY did ORDER BY salary DESC) AS paiming
FROM t_employee) temp
WHERE temp.paiming <=2;
```

## 14.5 UPDATE中嵌套子查询

当update的表和子查询的表是同一个表时，需要将子查询的结果用临时表的方式表示，即再套一层子查询，使得update和最外层的子查询不是同一张表

```mysql
#子查询也可以嵌套在update语句中
#（1）修改“t_employee”表中部门编号（did）和
#“测试部”部门编号（did）相同的员工薪资为原来薪资的1.5倍。
UPDATE t_employee
SET salary = salary * 1.5
WHERE did = (SELECT did FROM t_department WHERE dname = '测试部');

#（2）修改“t_employee”表中did为NULL的员工信息，
#将他们的did值修改为“测试部”的部门编号。
#子查询select did from t_department where dname = '测试部'
#这种子查询必须是单个值，否则无法赋值

UPDATE t_employee 
SET did = (SELECT did FROM t_department WHERE dname = '测试部')
WHERE did IS NULL;

#（3）修改“t_employee”表中“李冰冰”的薪资值等于“孙红梅”的薪资值。
#这里使用子查询先在“t_employee”表中查询出“孙红梅”的薪资。
#select salary from t_employee where ename = '孙红梅';

UPDATE t_employee
SET salary = (SELECT salary FROM t_employee WHERE ename = '孙红梅')
WHERE ename = '李冰冰';
#You can't specify target table 't_employee' for update in FROM clause'

UPDATE t_employee
SET salary = (SELECT salary FROM(SELECT salary FROM t_employee WHERE ename = '孙红梅')temp)
WHERE ename = '李冰冰';
#当update的表和子查询的表是同一个表时，需要将子查询的结果用临时表的方式表示
#即再套一层子查询，使得update和最外层的子查询不是同一张表

#（4）修改“t_employee”表“李冰冰”的薪资与她所在部门的平均薪资一样。
#子查询，查询李冰冰的部门编号 
#select did from t_employee where ename = '李冰冰';

#子查询第二层，查询李冰冰所在部门的平均薪资
#select avg(salary) from t_employee where did = (select did from t_employee where ename = '李冰冰');

#子查询第三层，把第二层的子查询结果当成临时表再查一下结果
#目的使得和外层的update不是同一张表
SELECT pingjun FROM (SELECT AVG(salary) pingjun FROM t_employee WHERE did = (SELECT did FROM t_employee WHERE ename = '李冰冰') temp)

#update更新
UPDATE t_employee
SET salary = 
(SELECT pingjun FROM 
	(SELECT AVG(salary) pingjun FROM t_employee WHERE did = 
		(SELECT did FROM t_employee WHERE ename = '李冰冰') ) temp)
WHERE ename = '李冰冰';
```

## 14.6 DELETE中嵌套子查询

```mysql
#delete语句中也可以嵌套子查询
#（1）从“t_employee”表中删除“测试部”的员工记录。
DELETE FROM t_employee 
WHERE did = (SELECT did FROM t_department WHERE dname = '测试部');


#（2）从“t_employee”表中删除和“李冰冰”同一个部门的员工记录。
#子查询 “李冰冰”的部门编号
#select did from t_employee where ename = '李冰冰';

DELETE FROM t_employee WHERE did = (SELECT did FROM t_employee WHERE ename = '李冰冰');
#You can't specify target table 't_employee' for update in FROM clause'
#删除和子查询是同一张表

DELETE FROM t_employee WHERE did = (SELECT did FROM (SELECT did FROM t_employee WHERE ename = '李冰冰')temp);
```

## 14.7 使用子查询复制表结构和数据

```mysql
#演示通过子查询复制表，
#（1）复制表结构
#（2）复制一条或多条记录
#（3）同时复制表结构和记录
#仅仅是复制表结构，可以用create语句
CREATE TABLE department LIKE t_department;

#使用INSERT语句+子查询，复制数据，此时INSERT不用写values
INSERT INTO department (SELECT * FROM t_department WHERE did<=3);

#同时复制表结构+数据
CREATE TABLE d_department AS (SELECT * FROM t_department);
#如果select后面是部分字段，复制的新表就只有这一部分字段
```

# 

# 15、事务

## 15.1 事务的特点

**1、事务处理**（事务操作）：**保证所有事务都作为一个工作单元来执行，即使出现了故障，都不能改变这种执行方式。当在一个事务中执行多个操作时，要么所有的事务都被提交(commit)，那么这些修改就永久地保存下来；要么数据库管理系统将放弃所作的所有修改，整个事务回滚(rollback)到最初状态。**

2、事务的ACID属性：

（1）**原子性（Automicity）**
原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。 

（2）**一致性（Consistency）**
事务必须使数据库从一个一致性状态变换到另外一个一致性状态。

（3）**隔离性（Isolation）**
事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。

（4）**持久性（Durability）**
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响，即不能在回滚

```mysql
/*
原子性：
例如：
张三给李四转账500
张三转账之前余额是1000
李四转账之前余额是1000
成功：张三账号变为500，李四变为1500，
失败：张三账号是1000，李四还是1000.


#一致性
例如：
张三给李四转账500
张三转账之前余额是1000
李四转账之前余额是1000

要么他俩的余额不变， 还是1000，总和就是2000
要么他俩的余额有变化，张三500，李四1500，总和仍然是2000
错误：
张三500，李四1000，总和是1500，结果不对
张三1000，李四1500，总和是2500，结果不对

#隔离性
例如：张三要给李四转500，
      王五要给李四转500，
      张三转账是否成功，和王五是否转账成功无关。
      
      
#持久性：
例如：张三要给李四转500，一旦成功提交，就转账成功，撤不回来了。
      */
```

## 15.2  事务的开启、提交、回滚

MySQL默认情况下是自动提交事务。
每一条语句都是一个独立的事务，一旦成功就提交了。一条语句失败，单独一条语句不生效，其他语句是生效的。

### 15.2.1 手动提交模式

```mysql
#开启手动提交事务模式
set autocommit = false;  或  set autocommit = 0;

上面语句执行之后，它之后的所有sql，都需要手动提交才能生效，直到恢复自动提交模式。

#恢复自动提交模式
set autocommit = true; 或 set autocommit = 1;
```

例如：

```mysql
SET autocommit = FALSE;#设置当前连接为手动提交模式

UPDATE t_employee SET salary = 15000 
WHERE ename = '孙红雷';

COMMIT;#提交
```

例如：

```mysql
SET autocommit = FALSE;#设置当前连接为手动提交模式

UPDATE t_employee SET salary = 15000 
WHERE ename = '孙红雷';

#后面没有提交，直接关了连接，那么这句修改没有生效
```

### 15.2.2 自动提交模式下开启事务

```mysql
/*
也可以在自动提交模式下，开启一个事务。
(1)start transaction;

....

(3)commit; 或 rollback;   
在(1)和(3)之间的语句是属于手动提交模式，其他的仍然是自动提交模式。
*/

START TRANSACTION; #开始事务

UPDATE t_employee SET salary = 0 
WHERE ename = '李冰冰'; 

#下面没有写commit;那么上面这句update语句没有正式生效。
commit;#提交

START TRANSACTION;
DELETE FROM t_employee;
ROLLBACK; #回滚
```

### 15.2.3 DDL语句不支持事务

```mysql
#说明：DDL不支持事务
#DDL：create,drop,alter等创建库、创建表、删除库、删除表、修改库、修改表结构等这些语句不支持事务。
#换句话只对insert,update,delete语句支持事务。
TRUNCATE 表名称; 清空整个表的数据，不支持事务。 把表drop掉，新建一张表。

START TRANSACTION;
TRUNCATE t_employee;
ROLLBACK; #回滚  无效
```

## 14.3 事务的隔离级别

```mysql
/*
mysql支持四个隔离级别：
read-uncommitted：会出现脏读、不可重复读、幻读
read-committed：可以避免脏读，会出现不可重复读、幻读
repeatable-read：可以避免脏读、不可重复读、幻读。但是两个事务不能操作（写update,delete）同一个行。
serializable：可以避免脏读、不可重复读、幻读。但是两个事务不能操作（写update,delete）同一个表。

修改隔离级别：
set transaction_isolation='隔离级别';  
#mysql8之前 transaction_isolation变量名是 tx_isolation

查看隔离级别：
select @@transaction_isolation;
*/
```

**数据库事务的隔离性**：数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题。**一个事务与其他事务隔离的程度称为隔离级别。**数据库规定了多种事务隔离级别, 不同隔离级别对应不同的干扰程度, 隔离级别越高, 数据一致性就越好, 但并发性越弱。

- 脏读：一个事务读取了另一个事务未提交数据；
- 不可重复读：同一个事务中前后两次读取同一条记录不一样。因为被其他事务修改了并且提交了。
- 幻读：一个事务读取了另一个事务新增、删除的记录情况，记录数不一样，像是出现幻觉。

**数据库提供的 4 种事务隔离级别：**

| 隔离级别         | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| read-uncommitted | 允许A事务读取其他事务未提交和已提交的数据。会出现脏读、不可重复读、幻读问题 |
| read-committed   | 只允许A事务读取其他事务已提交的数据。可以避免脏读，但仍然会出现不可重复读、幻读问题 |
| repeatable-read  | 确保事务可以多次从一个字段中读取相同的值。在这个事务持续期间，禁止其他事务对这个字段进行更新。可以避免脏读和不可重复读。但是幻读问题仍然存在。注意：mysql中使用了MVCC多版本控制技术，在这个级别也可以避免幻读。 |
| serializable     | 确保事务可以从一个表中读取相同的行，相同的记录。在这个事务持续期间，禁止其他事务对该表执行插入、更新、删除操作。所有并发问题都可以避免，但性能十分低下。 |

* Oracle 支持的 2 种事务隔离级别：**READ-COMMITED**, SERIALIZABLE。 Oracle 默认的事务隔离级别为: READ COMMITED 。
* Mysql 支持 4 种事务隔离级别。 Mysql 默认的事务隔离级别为: **REPEATABLE-READ**。在mysql中REPEATABLE READ的隔离级别也可以避免幻读了。

![image-20211202002655521](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211202002655521.png)

![image-20211202002704464](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211202002704464.png)

![image-20211202002714841](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211202002714841.png)

![image-20211202002723256](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211202002723256.png)

# 16、用户管理

## 16.1 用户管理的目标

1、登录验证：主机IP地址+用户名+密码三重验证

IP地址可以是一个明确的IP（例如：192.168.1.25），可以是某个IP段（例如：192.168.1.%)，可以是任意IP地址（%)。

```mysql
mysql -h mysql服务器的IP地址 -P端口号 -u用户名 -p
Enter password: ******
```

用户的IP是客户端主机的IP和mysql服务器的IP地址不一样。

### 演示用户的创建

例如：mysql服务器的IP地址是192.168.31.152，使用下面的命令登录是错误的。

```mysql
mysql -h192.168.31.23 -P端口号 -u用户名 -p
Enter password: ******
```

因为mysql服务器不在192.168.31.23上。

![image-20211201195333403](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201195333403.png)

刚才创建了一个用户：

用户名：yan，主机：192.168.31.%，表示可以从192.168.31段的其他客户端都可以连接到192.168.31.152机器上的mysql服务。

用户名：lin，主机：%，表示可以从任意一台主机的客户端都可以连接到192.168.31.152机器上的mysql服务。

用户名：chai，主机：192.168.31.23，表示只能从192.168.31.23主机的客户端，连接到192.168.31.152机器上的mysql服务。



2、权限管理

- 全局权限
- 数据库权限
- 数据表权限
- 字段权限
- 存储过程或函数子程序的权限

对用户的操作进行逐级权限验证，如果上一级有这个权限，下一级就不用验证了。

## 16.2  用户管理演示

当具有权限管理的用户通过SQLyog图形界面工具连接MySQL服务之前，可以按照如下步骤进行用户和权限管理。

第1步，选择工具栏中的用户管理器工具按钮，打开用户管理界面。

 ![image-20211201004001116](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004001116.png)



第2步，如果要创建新用户，选择“添加新用户”按钮，弹出新用户信息填写窗口。用户名和主机文本框必须填写，其他项可以不填写，按照默认值处理。如果密码和再一次输入密码框为空，表示密码为空。如果要设置密码必须保证密码框和再一次输入密码框输入相同字符，并在Plugin选择合适的插件“caching_sha2_password”或“mysql_native_password”，默认是“caching_sha2_password”插件。如果需要还可以在下面填写用户资源限制参数，默认值是0表示不限制。

 ![image-20211201004014348](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004014348.png)



​    第3步，如果要修改用户信息，可以直接在“用户”下拉列表中选择用户，然后在右边直接修改用户信息。

 ![image-20211201004021884](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004021884.png)



第4步，如果是对已有的用户进行授权操作，或撤销已有用户的授权，可以直接在“用户”下拉列表中选择用户，然后在左下方选择权限等级，右边对应权限打对勾表示授予该项权限，不打对勾表示不授予该项权限。

![image-20211201004028884](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004028884.png)

![image-20211201004033078](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004033078.png)

![image-20211201004038719](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004038719.png)

![image-20211201004046096](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004046096.png)

![image-20211201004054099](https://dum1615.oss-cn-chengdu.aliyuncs.com/image-20211201004054099.png)

# 17、MySQL8的新特性（部分）

## 1、系统表全部为InnoDB表

从 MySQL 8.0 开始，mysql 系统表和数据字典表使用 InnoDB 存储引擎，存储在 MySQL 数据目录下的 mysql.ibd 表空间文件中。在 MySQL 5.7 之前，这些系统表使用 MyISAM 存储引擎，存储在 mysql 数据库文件目录下各自的表空间文件中。关于数据库存储引擎的详细内容，在MySQL高级课程讲解。

在MySQL5.7版本中查看系统表类型，结果如下：

```mysql
mysql> #MySQL5.7
mysql> #查看系统表类型
mysql> SELECT DISTINCT(ENGINE) FROM information_schema.tables;
+----------------------+
| ENGINE             		|
+----------------------+
| MEMORY             		|
| InnoDB             		|
| MyISAM             		|
| CSV                		|
| PERFORMANCE_SCHEMA 	|
| NULL               		|
+----------------------+
6 rows in set (0.04 sec)
```

在MySQL8.0版本中查看系统表类型，结果如下：

```mysql
mysql> #MySQL8.0
mysql> #查看系统表类型
mysql> SELECT DISTINCT(ENGINE) FROM information_schema.tables;
+--------------------+
| ENGINE                |
+--------------------+
| InnoDB                |
| NULL                  |
| PERFORMANCE_SCHEMA |
| CSV                   |
+--------------------+
4 rows in set (0.00 sec)
```

系统表全部换成事务型的InnoDB表，默认的MySQL实例将不包含任何MyISAM表，除非手动创建MyISAM表。

## 2、默认字符集改为utf8mb4

在8.0版本之前，MySQL默认的字符集为Latin1，而8.0版本默认字符集为utf8mb4。

Latin1是ISO-8859-1的别名，有些环境下写作Latin-1。ISO-8859-1编码是单字节编码，不支持中文等多字节字符，但向下兼容ASCII。

MySQL中utf8字符集，它是utf8mb3的别称，使用三个字节编码表示一个字符。自MySQL4.1版本被引入，能够支持绝大多数语言的字符，但依然有些字符不能正确编码，如emoji表情字符等，为此MySQL5.5引入了utf8mb4字符集。在MySQL5.7对utf8mb4进行了大幅优化，并丰富了校验字符集。mb4就是“most byte 4”的意思，专门用来兼容四字节的Unicode，utf8mb4编码是utf8编码的超集，兼容utf8，并且能存储4字节的表情字符。如果原来某些库和表的字符集是utf8，可以直接修改为utf8mb4，不需要做其他转换。但是从uft8mb4转回utf8就会有问题。

使用SHOW语句查看MySQL5.7版本数据库的默认编码。

```mysql
mysql> #查看MySQL5.7数据库的默认编码
mysql> SHOW VARIABLES LIKE 'character_set_database';
+------------------------+--------+
| Variable_name            | Value  |
+------------------------+--------+
| character_set_database | latin1 |
+------------------------+--------+
1 row in set, 1 warning (0.00 sec)
```

使用SHOW语句查看MySQL8.0版本数据库的默认编码。

```mysql
mysql> #查看MySQL8.0数据库的默认编码
mysql> SHOW VARIABLES LIKE 'character_set_database';
+------------------------+---------+
| Variable_name            | Value   |
+------------------------+---------+
| character_set_database | utf8mb4 |
+------------------------+---------+
1 row in set, 1 warning (0.00 sec)
```

字符集校对规则是在字符集内用于字符比较和排序的一套规则，比如有的规则区分大小写，有的则无视。校对规则特征：

- 两个不同的字符集不能有相同的校对规则。
- 每个字符集有一个默认校对规则。
- 校对规则存在命名约定，以其相关的字符集名开始，中间包括一个语言名，并且以_ci、_cs或_bin结尾。其中_ci表示大小写不敏感、_cs表示大小写敏感、bin表示直接比较字符的二进制编码，即区分大小写。

使用SHOW语句查看utf8mb4字符集的部分校对规则如下：

```mysql
mysql> SHOW COLLATION LIKE 'utf8mb4_0900%';
+-----------+---------+-----+---------+----------+---------+--------------+
| Collation  | Charset | Id  | Default | Compiled | Sortlen | Pad_attribute|
+-------------------+---------+-----+---------+----------+-------+--------+
|utf8mb4_0900_ai_ci	| utf8mb4 | 255 | Yes    | Yes     |   0 | NO PAD        	|
|utf8mb4_0900_as_ci	| utf8mb4 | 305 |         | Yes     |   0 | NO PAD        	|
|utf8mb4_0900_as_cs	| utf8mb4 | 278 |         | Yes     |   0 | NO PAD        	|
|utf8mb4_0900_bin	| utf8mb4 | 309 |         | Yes     |   1 | NO PAD        	|
+-------------------+---------+-----+---------+----------+---------+------+
4 rows in set (0.00 sec)
```

## 3、支持检查约束（见上面检查约束）

## 4、支持窗口函数（见上面窗口函数）

## 5、用户管理

在MySQL 8.x中，默认的身份认证插件是“caching_sha2_password”，替代了之前的“mysql_native_password”。可以通过系统变量default_authentication_plugin和mysql数据库中的user表来看到这个变化。

在MySQL8之前默认的身份插件是“mysql_native_password”，即MySQL用户的密码使用PASSWORD函数进行加密。在MySQL 8.x中，默认的身份认证插件是“caching_sha2_password”，替代了之前的“mysql_native_password”，PASSWORD函数被弃用了。

在MySQL版本5.6.6版本起，在mysql.user表中添加了“password_expired”字段，它允许设置密码是否失效。如果“password_lifetime”字段值不为NULL，那么从MySQL服务启动时间开始，经过“password_lifetime”字段值的时间间隔之后，密码就过期了，即“password_expired”字段就为“Y”。任何密码超期的账号想要连接服务器端进行数据库操作都必须更改密码。MySQL8.0版本允许数据库管理员手动设置账户密码过期时间。

从MySQL 8.x版本开始允许限制重复使用以前的密码。

在MySQL8之前，如果要给多个用户授予相同的角色，需要为每个用户单独授权。在MySQL8之后，可以为多个用户赋予统一的角色，然后给角色授权即可，角色可以看成是一些权限的集合，这样就无须为每个用户单独授权。如果角色的权限修改，将会使得该角色下的所有用户的权限都跟着修改，这就非常方便。

mysql的密码字段有变化：

- mysql5.7之前mysql系统库的user表，密码字段名是password
- mysql5.7版本mysql系统库的user表，密码字段名是authentication_string

- mysql8.0版本mysql系统库的user表，密码字段名是authentication_string，另外用户管理还有角色概念，mysql系统库中有default_roles表。

## 6、其他新特性（略）

通用表达式、计算列、DDL操作支持原子性、数据字典合并等等。

通用表达式简称为CTE（Common Table Expressions）。CTE是命名的临时结果集，作用范围是当前语句。CTE可以理解为一个可以复用的子查询，但是和子查询又有区别，一个CTE可以引用其他CTE，CTE还可以是自引用(递归CTE)，也可以在同一查询中多次引用，但子查询不可以。

```mysql
WITH [RECURSIVE]
 cte_name [(col_name [, col_name] ...)] AS (subquery)
 [, cte_name [(col_name [, col_name] ...)] AS (subquery)] ...
```

通用表达式以“WITH”开头，如果“WITH”后面加“RECURSIVE”就表示接下来在通用表达式中需要递归引用自己，否则就不递归引用。每一个通用表达式都需要有一个名字，它相当于是子查询结果集的名字。

```mysql
#（1）在“t_employee”表中查询每个人薪资和公司平均薪资的的差值。
WITH temp AS (SELECT ROUND(AVG(salary),2) AS pingjun FROM t_employee)
SELECT ename AS "员工姓名",
    salary AS "薪资",
    pingjun "公司平均薪资",
    ROUND(salary - pingjun,2) "差值"
FROM t_employee,temp
HAVING ABS(差值)>5000;

#（2）查询薪资低于9000的员工编号，员工姓名，员工薪资，领导编号，领导姓名，领导薪资
WITH 
emp AS (SELECT eid,ename,salary,`mid` FROM t_employee WHERE salary <9000),
mgr(meid,mename,msalary) AS (SELECT eid,ename,salary FROM t_employee)
  
SELECT eid AS "员工薪资",
	ename AS "员工姓名",
	salary AS "员工薪资",
	meid AS "领导编号",
	mename AS "领导姓名",
	msalary AS "领导薪资"
FROM emp INNER JOIN mgr ON emp.mid = mgr.meid;

#（3）查询eid为21的员工，和他所有领导，直到最高领导。
CREATE TABLE emp AS (SELECT eid,ename,salary,tel,`mid` FROM t_employee WHERE salary < 10000);
UPDATE emp SET MID=19 WHERE eid=21; 
UPDATE emp SET MID=17 WHERE eid=19; 
UPDATE emp SET MID=16 WHERE eid=17; 
UPDATE emp SET MID=15 WHERE eid=16;
UPDATE emp SET MID=4 WHERE eid=15; 
UPDATE emp SET MID=NULL WHERE eid=4;
SELECT * FROM emp;


WITH RECURSIVE cte
AS ( 
	SELECT eid,ename,`mid`
 	FROM emp
 	WHERE eid = 21

UNION ALL

 	SELECT emp.eid,emp.ename,emp.mid
 	FROM emp INNER JOIN cte
 	ON emp.eid = cte.mid
 	WHERE emp.eid IS NOT NULL
)
SELECT * FROM cte;
```
