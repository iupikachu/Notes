## Mysql学习笔记



### 1.数据库的列类型



![image-20201119095300815](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119095300815.png)

![image-20201119095536497](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119095536497.png)

![image-20201119095742237](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119095742237.png)

![image-20201119095825823](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119095825823.png)



### 2.数据库的字段属性

![image-20201119100032450](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119100032450.png)





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119100422829.png" alt="image-20201119100422829" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119100507650.png" alt="image-20201119100507650" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119100828863.png" alt="image-20201119100828863" style="zoom:50%;" />





### 3.创建表

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119101744334.png" alt="image-20201119101744334" style="zoom:50%;" />

```sql
CREATE TABLE IF NOT EXISTS  `student` (
    `id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
    `name` VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
    `pwd` VARCHAR(20) Not Null DEFAULT '123456' COMMENT '密码',
    `sex` VARCHAR(2) NOT NULL Default '男' COMMENT '性别',
    `birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
    `address` VARCHAR(100) DEFAULT NULL COMMENT '家庭地址',
    `email` VARCHAR(50) Default NULL COMMENT '邮箱',
    PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8
```



> 查看创建数据库和表的语句

```sql
SHOW CREATE DATABASE `study`; 
SHOW CREATE TABLE `student`;
DESC `student`;   显示表的结构
```





### 4.数据表的类型



#### 4.1 数据库引擎

> INNODB     默认使用 
>
> MYISAM     早些年使用

|            | MYISAM              | INNODB           |
| ---------- | ------------------- | ---------------- |
| 事务支持   | 不支持 （新版支持） | 支持             |
| 数据行锁定 | 不支持              | 支持             |
| 外键约束   | 不支持              | 支持             |
| 全文索引   | 支持                |                  |
| 表空间大小 | 较小                | 较大 （约为2倍） |

常规使用操作：

* MYISAM  节约空间，速度较快
* INNODB  安全性高 事务处理 多用户多表操作

> 物理空间存在的位置

所有的数据库文件都存在data目录下

本质还是文件的存储！

![image-20201119111302011](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119111302011.png)



#### 4.2  字符集编码

![image-20201119111527998](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201119111527998.png)

 



### 5.数据库外键

不允许使用外键与级联，一切外键概念都在应用层解决。

> 如果数据库有外键，那么每次做Update和Delete时都要考虑外键约束，会导致开发的时候很痛苦，测试数据极为不方便。





### 6.  DML语言



#### 6.1 插入

![image-20201120134544566](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201120134544566.png)



> 语法  insert into 表名 （[字段名1],[字段名2],[字段名3]）values   ('值1')，('值2')，('值3')
>
> 字段是可以省略的 但是要一一对应

```sql
insert into student(`id`,`name`,`pwd`,`sex`,`birthday`,`address`,`email`)
values ('1','cqp','123456789','男','1997-08-23','新昌','2111053717@qq.com');
```



#### 6.2 修改



```sql
# 修改名字 带条件
update `student` set `name` = 'iu' where `id`= 2;

# 不指定条件会修改所有
update `student` set `name` = 'iu';

# 修改多个属性 
update `student` set `name` = 'iu',`email`='11111@qq.com' where `id`= 2;
```



#### 6.3 删除



```sql
delete from `student` where id =3 ;
```



> Truncate 命令  完全清空一个数据库表，表的结构和索引约束不会变

```mysql
#     清空student表
TRUNCATE `student`
```



> delete 与 truncate 的区别



相同点: 都能删除数据，都不会删除表结构

不同点：

* truncate 重新 设置 自增列 计数器会归零
* truncate  不会影响事务



### 7.DQL查询数据（最重要）

（Data Query Language） 

```sql
select SYSTEM_USER();
select USER();
select VERSION();
```

![image-20201121170636334](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121170636334.png)

![image-20201121170648604](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121170648604.png)

![image-20201121170702038](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121170702038.png)





![image-20201121151335433](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121151335433.png)





