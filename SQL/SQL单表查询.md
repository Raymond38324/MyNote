# SQL单表查询

## 查询语法

```mysql
SELECT 字段1,字段2... FROM 表名
                  WHERE 条件
                  GROUP BY field
                  HAVING 筛选
                  ORDER BY field
                  LIMIT 限制条数
```

## 关键字执行的优先级

```mysql
from
where
group by
having
select
distinct
order by
limit
```

**1.找到表:from**

**2.拿着where指定的约束条件，去文件/表中取出一条条记录**

**3.将取出的一条条记录进行分组group by，如果没有group by，则整体作为一组**

**4.将分组的结果进行having过滤**

**5.执行select**

**6.去重**

**7.将结果按条件排序：order by**

**8.限制结果的显示条数**

## 简单查询

### 1. 准备数据

```mysql
#创建表
create table employee(
    id int not null unique auto_increment,
    name varchar(20) not null,
    sex enum('male','female') not null default 'male',
    age int(3) unsigned not null default 28,
    hire_date date not null,
    post varchar(50),
    post_comment varchar(100),
    salary double(15,2),
    office int, #一个部门一个屋子
    depart_id int
);
```
```mysql
#查看表结构
mysql> desc employee;
+--------------+-----------------------+------+-----+---------+----------------+
| Field        | Type                  | Null | Key | Default | Extra          |
+--------------+-----------------------+------+-----+---------+----------------+
| id           | int(11)               | NO   | PRI | NULL    | auto_increment |
| name         | varchar(20)           | NO   |     | NULL    |                |
| sex          | enum('male','female') | NO   |     | male    |                |
| age          | int(3) unsigned       | NO   |     | 28      |                |
| hire_date    | date                  | NO   |     | NULL    |                |
| post         | varchar(50)           | YES  |     | NULL    |                |
| post_comment | varchar(100)          | YES  |     | NULL    |                |
| salary       | double(15,2)          | YES  |     | NULL    |                |
| office       | int(11)               | YES  |     | NULL    |                |
| depart_id    | int(11)               | YES  |     | NULL    |                |
+--------------+-----------------------+------+-----+---------+----------------+
```

```mysql
#插入记录
#三个部门：教学，销售，运营
insert into employee(name,sex,age,hire_date,post,salary,office,depart_id) values
    ('egon','male',18,'20170301','老男孩驻沙河办事处外交大使',7300.33,401,1), #以下是教学部
    ('alex','male',78,'20150302','teacher',1000000.31,401,1),
    ('wupeiqi','male',81,'20130305','teacher',8300,401,1),
    ('yuanhao','male',73,'20140701','teacher',3500,401,1),
    ('liwenzhou','male',28,'20121101','teacher',2100,401,1),
    ('jingliyang','female',18,'20110211','teacher',9000,401,1),
    ('jinxin','male',18,'19000301','teacher',30000,401,1),
    ('成龙','male',48,'20101111','teacher',10000,401,1),

    ('歪歪','female',48,'20150311','sale',3000.13,402,2),#以下是销售部门
    ('丫丫','female',38,'20101101','sale',2000.35,402,2),
    ('丁丁','female',18,'20110312','sale',1000.37,402,2),
    ('星星','female',18,'20160513','sale',3000.29,402,2),
    ('格格','female',28,'20170127','sale',4000.33,402,2),

    ('张野','male',28,'20160311','operation',10000.13,403,3), #以下是运营部门
    ('程咬金','male',18,'19970312','operation',20000,403,3),
    ('程咬银','female',18,'20130311','operation',19000,403,3),
    ('程咬铜','male',18,'20150411','operation',18000,403,3),
    ('程咬铁','female',18,'20140512','operation',17000,403,3)
;
```

### 2.简单查询

#### 简单查询

```mysql
SELECT * FROM employee;# 选择全部数据，显示全部字段
SELECT name,salary FROM employee;# 选择全部数据，显示name和salary字段
```

#### 避免重复

```mysql
SELECT DISTINCT post FROM employee;   
```

#### 查询中使用四则运算

```mysql
SELECT name, salary*12 FROM employee; # 选择全部数据，显示name和一年的工资
SELECT name, salary*12 AS Annual_salary FROM employee; #选择全部数据，显示name和一年的工资,并为年工资设置别名
```

#### 定义显示格式

```mysql
CONCAT() 函数用于连接字符串
SELECT CONCAT('姓名: ',name,'  年薪: ', salary*12)  AS Annual_salary 
FROM employee;

CONCAT_WS() 第一个参数为分隔符
SELECT CONCAT_WS(':',name,salary*12)  AS Annual_salary 
FROM employee;
```

#### 测试

1. 查出所有员工的名字，薪资,格式为 \<名字:egon\>,\<薪资:3000\>

