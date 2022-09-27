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

