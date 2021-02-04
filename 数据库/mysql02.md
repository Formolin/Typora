# mysql02

## 一、创建库

```sh
create datebase 1902s
```

## 二、创建表

学生表：

- id整型，自动增长 主键 不能为空
- stuName，字符类型 长度10 不能为空
- stuSex 字符类型 1 不能为空 默认值 男
- stuAge 整型 不能为空 默认值18
- stuBirthday 日期类型 可以为空
- stuCode 字符类型 20 如果设置成int类型，以0开头那么久省略了
- stuSchool int 外键

学校表：

- id 整型 自增	主键	不能为空
- schoolName 字符 20 不能为空

```sh
学生记录：1 1001 tom 男 18 1999-1-1 2
学校记录：1 黑龙江大学

哪有问题？违反主外键约束
建表先建学校表

删除表
drop table tb_users
```

### 对数据表进行增删改查操作

- 增加

  ```sh
  insert into tb_user values();
  
  insert into tb_users(id,stuSchool) values(1,"黑龙江大学");
  insert into tb_users(id,stuSchool) values(2,"工业大学");
  ```

  - 字段名与值必须意义匹配，那么我删除一个就报错，使用第二种方法，指定字段名添加

- 修改

  - where条件

  - 比较运算符

    ```hs
    >,<.<= >= <> !=
    ```

  - 逻辑运算符

    ```sh
    and  or  !=  not  
    ```

  - 非空判断

    ```sh
    is null   is not null
    ```

  - 区间判断

    ```sh
    age>=18 and age<=20   or  age between 18 and 20有等于的
    ```

```sh
update 表名 set 字段=值，字段=值 where 字段=值

修改所有人的年龄

修改tom的年龄

把所有的年龄加1
```

- 范围判断
  - Select * from tb_user where id = 1 or id =2
  - Select * from tb_user where id in (1,2,3,4)
    - 如果是文本那类型那就加单引号

- 删除
- 查询
  - 模糊查询
    - 各种搜索
    - like
    - % 若干个任意字符
    - _ 一个任意字符
  - 起别名

### 四、练习



##### 先导入表格

查询年龄在22~28岁的学生信息。

查询满族、回族、蒙古族学生信息

查询年龄小于20的或者民族是“鄂伦春族”的学生记录。


查询姓吴的学生信息

查询姓吴的，名字只带一个字的学生的信息

查询姓吴的，名字带两个字的学生的信息

以下语句的含义？
select id,code,name,sex,nationality,age
from tb_student
where name like '%晓%'

以下语句的含义？
select id,code,name,sex,nationality,age
from tb_student
where name like '_晓_'









```sql
-- 查询年龄在22~28岁的学生信息。--
select * from tb_student where age>=22 and age<=28
-- 查询满族、回族、蒙古族学生信息 --
select * from tb_student where nationality in ('满族','回族','蒙古族')
-- 查询年龄小于20的或者民族是“鄂伦春族”的学生记录。 --
select * from tb_student where age<20 or nationality = '鄂伦春族'
-- 查询姓吴的学生信息 --
select * from tb_student where name like '吴%'
-- 查询姓吴的，名字只带一个字的学生的信息 --
select * from tb_student where name like '吴_'
-- 查询姓吴的，名字带两个字的学生的信息 --
select * from tb_student where name like '吴__'
```

```sql
create table school(
	id int auto_increment primary key not null,
	schoolName varchar(20) not null
);
create table student(
	id int auto_increment primary key not null,
	stuName varchar(10) not null,
	stuSex varchar(2) not null default '男',
	stuAge int not null default 18,
	stuBirthday date,
	stuCode varchar(20),
	stuSchool int,
	foreign key(stuSchool) references school(id)
);
```

