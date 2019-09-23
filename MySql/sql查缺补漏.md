# 1.SQL数据查询语言DQL

## 1.字段控制查询

### 1.1sql结果集去重

```sql
SELECT DISTINCT name FROM employee
```

### 1.2sql结果别名

```sql
SELECT *,IFNULL(salary,0)+IFNULL(performance,0) AS newsum FROM employee
```

### 1.3排序

```sql
SELECT * FROM employee ORDER BY gender ASC,salary DESC
```

## 2.聚合函数

### 2.1COUNT()

COUNT()：统计指定列不为NULL的记录行数；

```sql
SELECT COUNT(*) FROM employee
```

### 2.2MAX(),MIN()

MAX()：计算指定列的最大值，如果指定列是字符串类型，那么使用字符串排序运算；

MIN()：计算指定列的最小值，如果指定列是字符串类型，那么使用字符串排序运算；

```sql
SELECT MAX(salary), MAX(salary) FROM employee;
SELECT MAX(salary), MIN(salary) FROM employee;
```

### 2.3SUM()

SUM()：计算指定列的数值和，如果指定列类型不是数值类型，那么计算结果为0；

```sql
SELECT SUM(salary) FROM employee;
```

### 2.4AVG()

AVG()：计算指定列的平均值，如果指定列类型不是数值类型，那么计算结果为0；

```sql
SELECT AVG(salary) FROM employee;
```

## 3.分组

### 3.1什么是分组查询

SELECT gender from employee GROUP BY gender;

### 3.2分组使用

根据gender字段来分组，gender字段的全部值只有两个('男'和'女')，所以分为了两组

当group by单独使用时，只显示出每组的第一条记录

所以group by单独使用时的实际意义不大

### 3.3分组注意

在使用分组时，select后面直接跟的字段一般都出现在group by 后

### 3.4group by + group_concat()

group_concat(字段名)可以作为一个输出字段来使用

表示分组之后，根据分组结果，使用group_concat()来放置每一组的某字段的值的集合

SELECT gender,GROUP_CONCAT(name) from employee GROUP BY gender;

### 3.5分组 + 聚合函数

查询各部门的工资总和，平均工资，最多工资，最少工资

```sql
SELECT department,GROUP_CONCAT(salary),SUM(salary) FROM employee GROUP BY department

SELECT department,GROUP_CONCAT(salary),AVG(salary) FROM employee GROUP BY department

SELECT department,GROUP_CONCAT(salary),MAX(salary) FROM employee GROUP BY department

SELECT department,GROUP_CONCAT(salary),MIN(salary) FROM employee GROUP BY department
```

**练习：**

**1.查询每个部门的名称及人数**

```sql
SELECT department,GROUP_CONCAT(name),COUNT(*) FROM employee GROUP BY department
```

**2.查询每个部门薪资大于2000的部门名称及人数**

```sql
SELECT department,GROUP_CONCAT(name),GROUP_CONCAT(salary),COUNT(*) FROM employee WHERE salary > 2000 GROUP BY department
```

![1568812863276](F:\GitSpace\sqlStudy\1568812863276.png)

### 3.6HAVING()

用来分组查询后指定一些条件来输出查询结果

having作用和where一样，但having只能用于group by

**having与where的区别**

- having是在分组后对数据进行过滤
- where是在分组前对数据进行过滤
- having后面可以使用分组函数(统计函数)
- where后面不可以使用分组函数
- WHERE是对分组前记录的条件，如果某行记录没有满足WHERE子句的条件，那么这行记录不会参加分组；而HAVING是对分组后数据的约束。

**查询总薪资大于等于9000的部门**

```sql
SELECT department,GROUP_CONCAT(name),GROUP_CONCAT(salary),SUM(salary)
FROM employee 
GROUP BY department 
HAVING SUM(salary) >= 9000
```

![1568813560372](F:\GitSpace\sqlStudy\1568813560372.png)

**查询工资大于等于2000的，部门总工资大于9000的部门名称和总工资**

```sql
SELECT department,GROUP_CONCAT(name),GROUP_CONCAT(salary),SUM(salary) 
FROM employee 
WHERE salary >= 2000 
GROUP BY department 
HAVING SUM(salary) > 9000
-- 还可以再排序加上 ORDER BY SUM(salary)
```

![1568814026829](F:\GitSpace\sqlStudy\1568814026829.png)

## 4.SQL查询语句书写&&执行顺序

书写顺序：SELECT  => FROM => WHERE => GROUP BY => HAVING => ORDER BY => LIMIT

执行顺序：FROM => WHERE => GROUP BY => HAVING => SELECT  => ORDER BY => LIMIT

