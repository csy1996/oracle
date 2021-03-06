# 实验一：分析SQL执行计划，执行SQL语句的优化指导
## 一.查询1：
```
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d，hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
```
### 查询1结果：
![result1](https://github.com/csy1996/oracle/blob/master/%E6%B5%8B%E8%AF%951/1.png)
### 优化指导：
在数据库中创建一个或多个索引，用来改进当前语句的执行方式。
## 二.查询2：
```
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT'，'Sales');
```
### 查询2结果：
![result1](https://github.com/csy1996/oracle/blob/master/%E6%B5%8B%E8%AF%951/2.png)

## 三.编写：
```
SELECT d.department_name ,count(e.job_id)as "部门总人数" ,
avg(e.salary)as "平均工资"
FROM hr.departments d, hr.employees e
WHERE d.department_id = e.department_id and d.department_name = 'IT' or d.department_name = 'Sales'
GROUP BY department_name 
```
### 查询结果：
![result1](https://github.com/csy1996/oracle/blob/master/%E6%B5%8B%E8%AF%951/3.png)
### 优化指导：
where语句处太过复杂，应该适当减少。
## 四.结果分析：
查询2在运行结果上反应时间上更快，查询1反应时间比较慢，所以我认为查询2更优。
本次实验更让我明白了SQL执行计划，执行SQL语句的优化指导的基本概念。

