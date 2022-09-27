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

5

```sql
select id, (case when p_id is null then 'Root'
when id in (select p_id from tree) then 'Inner'
else 'Leaf' end) as Type
from tree
```
