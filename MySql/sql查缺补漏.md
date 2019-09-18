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

## 2.2域完整性

使用


域完整性约束

## 2.3参照完整性