> concat 拼接函数
>
> ```sql
> SELECT CONCAT('姓名:',studentname) AS 名字 from student;
> ```

![image-20201121095922824](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121095922824.png)



> distinct 去重
>
> ```mysql
> SELECT DISTINCT `studentno` from result;
> ```





> 逻辑表达式



![image-20201121102221415](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121102221415.png)







> ### where 条件查询



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121102104346.png" alt="image-20201121102104346" style="zoom:80%;" />







> 模糊查询

![image-20201121104255073](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121104255073.png)



```mysql
-- 查询姓刘的同学
-- like结合 %(代表0到任意个字符)  _（匹配一个字符）
SELECT `studentno`,`studentname` from `student`
where studentname like '刘%';

-- 查询姓刘的同学名字后面只有一个字的
SELECT `studentno`,`studentname` from `student`
where studentname like '刘_';

-- 查询姓刘的同学名字后面只有两个字的
SELECT `studentno`,`studentname` from `student`
where studentname like '刘__';

-- 查询1001 1002 1003 的学员
SELECT `studentno`,`studentname` from `student`
where studentno in (1001,1002,1003);
```





> ### 联表查询



![image-20201121104543747](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121104543747.png)



![62d90499172259fd0f482268d2f8db6b](/Users/chenqipeng/Library/Application Support/typora-user-images/62d90499172259fd0f482268d2f8db6b.jpeg)





> on 和 where 的区别

多表查询时，ON和where都表示筛选条件，on先执行，where后执行。

比如 左外连接。 

on 会先生成左表再连接右表中符合条件的数据。

> 外连接

| 操作       | 描述                             |
| ---------- | -------------------------------- |
| Inner join | 如果表中至少有一个匹配，就返回行 |
| Left join  | 从左表返回所有值，即使右表无匹配 |
| Right join | 从右表返回所有值，即使左表无匹配 |

```mysql
select s.studentno,studentname,subjectname,studentresult
from student  s
right JOIN  result  r
on  s.studentno = r.studentno
INNER join `subject` sub
on r.subjectno = sub.subjectno;
```



> 自连接

自己的表和自己的表连接，核心: **一张表拆为两张一样的表即可**



![image-20201121135146962](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121135146962.png)



拆开



父类

| categoryid | categoryname |
| ---------- | ------------ |
| 2          | 信息技术     |
| 3          | 软件开发     |
| 5          | 美术设计     |
|            |              |

子类

| pid | categoryid | categoryName |
| ---------- | ------------ | ------------ |
| 3 | 4      |数据库|
| 2 | 8 |办公信息|
| 3 | 6 |web开发|
| 5 | 7 |ps设计|

树形结构 

操作：查询父类对应的子类关系

```mysql
select a.`categoryName` as '父栏目', b.`categoryName` as '子栏目'
from `category` as a,`category` as b
where a.categoryid = b.pid;
```

![image-20201121140500566](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121140500566.png)





> 试一试:    查询参加'数据库结构-1' 考试的同学信息: 学号，学生姓名，科目名，分数



```mysql
# 查询参加'数据库结构-1' 考试的同学信息: 学号，学生姓名，科目名，分数
select s.`studentno`,`studentname`,`subjectname`,`studentresult`
from student s
inner join result r
on s.studentno = r.studentno
inner join `subject` sub
on r.subjectno = sub.subjectno
where subjectname = '数据库结构-1';
```





### 8.分页 （limit）和 排序 (order by)



**排序        升序 asc     降序 desc** 

```sql
# 查询参加'数据库结构-1' 考试的同学信息: 学号，学生姓名，科目名，分数
select s.`studentno`,`studentname`,`subjectname`,`studentresult`
from student s
inner join result r
on s.studentno = r.studentno
inner join `subject` sub
on r.subjectno = sub.subjectno
where subjectname = '数据库结构-1'
order by studentresult asc;
```

![image-20201121144454644](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121144454644.png)





**分页   limit 起始值 页面的大小**

