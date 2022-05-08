[toc]

# MySQL Study

### 第1章 数据库的服务与连接

##### MySQL服务的启动与停止

```cmd
::启动mysql57服务
net start mysql57
```

```cmd
::停止mysql57服务
net stop mysql57
```

##### 连接MySQL

```cmd
mysql -u root -p
->123456
```



##### 创建Data文件夹（在安装目录下）

```cmd
cd mysql server文件夹位置   ::cd 到mysql server文件夹位置

->mysqld --initialize-insecure --user=root
```



### 第2章 数据库仓库的操纵

##### 显示所有数据库

```mysql
show databases;
```

##### 创建数据库

```mysql
create database + 数据库名称 ;
```



##### 更规范的创建方式

```mysql
create database if not exists + 数据库名称 ;
```



##### 示例

```mysql
create database if not exists student;
```



##### 删除数据库

```mysql
drop database + 数据库名称 ;
```



##### 删除一个是否存在的数据库（更规范）

```mysql
drop database if exists + 数据库名称
```



##### 查看当时自己创建的其中数据库

```mysql
show create database + 数据库名称 ;
```



##### 字符集编码问题

**在实际开发过程中，坚决不可使用gbk的字符编码**，但是学习使用，win10下的cmd使用的是gbk，故**每次创建数据库时应该在后面补上字符集编码**

##### 示例

```mysql
create database if not exists students **charset=gbk** ;
```





### 第3章 数据库中表的操作

##### 引用数据库

```mysql
use chen_school;
```

(则之后将进入该数据库中操作)

##### 显示该数据库中所有表

```mysql
show tables;
```



##### 创建一个表

```mysql
create table student( #表的名称
->id int,  #学号  学号类型
->name	varcahr(30), #（名字）（名称属于字符串varchar，给定最大宽度为30）
->age  int
->);
```

##### 更规范的创建方式

```mysql
create table if not exists teacher(
#auto_increment表示自动增长
# comment 意为注释，后面单引号内容是解释前面定义的字段（比较标准的写法）
#字段：id  name  age  phone  address...
#primary key 设置该字段是主键，就是唯一的，不能重复，必须要填，主要就是靠主键来区分表中内容
#not null 表示该字段不能为空，这里表示老师的名字不能为空
#default 表示该字段未填写时，自动填写后面单引号的默认值
->id int auto_increment primary key comment '主键id', 
->name varcharm(30) not null comment '老师的名字',
->phone varchar(100) comment '电话号码',
->address varchar(100) defualt '暂时未知' comment '住址'
->)engine=innodb;    #数据库引擎
```

##### 查看表的结构

```mysql
desc teacher;  # desc + 表的名称
#与show不同的是，desc是显示这张表的结构，show是展示当时如何创建的这张表
```

##### 删除表

```mysql
drop table if exists teacher;  #推荐
#drop teacher;
```

##### 修改表

```mysql
#alter 表示修改
alter table student add phone varchar(20); #在表后面再添加一个字段phone

alter table student add gender varchar(1) after name;#添加字段放在指定字段后面

alter table student add add address varchar(100) first;#将添加字段放在第一位

alter table student drop address; #删除指定字段

#修改表中字段名和字段类型（phone->tel_phone,varchar->int）
alter table student change phone tel_phone int(11);

alter table student modify tel_phone varchar(13);#modify 只能用来改类型，change可以改名字跟类型

alter table student renname students;  #rename 修改表名
```



### 第4章 对数据的操作

##### 向表中添加数据

```mysql
insert into teacher (id,name,phone,address) values(1,'chen','18888','Shanghai');
#values括号中数据与前面一一对应

#可以不按照表的顺序来添加,values后面依然保持与前面一一对应
insert into teacher (name,phone,address,id) values('chen','18888','Shanghai',1);

#如果省略字段名称，那么values必须按照表的顺序来插入数据，不能为空的值必须填
insert into teacher (3,'Tom','100000','NanJing');


insert into teacher (NULL,'Tom',NULL,default);
#自增的字段(id)可以填NULL跟随系统即可
#name设计之初定义不能为空，故不可以为NULL
#phone设计之初定义可以为空，可以填NULL
#address设计之初定义default '暂时未知',故可以填写default

```

##### 查询表中数据

```mysql
#该方法有性能问题
select * from teacher;#查询这张表所有的数据 
#select 选择
# * 所有数据
#from 从哪张表
```

##### 字符编码问题

character_set_client 和 char_set_results字符编码必须为gbk，否则可能出现乱码问题

