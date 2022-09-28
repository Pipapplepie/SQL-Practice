# Section A

1

```sql
create table Employee (
Id integer not null,
Name varchar(32) not null,
Salary integer not null,
DepartmentId integer not null,
primary key (Id)
)
```

```sql
insert into employee (Id, Name, Salary, DepartmentId) values (1,'Joe',70000,1);
insert into employee (Id, Name, Salary, DepartmentId) values (2,'Henry',80000,2);
insert into employee (Id, Name, Salary, DepartmentId) values (3,'Sam',60000,2);
insert into employee (Id, Name, Salary, DepartmentId) values (4,'Max',90000,1);
```

![1](https://user-images.githubusercontent.com/107236740/192513863-b051cdea-8ca9-4e0f-9fbc-3b6b8287f26e.png)

```sql
create table Department (
Id integer not null,
Name varchar(32) not null,
primary key (Id)
)
```

```sql
select departmentid, name, max(salary) as max_salary_dep
from employee
group by departmentid
```

2

```sql
create table (
id integer not null,
student varchar(32) not null,
primary key (id)
)
```

```sql
insert into seat values (1,'Abbot');
insert into seat values (2,'Doris');
insert into seat values (3,'Emerson');
insert into seat values (4,'Green');
insert into seat values (5,'James');
```

```sql
select (case when mod(c.id_count,2) = 1 then 
(case when s.id=c.id_count then s.id
when mod(s.id,2) = 0 then s.id-1
when mod(s.id,2) = 1 then s.id+1
else null end) 
else
(case when mod(s.id,2) = 0 then s.id-1
when mod(s.id,2) = 1 then s.id+1
else null end) end) as id
,s.student
from seat as s, (select count(id) as id_count from seat) as c
order by id
```

3

```sql
select *, 
rank() over (order by score_avg desc) as rank1,
dense_rank() over (order by score_avg desc) as rank2,
row_number() over (order by score_avg desc) as rank3
from score
```
4

```sql
select num1 as ConsecutiveNums
from (select l1.id, l1.num as num1, l2.num as num2, l3.num as num3
from logs l1 left outer join logs l2 on l1.id + 1 = l2.id
left outer join logs l3 on l1.id + 2 = l3.id) s
where num1 = num2 and num1 = num3
```

5

```sql
select id, (case when p_id is null then 'Root'
when id in (select p_id from tree) then 'Inner'
else 'Leaf' end) as Type
from tree
```

6

```sql
select name
from employee
where id in 
(select managerid
from employee
group by managerid
having count(id) >= 5)
```

7

```sql
select question_id,
sum((case when action = 'show' then 1 else 0 end))/sum((case when action = 'answer' then 1 else 0 end))
as ratio
from survey_log
group by question_id
```

8

```sql
select d.name as department, e.name as employee, e.salary,
row_number() over (partition by e.departmentid 
											order by e.salary desc) as ranking
from new_employee e inner join department d on (e.departmentid = d.id)
```

9

```sql
select min(round(sqrt((p1.x - p2.x)*(p1.x - p2.x) + (p1.y - p2.y)*(p1.y - p2.y)),2)) as distance
from point_2d as p1 inner join point_2d as p2 on (p1.x, p1.y) != (p2.x, p2.y)
```

* **ROUND(x,y)**: x-the number to be rounded; y-the number of decimal digits.
* **inner join** on inequality.

10

```sql
select t.request_at as date,
sum(case when status != 'completed' then 1 else 0 end)/count(status) as cancellation_rate

from trips as t 
inner join users as u1
on t.client_id = u1.users_id
inner join users as u2
on t.driver_id = u2.users_id

where u1.banned = 'No' and u2.banned = 'No'

group by t.request_at
```

![c](https://user-images.githubusercontent.com/107236740/192719207-57408540-eb54-49ff-9cdf-01e0f7248bb5.png)

<img src='https://user-images.githubusercontent.com/107236740/192719315-a889c2a9-66f9-4086-9f71-eecbc60c53e5.png' width='450'>

# Section B

1

```sql
select name,
sum(case when subject = 'chinese' then score end) as chinese,
sum(case when subject = 'math' then score end) as math,
sum(case when subject = 'english' then score end) as english
from exam
group by name
```

2

```sql
select *
from (select name, 'chinese' as subject, chinese
from score1
union
select name, 'math' as subject, math
from score1
union
select name, 'english' as subject, english
from score1) s
order by name
```

* 想了很久，最后上网查了，想起了第四章中的**隐式数据类型转换**。

3

```sql
select anchor_name, date
from
(select anchor_name, date, sales/(sum(sales) over (partition by date)) as ratio
from anchor_sales) as new_anchor
where ratio >= 0.9
```

a. 2 dates; b. one anchor: C

<img src='https://user-images.githubusercontent.com/107236740/192738741-b2d68e86-5c85-4bae-9c1c-19c6ea7fb70b.png' width='450'>

4 

5 A(atomacity) C(consistency) I(isolation) D(duribility)

_参考博客：https://blog.csdn.net/Michaelia_hu/article/details/75339924_

# Section C

1

```sql
select cdate, 
count(case when result = '胜' then result end) as 胜,
count(case when result = '负' then result end) as 负
from competition
group by cdate
```

2