> ### limit  0,5    1~5         条数据  第一页
>
> ### limit  1,5    2~6
>
> ### limit  5,5   6~10                      第二页

**第一页   limit 0,5**

**第二页   limit 5,5**

**第三页   limit 10,5**

**第四页   limit 15,5**

> ### limit size*(page -1),size       数据总数/页面大小=总页数



```sql
# 查询参加'数据库结构-1' 考试的同学信息: 学号，学生姓名，科目名，分数
select s.`studentno`,`studentname`,`subjectname`,`studentresult`
from student s
inner join result r
on s.studentno = r.studentno
inner join `subject` sub
on r.subjectno = sub.subjectno
where subjectname = '数据库结构-1'
order by studentresult asc
limit 0,5;
```

![image-20201121144643440](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121144643440.png)



### 9.子查询

本质: 在where语句中嵌套一个子查询语句

```sql
# 查询 数据库结构-1 的所有考试结果（学号，科目编号，成绩），降序排序
#方法一

select studentno,r.subjectno,studentresult
from result r
inner join subject sub
on r.subjectno = sub.subjectno
where subjectname = '数据库结构-1'
order by studentresult desc;


# 方法二

select studentno,subjectno,studentresult
from result
where subjectno=
(select subjectno from subject
where subjectname='数据库结构-1')
order by studentresult desc;
;
```



> ### 查询分数不小于80分的学生的学号、姓名、课编号程

```sql
-- 分数不小于80分的学生的学号和姓名,课编号程
select distinct s.studentno,studentname,r.subjectno
from student s inner join
result r  on s.studentno=r.studentno
where studentresult >= 80 ;

-- 在这个基础上增加一个科目 '高等数学-2' 查询'高等数学-2'分数不小于80分的学生的学号和姓名
-- 方法一
select distinct s.studentno,studentname,r.subjectno
from student s inner join
result r  on s.studentno=r.studentno
inner join
subject sub on r.subjectno = sub.subjectno
where studentresult >= 80 and subjectname='高等数学-2';

-- 方法二

select distinct s.studentno,studentname,r.subjectno
from student s inner join
result r  on s.studentno=r.studentno
where studentresult >= 80  and subjectno=(select subjectno from subject where subjectname='高等数学-2');
```

题目等价于  查询课程为 ''高等数学-2' 且分数不小于 80 的同学的学号、姓名、课程编号



```sql
# 方法三 但是这个查询不出 subjectno 连续嵌套只能查出最外层的属性
select studentno,studentname from student where studentno in (
    select studentno from result where studentresult > 80 and subjectno = (
        select subjectno from subject where subjectname ='高等数学-2'
        )
    )
```



where 子查询效率更好因为它不需要连表。



### 10.聚合函数 分组过滤

```sql
-- 查询不同课程的平均分、最高分、最低分
-- 核心: （根据不同的课程分组）
select subjectname,AVG(studentresult),MAX(studentresult),MIN(studentresult)
from result r
INNER JOIN subject sub on sub.subjectno = r.subjectno
group by r.subjectno;
```





### 11.事务

#### 11.1事务原则

事务 ： 要么都成功，要么都失败。

> 事务原则： ACID原则    原子性 一致性 隔离性 持久性   (脏读 幻读)

* 原子性 (Atomicity)  :     **要么都成功，要么都失败。**

* 一致性( Consistency ):   **事务前后的数据完整性保持一致**                  a 800 b 200 无论怎么样最终a+b一定是1000





* 持久性(Durability):       **数据不随着外界原因导致数据丢失，事务一旦提交就不可逆，被持久化到数据库中。**

​                         如果事务没有提交，发生了宕机，数据恢复到原始状态

​                         如果事务提交了，发生了宕机，数据持久化到数据库



* 隔离性（Isolation):       **多个用户同时操作，主要是排除其他事务对本次事务的影响。** 

   