如果出现乱码，更改编码即可

```mysql
show variables like 'character_set_%';#查看字符集编码

#更改客服端与输出端编码
set character_set_client=gbk;
set character_set_results=gbk;
```



##### 插入多条语句

```mysql
#两条数据间用逗号隔开即可
insert into teacher values(NULL,'Tom_1',NULL,defualt),(NULL,'Jerry',NULL,defualt);

```

##### 删除数据

```mysql
#删除具体一个表中的数据
delete from students where id=9;  #删除id=9的
#用其余属性也是可以的
delete from students where name="Tom";  #会删除同名，建议删除唯一属性的标志
```

##### 清空表

```mysql
#delete 清空表中数据 (不推荐，当数据过于庞大，计算量更大，性能不好)
delete from students;

#truncate 将原先的表直接干掉，然后重新创建一个一模一样的表，空的
truncate table students;

```

##### 小细节

```mysql
#通过上面的 delete 已经将表中数据全部删除，但是当再次向表中写入数据时，id会跟随之前的id继续叠加，但是truncate不会出现这样的情况
```

##### 更新数据

```mysql
#将students这张表中id=1的名字更改为'frank'
#set 设置
update students set name='frank' where id=1;

update students set name='frank' where id=1 or phone='1111'  #满足一个条件即可

#如果后面不加where条件 那么会更改所有数据！
```

##### 查询表中数据（基本）

```mysql
#可以选择需要查询的内容(通过字段查询)

select phone,address from students;

#查询全部的
select * from students;
```

##### 数据库语句区分

```mysql
DDL data definition language ->数据库定义语言(给数据库用的) create alter drop show

DML data manipulation language ->数据库操作语言(给数据用的) select insert delete update

DCL data control language ->数据库控制语言
```





### 第5章   数据类型









### 第6章  列属性完整性

#####  Primary Key  主键

```mysql
#主键，即必须要有，不能为空，绝对性，唯一性，不可重复性，且能确定这项数据存在；例如身份证号，去任何部门都可通过该身份证号查询该主键的所有信息；
#主键最大的的作用：保证数据的完整性；加快查询资料的速度
#主键的特点：不可能为重复；不可能为空，不能为NULL

#当一张表没有主键可以后续添加
alter table teacher add primary key (id);
```

##### 删除、添加组合键、选择主键

```mysql
alter table teacher drop primary key; #删除主键

alter table teacher add primary key (id,name);#添加多个主键（不推荐添加多个，最好一个）
```

##### 复合主键

例如网站用户注册，用户id唯一，是主键，但是当注册昵称时，昵称必须唯一，不能重复，这就是组合键，但是只能有一个主键，实质上可以理解为两个字段构成了一个主键。

##### unique  唯一键

主键可能是与其他数据库相关联的，是其他数据库的主键，但是唯一键只是单个数据表中的键，可以为NULL，一个表中可以有多个唯一键；用处：保证数据不重复

```mysql
create table t_9(
->id int primary key,
->phone varchar(20) unique  #添加唯一键
->)
->;

#添加唯一键
alter table teacher add unique(phone);

#删除唯一键
alter table teacher drop index phone;
```

##### 主键和唯一键的区别

主键不能重复，    不能为NULL，主键只有一个，哪怕是组合键，主键可能会被其他表应用

唯一键不能重复，可以为NULL，唯一键可以有多个，                   唯一键只能在该表被使用

#####  数据库完整性

1）应当要有一个主键；

2）选择合适的数据类型

3）有些字段可以为空，有些可以不为空

4）有些字段必须要有defualt；比如考试，缺考应当时defualt，不是0分

5）可能需要对外部进行引用，比如这张表的字段可能被别的表引用



##### 外键

以学生到食堂买饭为例，学生在学生表中有id，买饭在食堂交易也存在学生id

```mysql
#首先创建学生表
create table stu(
->stuId int(4) primary key,
->name varhchar(20)
->);

#创建食堂交易表
create table eatery(
->id int primary key,
->money decimal(10,4),    #设置有效数字以及小数点后多少位
->stuId int(4),
->foreign key (stuId) refenrences stu(stuId)#设置外键，外键是谁，它来自哪张表
->);

#在已有表上添加外键
alter table eatery add foreign key (stuI  d) refenrences stu(stuId);
```

##### 删除外键

```mysql
#在删除之前应先展示之前如何创建外键的，因为外键名未知
show create table eatery;
#删除外键
alter table eatery drop foreign key 外键名 ;

#更正之前错误，PRI表示主键，UNI表示唯一键，但是MUL不是外键的意思，而是可重复的意思

```