2.  查出所有的岗位（去掉重复）

3.  查出所有员工名字，以及他们的年薪,年薪的字段名为annual_year
答案
```mysql
1. select concat('<名字:',name,'>') as name ,concat('<薪水:',salary,'>') as salary from employee; 
2. SELECT DISTINCT post FROM employee;
3. SELECT name, salary * 12 AS annual_year FROM employee;
```
### 3.WHERE约束

where字句中可以使用：

1. 比较运算符：><>= <= <> !=

2. between 80 and 100 值在10到20之间

3. in(80,90,100) 值是10或20或30

4. like 'egon%' pattern可以是%或_，

   - %表示任意多字符
   - _表示一个字符

5. 逻辑运算符：在多个条件直接可以使用逻辑运算符 and or not

#### 单条件查询

```mysql
SELECT name FROM employee　WHERE post='sale';
```

#### 多条件查询

```mysql
SELECT name,salary FROM employee WHERE post='teacher' AND salary>10000;
```

#### 关键字BETWEEN AND

```mysql
SELECT name,salary FROM employee WHERE salary BETWEEN 10000 AND 20000;# 在一万和两万之间
SELECT name,salary FROM employee WHERE salary NOT BETWEEN 10000 AND 20000;#不在一万和两万之间
```

#### 关键字IS NULL
判断某个字段是否为NULL不能用等号，需要用IS

```mysql
SELECT name,post_comment FROM employee WHERE post_comment IS NULL; 
SELECT name,post_comment FROM employee WHERE post_comment IS NOT NULL;
```

#### 关键字IN集合查询

```mysql
SELECT name,salary FROM employee WHERE salary=3000 OR salary=3500 OR salary=4000 OR salary=9000; # 下面的查询结果一样，下面的更精简
SELECT name,salary FROM employee WHERE salary IN (3000,3500,4000,9000);
```

#### 关键字LIKE模糊匹配

```mysql
通配符’%’(匹配任意个字符)
SELECT * FROM employee WHERE name LIKE 'eg%'; 
通配符’_’(匹配一个字符)
SELECT * FROM employee WHERE name LIKE 'al__';
```

#### 测试
1. 查看岗位是teacher的员工姓名、年龄
2. 查看岗位是teacher且年龄大于30岁的员工姓名、年龄
3. 查看岗位是teacher且薪资在9000-10000范围内的员工姓名、年龄、薪资
4. 查看岗位描述不为NULL的员工信息
5. 查看岗位是teacher且薪资是10000或9000或30000的员工姓名、年龄、薪资
6. 查看岗位是teacher且薪资不是10000或9000或30000的员工姓名、年龄、薪资
7. 查看岗位是teacher且名字是jin开头的员工姓名、年薪

答案

```mysql
1. SELECT name, age FROM employee WHERE post='teacher';
2. SELECT name, age FROM employee WHERE post='teacher' AND age > 30;
3. SELECT name, age, salary FROM employee WHERE post='teacher' AND salary BETWEEN 9000 AND 10000;
4. SELECT * FROM employee WHERE post_comment IS NOT NULL;
5. SELECT name, age, salary FROM employee WHERE post='teacher' AND salary IN (10000,9000,30000); 
6. SELECT name, age, salary FROM employee WHERE post='teacher' AND salary NOT IN (10000,9000,30000); 
7. SELECT name, salary * 12 AS annual_year FROM employee WHERE name like 'jin%';
```

### 4. 分组查询:GROUP BY

 **什么是分组？为什么要分组？**

分组指的是：将所有记录按照某个相同字段进行归类。分组在数据分析时很有用，比如查看公司每个部门的人数，平均工资，最高工资…… 都要用到分组

单独使用GROUP BY关键字分组
```mysql
SELECT post FROM employee GROUP BY post;
注意：我们按照post字段分组，那么select查询的字段只能是post，想要获取组内的其他相关信息，需要借助函数
```
GROUP BY关键字和GROUP_CONCAT()函数一起使用
```mysql
SELECT post,GROUP_CONCAT(name) FROM employee GROUP BY post;#按照岗位分组，并查看组内成员名
SELECT post,GROUP_CONCAT(name) as emp_members FROM employee GROUP BY post;
```
GROUP BY与聚合函数一起使用
```mysql
select post,count(id) as count from employee group by post;#按照岗位分组，并查看每个组有多少人
```

#### 聚合函数

**强调：**聚合函数聚合的是组的内容，若是没有分组，则默认一组


```mysql
SELECT COUNT(*) FROM employee;
SELECT COUNT(*) FROM employee WHERE depart_id=1;
SELECT MAX(salary) FROM employee;
SELECT MIN(salary) FROM employee;
SELECT AVG(salary) FROM employee;
SELECT SUM(salary) FROM employee;
SELECT SUM(salary) FROM employee WHERE depart_id=3;
```