​       导致的问题:    

                       1. 脏读:  一个事务读取了另一个事务未提交的数据
                          2. 不可重复读: 一个事务读取表中的数据，多次读取结果不同。（不一定是错误，只是某些场合不对）   
                          3. 幻读:  一个事务读取到了别的事务插入的数据，导致前后读取不一致。

​                 



![image-20201121191402007](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121191402007.png) 

​      



> #### 脏读

![image-20201121191540557](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121191540557.png)

> #### 不可重复读

![image-20201121191751203](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121191751203.png)



> #### 幻读



![image-20201121191910111](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121191910111.png)





####    11.2**测试事务实现转账**

![image-20201121193714664](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201121193714664.png)



​     

```mysql
-- mysql 是默认开启事务自动提交的
SET autocommit = 0;  #  关闭自动提交
SET autocommit = 1;  #  开启自动提交


-- 手动处理事务

SET autocommit = 0; -- 关闭自动提交

START TRANSACTION; -- 标记一个事务的开始，从这个之后的sql都在同一个事务内

-- 提交 ：持久化 (成功!)
COMMIT;
-- 回滚: 回到原来的样子 (失败!)
ROLLBACK;

-- 事务结束
SET autocommit = 1;-- 开启自动提交



-- 了解即可
SAVEPOINT 保存点名;  -- 设置一个事务的保存点
ROLLBACK TO SAVEPOINT  保存点名; -- 回滚到保存点
RELEASE SAVEPOINT 保存点名;  -- 撤销保存点

-- 转账
CREATE DATABASE shop CHARACTER SET utf8 COLLATE utf8_general_ci;
use shop;
create TABLE `account`(
    `id`int (3) NOT NULL auto_increment,
    `name` varchar(30) not null,
    `money` DECIMAL(9,2) not null,
    primary key(`id`)
)engine=INNODB DEFAULT CHARSET =utf8;


INSERT INTO account(`name`,`money`)
value ('A',2000.00),
      ('B',100000.00);

-- 模拟转账 事务
SET autocommit =0;  -- 关闭自动提交
START TRANSACTION;   -- 开启一个事务

UPDATE `account` SET money=money-500 where `name` = 'A';
UPDATE `account` SET money=money+500 where `name` = 'B';

commit;
ROLLBACK;

SET autocommit = 1;
```





### 12.索引



**数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。**           

参考博客:https://blog.codinglabs.org/articles/theory-of-mysql-index.html



#### 12.1 索引的分类

> 在一个表中，主键索引只能有一个，唯一索引可以有多个



* 主键索引 (primary key)
  * 唯一的标识，主键不可重复，只能有一个列作为主键
* 唯一索引 (unique key)
  * 避免重复的列出现，唯一索引可以重复，多个列都可以标识为 唯一索引  比如:   个人账户 不能重复
* 常规索引 (key/index)
  * 默认的，index。 key关键字来设置

* 全文索引 (fulltext)
  * 快速定位数据



```mysql
-- 显示所有的索引信息
SHOW INDEX FROM student;

-- 增加一个全文索引 (索引名) 列名
ALTER TABLE school.student ADD FULLTEXT  INDEX `studentName`(`studentname`);

-- EXPLAIN 分析sql执行的情况
EXPLAIN select * from student;

explain SELECT * from student where match(studentname) against ('皮');
```



#### 12.2  索引对性能的影响



![image-20201123112728940](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201123112728940.png)



#### **12.3 索引原则**

* 索引不是越多越好
* 不要对经常变动的数据加索引
* 小数据量的表不需要加索引
* 索引一般加在常用来查询的字段上！









#### 12.4B树

![image-20201122131457614](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122131457614.png)

​	展示了一种可能的索引方式。左边是数据表，一共有两列七条记录，最左边的是数据记录的物理地址（注意逻辑上相邻的记录在磁盘上也并不是一定物理相邻的）。为了加快Col2的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在\(O(log_2n)\)的复杂度内获取到相应数据。