##### 外键三种操作

1）严格性操作

2）置空操作：当id为5的学生被学校开除，那么食堂表中学生5的信息全部置空，变为NULL，意思是学生虽然被学校开除，但是这个学生在食堂花过钱，食堂的销售额不能因为学生的开除而删除该学生的消费额，即学生id被删除了，但是食堂信息不因此而改变

3）级联操作：id为5的学生被强制要求删除所有信息，包括食堂消费信息

所以删除时候一般采用置空操作

##### 级联操作演示

```mysql
create table stu(
->stuId int(4) primary key,
->name varchar(20)
->);

create table eatery(
->id int(20) primary key,
->money decimal(10,4),
->stuId int(4),
            #与主表进行绑定，如果删除，进行置空操作，如果更新，进行级联
->foreign key(stuId) references stu(stuId) on delete set null on update cascade
->);

insert into stu values(1,'frank');
insert into stu values(2,'jerry');
selete stuId,name from stu;

#给足订单
insert into eatery values(1,18.13,2);
insert into eatery values(2,12.34,1);
insert into eatery values(3,15.34,1);
insert into eatery values(4,34.65,2);
insert into eatery values(5,23.56,1);

#修改订单，将'frank'改成4号
update stu set stuId='4' where name='frank';

select * from stu;
select * from eatery;

#删除学生jerry
delete from stu where stuId='2';
select * from stu;
select * from eatery;

```

### 第 7 章 数据库设计思路4 

##### 关系

关系型数据库，两张表的共有字段去确定数据的完整性

##### 行

一条数据 ，一条记录  实体

##### 列

一个字段  属性

##### Codd第一范式

确保每列字段原子性，不能再分了，例如2018-2019是可以再分的，开始时间为2018单独拿出来，结束时间2019单独拿出来；省市地区县都要分开

##### Codd第二范式

一张表只能描述一件事

##### Codd第三范式

消除传递依赖  高考查分，语数外综合，那么高考总分就是多余字段，可能考虑是否需要删除总分这个字段，实际情况中，高考总分必须存在，提高性能



### 第 8 章  单表查询

##### select

```mysql
select '去你妈的'; #输出字段跟数据都是去你妈的

select 2*7; #2*7作为字段名，值是结果为14

select 'Go fuck yourself' as qnmd;#字段名是qnmd，数据为Go... 

select 2*7 as result; #字段名为result，数据为结果14

# as 是起一个别名，如果没有as 那么2*7本身会作为一个字段显示，如果有as，那么后面的别名会作为一个字段
```

##### from

意为来自谁？来自哪张表 

```mysql
#创建两张数据表t1，t2
create table t1(
->id int,
->name varchar(20)
->);

create teble t2(
->score1 int(20),
->score2 int(20)
->);

#向t1添加数据
insert into t1 values(1,'frank');
insert into t1 values(2,'Jerry');
#向t2添加数据
insert into t2 values(99,78);
insert into t2 values(100,67);

select * from t1,t2;#两张表同时查，返回一个笛卡尔积 

```

##### dual

```mysql
select 2*7 as result from dual;#存在默认的伪表 伪表：dual
```

##### where

```myslq
select * from teacher where age<18;

select * from teacher where address='Shanghai' or address='Beijing';

```

##### in

```mysql
select * from teacher where address in('Shanghai','Beijing');
```



##### between...and

```mysql
select * from teacher where age>=18 and age<20;#查询年龄在18到20之间的数据(不包括20)

#更有逼格
select * from teacher where age (not)between 15 and 20;#包括了20
```



##### is null

```mysql
select * from teacher where age is (not)null;#查询年龄为(not)null的数据
```

##### 聚合函数（自带的函数）

```mysql
#创建分数表
create table score(
->id int,
->chinese int,
->math int,
->english int
->);

insert into score values(98,99,100);
insert into score values(90,80,88);

#求语文总分
select sum(chinese) from score;

#求平均值
select avg(chinese) from score;

#求最大值
select max(chinese) from score;

#统计次数
select count(chinese) from score;

```

##### like模糊查询

```mysql
insert into student values(1,'张三',18,18976876);
insert into student values(2,'张杰',16,1239758);
insert into student values(3,'张翔',13,1534897);
insert into student values(4,'张跃进',33,132445);

#like模糊查询
select * from student where name like '张%';#张后面带%，可以查多个字符，包括两个字名称与三个字名称的人都可以查，

select * from student where name like '张_';#不能查超过俩字的名字，比如张跃进查不出来

```

