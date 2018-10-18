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
## 三.查询1与查询2结果分析：
虽然第一次反应时间比查询2反应时间慢，但是在只后的每一次反应时间结果来看，都比2快，所以我认为查询1更优。