## 5.LIMIT

格式：

```sql
-- 从第0条纪录开始查，共查3条纪录
SELECT * FROM 表名 LIMIT 0,3
```

分页思路

```java
int curPage = 1; // 当前页
int pageSize = 3; // 每页多少条纪录

// 当前页为1， 第一页从0开始(1 - 1) * 3 = 0
// 当前页为2， 第一页从0开始(2 - 1) * 3 = 3
// 当前页为3， 第一页从0开始(3 - 1) * 3 = 6
// 当前页为4， 第一页从0开始(4 - 1) * 3 = 9

SELECT * FROM employee LIMIT (curPage - 1) * pageSize,pageSize;

```

# 2.数据完整性

**什么是数据的完整性**

保证用户输入的数据保存到数据库中是正确的。

**如何添加数据完整性**

在创建表时给表中添加约束

**完整性分类**

- 实体完整性
- 域完整性
- 参照完整性(引用完整性)

## 2.1实体完整性

**什么是实体完整性**

表中的一行(一条记录)代表一个实体（entity）

- 主键约束
- 唯一约束
- 自动增长列

## 2.2域完整性

- 数据类型
- 非空约束
- 默认值约束

## 2.3参照完整性

表与表之间对应的一种关系

# 3.多表查询

## 3.1合并结果集

**什么是合并结果集**

就是把两个select语句的查询结果合并到一起

**合并结果集的两种方式**

- UNION  合并时去除重复记录
- UNION ALL 合并时不去出重复记录

**格式**

```
SELECT * FROM 表1 UNION SELECT * FROM 表2；
SELECT * FROM 表1 UNION ALL SELECT * FROM 表2;
```

## 3.2连接查询

**什么是连接查询**

从多个表查询数据

**什么是笛卡尔集**

同时查询两个表就会出现笛卡尔集结果

```sql
SELECT * FROM stu,score;
```

**查询时给表起别名**

```sql
SELECT * FROM stu st,score sc;
```

**多表查询如何保证数据正确**

在查询时主键和外键保持一致

```sql
SELECT * FROM stu st,score sc WHERE st.id = sc.id;
```

**原理**

逐行判断，相等的留下，不相等的不要

## 3.3根据连接方式分类

### 3.3.1内连接

- 等值连接查询

  ```sql
  SELECT * FROM t_student st,score sc WHERE st.sid = sc.sid -- 99查询
  SELECT * FROM t_student st INNER JOIN score sc ON st.sid = sc.sid -- 内连接
  SELECT * FROM t_student st JOIN score sc ON st.sid = sc.sid -- 内连接 INNER可以省略不写
  SELECT * FROM t_student st INNER JOIN score sc ON st.sid = sc.sid WHERE sc.score > 70 -- 可以继续在后面加条件
  SELECT st.`name`,st.sex,sc.score FROM t_student st INNER JOIN score sc ON st.sid = sc.sid WHERE sc.score > 70 -- 只显示姓名，成绩
  SELECT st.`name`,st.sex,sc.score FROM t_student st INNER JOIN score sc ON st.sid = sc.sid WHERE sc.score > 70 AND st.sex = '男' -- WHERE后可以加多个条件
  ```

- 多表连接查询

  ```sql
   -- 多表联查 99查询
  SELECT st.`name`,sc.score,fd.xueke from t_student st,score sc,find fd WHERE st.sid = sc.sid AND sc.cid = fd.cid
  
  -- 多表联查 内连接查询
  SELECT st.`name`,sc.score,fd.xueke FROM t_student st
  JOIN score sc
  ON st.sid = sc.sid
  JOIN find fd
  ON sc.cid = fd.cid 
  ```

  ![1569208989649](F:\GitSpace\sqlStudy\src\imgs\1569208989649.png)

- 非等值连接查询

  ```sql
  -- 查询员工姓名，工资，部门，工资等级 99查询
  SELECT e.ename,e.salary,d.dname,s.grade FROM emp e,salgrade s,dept d
  WHERE e.deptno = d.deptno
  AND e.salary >= s.lowSalary and e.salary <= s.highSalary
  
  -- 内链接查询
  SELECT e.ename,e.salary,d.dname,s.grade 
  FROM emp e
  JOIN dept d
  ON e.deptno = d.deptno
  JOIN salgrade s
  ON e.salary >= s.lowSalary and e.salary <= s.highSalary
  ```

  

- 自连接查询

### 3.3.2外连接

- 左外连接   左边的表忽视条件全部查出来，右边的表只有符合条件的才能查出来

  ```sql
  SELECT * from t_student st LEFT OUTER JOIN score sc ON st.sid = sc.sid -- 左外连接
  ```