##### order by排序查询

```mysql
#语文成绩排序
select * from score order by Chinese asc;#asc 表示升序

#降序
select * from score order by Chinese desc;#desc 表示降序

```

##### group by分组查询

```mysql
#创建info表，包含age，gender字段名
#分别统计表中男性和女性的平均年龄
select avg(age) as '年龄',gender as '性别' from info group by gender;
#以上代码表示，age的平均值从info这张表按照gender分组(按照性别分组)

#统计上海地区与北京 地区的平均年龄(按照地区分组)
select avg(age) as '年龄',address as '地区' from info group by address;

#如果使用group by，那么查询的字段必须是分组字段和聚合函数，以上代码模板可写为select 聚合函数，分组字段 from....group by 分组字段;
```

##### group_concat

```mysql
#聚合显示
#将表中所有姓名按照性别分组显示出来
select group_concat(name),gender from student group by gender;

```

##### having

```mysql
#区分where与having：where是在原本建立的表中查询的，而having是对已经查询之后显示的虚拟表再次查询;这个虚拟表本身是不存在的，只是显示结果，在结果的基础上再次查询筛选，我们采用having

#以上面代码段为例，
select avg(age) as '年龄'，address as '地区' from info group by address;
#如果我们想在查询之后的基础上再条件筛选年龄大于28的地区应当在后面补上 having 年龄>28;
```

##### limit

```mysql
select * from info limit 0,2; #0表示从哪条数据开始，一般数据都是从0开始，2表示长度 包括0，往后查2个
```

##### distinct（筛选去重）

```mysql
#查所有地址，去掉重复字段
select distinct address from info;
#甚至可以用count直接显示数量
select count(distinct address) from info;

#其实默认情况下是用all
select all address from info;#all 可以省略
```



### 第 9 章 多表查询

##### union联合查询

```mysql
#两张表联合查询，去除重复字段，加入union
select age gender from info union distinct select name,phone from teacher;
```

##### inner join（内连接）

```mysql
#创建学生表
create table student(
->id int,
->name varchar(20),
->age int(2),
->phone varchar(20)
->);

insert into student values(1,'张三',0,18,12378451,
                          ->2,'张八',1,20,1235491,
                          ->3,'张跃进',0,31,126723,
                          ->4,'高就',1,32,12349452
                          ->);
                          
#新建一张表score成绩表
create table score(
->id int(11) auto_crement primary key,
->stuid int(11) not null comment '学生id',
->score double(5,2) not null
->);

insert into score values(1,89.00);
insert into score values(2,70.00);
insert into score values(3,88.00);
insert into score values(4,67.00);

#从student和score两张表联合查询，联合查询name和score，on后面的意思是两张表的公共字段，显然这两张表的公共字段是学生id，即学生表的id其实是成绩表中的stuid
select name,score from student inner join score on student.id=score.stuid;

#inner join  属于内连接，要求两张表要有公共字段
```

##### inner join注意事项

如果有多张表，可以多次使用inner join 在on之前

##### left join (左连接，左查询)

```mysql
#以左表为基准，以student这张表为基准，以分数表为辅，如果是内查询，但是其中一个人没来考试，分数为null，那么在表中不会显示，但是左查询会以学生为主，哪怕是null也必须显示出来
select name,score from student left join score on student.id=score.stuid;
```

##### right join(右连接，右查询)

```mysql
#与左查询一样，有分数没名字也得显示出来
```



##### cross join(交叉连接，返回一个笛卡尔积)

```mysql
#创建两张表t1,t2
create table t1(
->id int,
->name varchar(20)
->);
insert into t1 values(1,'frank');
insert into t1 values(2,'Jerry');

create table t2(
->id int,
->age int(20)
->);
insert into t2 values(1,18);
insert into t2 values(2,23);
insert into t2 values(3,14);

#cross join交叉查询，返回一个笛卡尔积
select * from t1 cross join t2;

select *from t1 cross join t2 where t1.id=t2.id;#加了where后相当于内连接
```

##### nutural  join自然连接

```mysql
#以上面t1,t2为例
#找公共字段连接,必须保证两张表有一个公共字段名是一模一样的
select * from t1 nutural join t2;

#上面是自然内连接，还有自然左连接与自然右连接，与前面同理，不存在数据则默认补null

#如果两张表没有公共字段，会返回笛卡尔积
```

##### using

```mysql
#如果两张表有多个公共字段，则什么都不返回

#所以使用using指定字段
select * from t1 inner join using(id);
```



### 第 10 章 子查询

