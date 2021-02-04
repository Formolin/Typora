# mysql04

商业项目会有多个表，如何实现一次查询多个表，就需要使用联合查询

- 一张表中不可能存在完全相同的两条记录
- 一张表代表一个实体

例如：学生表

- id
- name
- sex
- age

成绩表：

- id
- stuid
- java
- html
- python

两张表有关系，那么都写在一张表了呢？

数据太乱了，长度不可变，我没上学，字段就空着，那么久没意义了

如果我这个学生退学了，你敢删除这条记录吗？不能，其他的信息都删除了，如果分表就可以了

- 一个表的字段越少操作效率越高

## 联合查询

- 内连接 inner join..on 关联字段 
- 外链接
  - 左外链接
  - 右外链接
  - 全连接
- 自连接
- 交叉连接



- 查询正式员工的信息，包含部门名称

```sql
select * from tb_emp where deoId is not null,但是我要的是部门名称

select * from tb_emp inner join tb_dep on tb_emp.depId = tb_dep.depId



SELECT e.empId,e.empName,d.depName from 
tb_employee e INNER join tb_department d 
on e.depId = d.depId


SELECT e.empId,e.empName,d.depName from 
tb_employee e,tb_department d where
e.depId = d.depId
```

- 查询所有员工的信息，包含部门名称

```sql
SELECT * from 
tb_employee e left outer join tb_department d 
on e.depId = d.depId
```

- 查询所有部门及其员工

```sql
SELECT * from 
tb_employee e right outer join tb_department d 
on e.depId = d.depId
```

- 交叉连接

```sql
SELECT e.empId,e.empName,d.depName from 
tb_employee e cross join tb_department d 
```

返回的是左表记录数乘以右表记录数，作用，测试数据效率，查询时间

- 自连接

```sql
select a.empId,b.empName from tb_employee a,tb_employee b WHERE
a.depId = b.depId
```

- 以job表为例

  - 查询姓名等于李春林的年龄的信息
  - 查询年龄大于24的信息
  - 查询年龄大于李春林的个人信息

  ```sql
  select * from job a,job b where a.name='李春林' and b.age>a.age
  
  select b.* from job a,job b where a.name='李春林' and b.age>a.age
  ```

- 全连接

```sql
SELECT * from 
tb_employee e right join tb_department d 
on e.depId = d.depId
UNION
SELECT * from 
tb_employee e left join tb_department d 
on e.depId = d.depId

字段必须一致,顺序也必须一致
```



## 嵌套查询

- 查询姓名是李春林的年龄

  ```sh
  select age from job where name='李春林'
  ```

  

- 查询年龄大于李春林的信息

  ```sql
  select * from job where age>(select age from job where name='李春林')
  ```

  

- 外层的条件和里层的数据类型一致

- 里层的sql语句必须返回一个值，如果有多个值，用in

- 只能单表查询，只能显示外层表的字段

- 执行效率不高，外层100条里层100条  - 100*100

## 练习

- 查询哪些求职者的年龄，大于 求职者的平均年龄的信息

  ```sql
  select * from job where age>(
  	select avg(age) from job  
  ) order by age 
  
  ```

  

- 显示求职咨询顾问岗位的最大年龄和他同岁都哪些人

```sql
select * from job where position='咨询顾问' and age=(
	select max(age) from job  where position='咨询顾问'
) 
```

- 查询和张洁求职相同职位的有哪些人

```sql
select * from job where position = (
	select position from job where name = '张洁'
)

把美工职位里改成一个张洁的

select * from job where position in (
	select position from job where name = '张洁'
)
```



- 查询和张洁求职相同职位的还有哪些人？(去掉张洁)

  

```sql
select * from job where position in (
	select position from job where name = '张洁'
) and name != '张洁'
```

- 查询技术部有哪些人？

```sql
select * from tb_department d,tb_employee e where d.depId=e.depId AND
 d.depName='技术部'

select * from tb_department d inner join tb_employee e ON
d.depId = e.depId where d.depName='技术部'


select * from tb_employee e where e.depId = (
	select d.depId from tb_department d where d.depName='技术部'
)
```

- 查询技术部有哪些人并显示部门名称

```sql

```