- 右外连接  右边的表忽视条件全部查出来，左边的表只有符合条件的才能查出来

  ```sql
  SELECT * from t_student st RIGHT OUTER JOIN score sc ON st.sid = sc.sid -- 右外连接
  ```

### 3.3.2自然连接

自动去除笛卡尔集，但是两个表的结构必须一致

```sql
SELECT * FROM 表1 NATURAL JOIN 表2 
```

# 4.子查询

## 4.1子查询WHERE&&FROM条件查询

**什么时子查询**

一个select语句中包含另一个完整的select语句。

或两个以上SELECT，那么就是子查询语句了。

**子查询出现的位置**

- where后，把select查询出的结果当作另一个select的条件值

  ```sql
  -- 查询和项羽同一个部门的员工姓名
  SELECT e.deptno FROM emp e WHERE e.ename = '项羽'
  
  -- 把上一个表的结果作为另一个表的条件
  SELECT e.ename 
  FROM emp e 
  WHERE e.deptno = (SELECT e.deptno FROM emp e WHERE e.ename = '项羽');
  ```

- from后，把查询出的结果当作一个新表；

  ```sql
  -- 查出部门在30的 员工姓名，工资，且工资大于1200
  SELECT e.ename,e.salary FROM emp e WHERE e.deptno = 30
   
  -- 把上面的查询语句的结果作为新得表在筛选 工资大于1200
  SELECT n.ename,n.salary 
  FROM (SELECT e.ename,e.salary FROM emp e WHERE e.deptno = 30) n 
  WHERE n.salary > 1200
  ```

  

**练习1**

```sql
-- 练习1 查询工资高于程咬金得员工
-- 先查出程咬金得工资
SELECT e.salary FROM emp e WHERE e.ename = '程咬金'
-- 再把程咬金得工资作为条件
SELECT * FROM emp e 
WHERE e.salary > (SELECT e.salary FROM emp e WHERE e.ename = '程咬金')
```

**练习2**

```sql
-- 练习2 查询出工资高于30号部门的员工信息
-- 先查出部门为30号的最高工资
SELECT MAX(e.salary) FROM emp e WHERE e.deptno = 30
-- 再把结果作为条件
SELECT * FROM emp e WHERE e.salary > (SELECT MAX(e.salary) FROM emp e WHERE e.deptno = 30)

```

**练习3**

```sql
-- 查询工作和薪资都和妲己一样的员工信息
SELECT e.job,e.salary FROM emp e WHERE e.ename = '妲己' -- 销售 7698
-- 把结果作为条件
SELECT * FROM emp e WHERE (e.job,e.salary) in (SELECT e.job,e.salary FROM emp e WHERE e.ename = '妲己')

SELECT * 
FROM emp e,(SELECT e.job,e.salary FROM emp e WHERE e.ename = '妲己') r
WHERE e.job = r.job AND e.salary = r.salary
```

![1569224187693](F:\GitSpace\sqlStudy\src\imgs\1569224187693.png)

**练习4**

```sql
-- 有两个以上下属的员工
SELECT e.mgr FROM emp e GROUP BY mgr HAVING COUNT(e.mgr) > 2

SELECT * FROM emp e
WHERE e.empno in (SELECT e.mgr FROM emp e GROUP BY mgr HAVING COUNT(e.mgr) > 2)
```

![1569225121695](F:\GitSpace\sqlStudy\src\imgs\1569225121695.png)

**练习5**

```sql
-- 查询员工编号为7788的姓名，工资，部门，部门地址
SELECT e.ename,e.salary,d.dname,d.local 
FROM emp e,dept d 
WHERE e.deptno = d.deptno AND e.empno = 7788
```

![1569225490498](F:\GitSpace\sqlStudy\src\imgs\1569225490498.png)

## 4.2自连接

自连接：自己连接自己，起别名

```sql
-- 自连接
-- 查询编号为7369的姓名及上司的编号和姓名
SELECT e1.empno,e1.ename,e2.empno,e2.ename FROM emp e1,emp e2
WHERE e1.mgr = e2.empno AND e1.empno = 7369
```

![1569226276384](F:\GitSpace\sqlStudy\src\imgs\1569226276384.png)

# 5.常用函数

**函数介绍**

事先提供好的一些功能可以直接使用

函数可以用在SELECT语句及其子句

也可以用在UPDATE,DELETE语句当中。

**函数分类**

- 字符串函数
- 数值函数
- 日期时间函数
- 流程函数
- 其他函数

# 6.事务

# 7.权限操作

# 8.视图

# 9.存储过程

# 10.自定义函数

# 11.索引