##### 子查询基本语法

```mysql
#存在学生表student与成绩表score
#在分数表中，查询成绩大于多少多少的学生
select * from score where score>80;

#但是不想要这个效果，我想查以上学生的所有信息
#把上面语句括起来,再来格select,查询学生表，此时都不用*，用stuid
select * from student where id in (select stuid from score where score>80;)
 这是从学生信息表查询学生所有数据           这是分数表  查询成绩大于80的学生的id

#注意in的用处，适用于返回多个数据,单个结果的话用=号也可
```

##### in和not in

not in 就是查询条件相反

##### exists和not exists

```mysql
#将上面的 id in 换成 exists
select * from student where exists (select stuid from score where score>80;)
#表示如果存在成绩>80的学生,则将整张学生表返回

#not exists就是如果不存在score>80的，返回整张学生表
```





# **基础部分至此。。。结束！！！**









# 高级部分

### View 视图

##### View 创建 使用 作用

```mysql
#采用视图  防止他人看到隐私数据，隐藏表的结构
#某种意义上 减低sql复杂度

#创建一个叫vw_stu的视图，查看student中 name，phone
create view vw_stu as 
select name,phone from student;

#查询视图
select * from vw_stu;
#查询学生表的name和phone 以及对应学生的成绩  view + 视图名称
create view vw_stu_all as 
select name,phone,score from student inner join score on student.id=score.stuId;

```

##### 显示视图

```mysql
#当我们用第三方客户端 navicat创建视图之后，其实在sql终端中是可以显示视图的
show tables;  #可以显示视图
#所以我们在创建视图的时候名称应带有 vw 方便与表进行区别

#语句上  同样可以用通用命令对视图进行操作
#show create view vw_stu;  desc vw_stu;   ....


#有b格的查询视图信息
show table status where comment= 'view' \G;
```

##### 更新和删除视图

```mysql
#与表的操作差不多
#之前创建了一个视图 包含了name，phone，score 
#在此基础上修改视图,变成了只查询student一张表中的name
alter view vw_stu_all as select name from student;

#删除视图
drop view vw_stu_all;
```

##### 视图算法  temptable ( 临时表算法 ) , merge ( 合并算法 ) , undefined ( 未定义 )

```mysql
#运用不同的算法，在子查询时，得到的结果也不一样，我们可以指定用什么算法创建视图
create algorithm=temptable view vw_stu_all as .....;
```

### 事务   transaction

```mysql
#创建一张表 wallet 包含两个字段   id(int)   balance(decimal)
#进行转账操作  一号给二号转账 ：一号你要给二号转账么？ 二号你要接收一号的转账么？ 如果都确定 那就签个协议吧
#事务的操作就是要么一起执行 要么一起回滚

#开启事务
start transaction;
#把id=1的账户减少50元
update wallet set balance=balance-50 where id=1;
#这时候还看不到结果，没有提交

#id=2的账户余额 +50
update wallet set balance=balance+50 where id=2;

#提交事务,余额改变了
commit;
#在提交事务之前是可以回滚所有操作的
rollback;
```

##### rollback to回滚点

```mysql
#类似于虚拟机设置快照的操作 设置回滚点
insert into wallet values(4,1000);
#在此 设置第一个回滚点
savepoint four;

insert into wallet values(5,1999);
#设置第二个回滚点
savepoint five;

#回滚操作  选择回到 four 这个回滚点
rollback four;
#最后提交
commit;

```

##### 事务四大特性  ACID

Atomicity       原子性

Consistency  一致性

Isolation        隔离性

Durability      持久性



##### 注意事项

不是在所有情况下都可以用事务，只有在mysql**数据库引擎为innoDB**时候才可以用。



### 索引 index



索引的**作用**:类似于新华字典  帮助我们快速查询到我们想要的数据

索引的**缺点**:1.如果设置成索引 就意味着你的表**增删改效率会降低  而且不是一般的低**！！

​                    2.索引**占空间**



主键索引： primary key

唯一键索引：unique key

普通索引：既不是主键索引，也不是唯一键索引，就是普通查数据的索引

全文索引 ：给搜索引擎使用

```mysql
#为上一张wallet表设置索引
#普通索引  为 balance 这个字段设置索引
create index balance_index on wallet(balance)

#创建唯一索引 
create unique index balance on t2(score);
#更新索引
alter table balance add balance_index(balance);
#删除索引
drop index balance_index on wallet;

#知道为什么要创建这个索引，首先这个数据经常被查到，比如高考总分，公共字段
```



### 存储过程