#### 测试
1. 查询岗位名以及岗位包含的所有员工名字
2. 查询岗位名以及各岗位内包含的员工个数
3. 查询公司内男员工和女员工的个数
4. 查询岗位名以及各岗位的平均薪资
5. 查询岗位名以及各岗位的最高薪资
6. 查询岗位名以及各岗位的最低薪资
7. 查询男员工与男员工的平均薪资，女员工与女员工的平均薪资

答案

```mysql
1. SELECT post, group_concat(name) AS members FROM employee GROUP BY post;
2. SELECT post, COUNT(id) AS post_num FROM employee GROUP BY post;
3. SELECT sex, COUNT(id) AS num FROM employee GROUP BY sex;
4. SELECT post, AVG(salary) AS avg_salary FROM employee GROUP BY post;
5. SELECT post, MIN(salary) AS min_salary FROM employee GROUP BY post;
6. SELECT post, MAX(salary) AS avg_salary FROM employee GROUP BY post;
7. SELECT sex, AVG(salary) AS sex_salary FROM employee GROUP BY sex;
```

### 5.HAVING过滤

**WHERE和HAVING的区别：**
执行优先级从高到低：where > group by > having 

1. Where 发生在分组group by之前，因而Where中可以有任意字段，但是绝对不能使用聚合函数。
2. Having发生在分组group by之后，因而Having中可以使用分组的字段，无法直接取到其他字段,可以使用聚合函数

#### 小练习

1. 查询各岗位内包含的员工个数小于2的岗位名、岗位内包含员工名字、个数
2. 查询各岗位平均薪资大于10000的岗位名、平均工资
3. 查询各岗位平均薪资大于10000且小于20000的岗位名、平均工资

答案

```mysql
1. SELECT post, group_concat(name) AS member ,COUNT(id) FROM employee GROUP BY post HAVING COUNT(id) < 2;
2. SELECT post, AVG(salary) AS avg_salary FROM employee GROUP BY post HAVING AVG(salary) > 10000;
3. SELECT post, AVG(salary) AS avg_salary FROM employee GROUP BY post HAVING AVG(salary) BETWEEN 10000 AND 20000;
```

### 6. 查询排序:ORDER BY
按单列排序
```mysql
SELECT * FROM employee ORDER BY salary;
SELECT * FROM employee ORDER BY salary ASC;
SELECT * FROM employee ORDER BY salary DESC;
```
按多列排序:先按照age排序，如果年纪相同，则按照薪资排序
```mysql
SELECT * from employee ORDER BY age, salary DESC;
```
小练习：
1. 查询所有员工信息，先按照age升序排序，如果age相同则按照hire_date降序排序
2. 查询各岗位平均薪资大于10000的岗位名、平均工资,结果按平均薪资升序排列
3. 查询各岗位平均薪资大于10000的岗位名、平均工资,结果按平均薪资降序排列

```mysql
1. SELECT * FROM employee ORDER BY age ASC, hire_date DESC;
2. SELECT post, AVG(salary) AS avg_salary FROM employee GROUP BY post HAVING avg_salary > 10000 ORDER BY avg_salary ASC;
3. SELECT post, AVG(salary) AS avg_salary FROM employee GROUP BY post HAVING avg_salary > 10000 ORDER BY avg_salary DESC; 

```

### 7. 限制查询的记录数:LIMIT

```mysql

SELECT * FROM employee ORDER BY salary DESC LIMIT 3;                    #默认初始位置为0 

SELECT * FROM employee ORDER BY salary DESC LIMIT 0,5; #从第0开始，即先查询出第一条，然后包含这一条在内往后查5条

SELECT * FROM employee ORDER BY salary DESC LIMIT 5,5; #从第5开始，即先查询出第6条，然后包含这一条在内往后查5条
```

#### 小练习

1. 分页显示，每页5条

答案

```mysql
SELECT * FROM employee LIMIT 0,5;
SELECT * FROM employee LIMIT 5,5;
SELECT * FROM employee LIMIT 10,5;
```

### 8. 使用正则表达式查询

```mysql
SELECT * FROM employee WHERE name REGEXP '^ale';

SELECT * FROM employee WHERE name REGEXP 'on$';

SELECT * FROM employee WHERE name REGEXP 'm{2}';
```

```mysql
小结：对字符串匹配的方式
WHERE name = 'egon';
WHERE name LIKE 'yua%';
WHERE name REGEXP 'on$';
```

#### 小练习：

查看所有员工中名字是jin开头，n或者g结果的员工信息

```mysql
SELECT * FROM employee WHERE name REGEXP '^jin.*[gn]$';
```