虽然这是一个货真价实的索引，但是实际的数据库系统几乎没有使用二叉查找树或其进化品种[红黑树](http://en.wikipedia.org/wiki/Red-black_tree)（red-black tree）实现的，



所有的数据都要在内存中查找，面临一个问题就是磁盘读写问题。数据库索引是存储在磁盘上的，数据量较大时，无法将全部的索引数据一次性装载到内存中。只能逐一加载磁盘页，磁盘页对应搜索树的节点 。

如果用**二叉搜索树**作为索引，情况会非常糟糕。最坏情况下，磁盘读写次数等于树的高度。 我们要降低树的高度。把瘦长的树变成矮壮的树。

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122132950499.png" alt="image-20201122132950499" style="zoom:50%;" />



可以采用多路平衡树，其中常用的就是**B树**。

> ### 建立 B树 的五条规则         
>
> 描述一颗 B树时需要指定它的**阶数**，阶= max（孩子节点个数），一般用字母 M 表示阶数  阶数是提前定义的，值取决于磁盘页的大小
>
> **K**是节点的元素个数   



* 根节点至少有两个子节点
* 每个中间节点都包含 k-1 个元素和 k 个孩子。其中 m/2 <= k <=m
* 每一个叶子节点都包含 k-1 个元素, 其中 m/2 <= k <=m
* 所有的叶子节点都位于同一层
* 每个节点的元素从小到大排列



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122135823375.png" alt="image-20201122135823375" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122133805765.png" alt="image-20201122133805765" style="zoom:50%;" />

​        

​     查找3的过程看出，B树的比较次数不比二叉树少，不过比较操作都是在内存中完成的，效率相当高。磁盘读写非常昂贵，B树对性能的提升非常明显。 



### 

​                                                          

​         **五阶B树**    参考自:https://www.bilibili.com/video/BV1Aa4y1j7a4/?spm_id_from=333.788.videocard.0

![image-20201122142726694](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122142726694.png)

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122143110376.png" alt="image-20201122143110376" style="zoom:50%;" />

节点个数



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122143147785.png" alt="image-20201122143147785" style="zoom:50%;" />



严格画法 因为阶数是提前定义的  就算把59删掉它依然是五阶b树

![image-20201122144011025](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122144011025.png)





#### 12.5  B+树

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122163505200.png" alt="image-20201122163505200" style="zoom:100%;" />

![image-20201122164145823](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122164145823.png)

![image-20201122164359955](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201122164359955.png)



### 13.权限管理和备份

mysql 下的user表进行增删改查



![image-20201123151237735](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201123151237735.png)

![image-20201123151436773](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201123151436773.png)





> mysql 数据备份

备份的方式

* 拷贝物理文件 data文件夹
* 可视化工具中导出sql文件
* 使用命令行导出 mysqldump 命令行使用



### 14.规范数据库设计 



软件开发中，关于数据库的设计

* 分析需求: 分析业务和需要处理的数据库的需求
* 概要设计:设计关系图E-R图



设计数据库的步骤:(个人博客)

* 收集信息，分析需求 
* 标识实体（建表  把需求落实到每个字段）
* 标识实体之间的关系
  * 写博客： user-> blog
  * 创建分类: user-> category
  * 关注: user ->user
  * 友链: links
  * 评论: user-user-blog



### 15.数据库三大范式



第一范式：要求数据库表的每一列都是不可再分的原子数据项

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201124201117277.png" alt="image-20201124201117277" style="zoom:50%;" />



第二范式: 在满足第一范式的基础上

​                表中的每一列都和主键相关，而不能只与主键的某一部分相关（每张表只做一件事）

![image-20201124201447495](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201124201447495.png)







### 16.补充（《sql进阶教程》）

![image-20201201135908173](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201201135908173.png)

> 问题：选择重复的行但是保留一行id最大的。
>
> 解决方法:非等值自连接

```sql
select * from  Products as p1
where rowid < (select max(p2.rowid)
     from Products as p2
    where p1.name = p2.name
    and p1.price = p2.price
    );
```

