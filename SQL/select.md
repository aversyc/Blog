##### 数据定义

1. **定义模式**

   *CREATE SCHEMA <模式名> AUTHORIZATION <用户名>;*

   ```sql
   create schema `S-T`;
   ```

   

2. **删除模式**

   *DROP SCHEMA <模式名> <CASCADE|RESTRICT>;*

   ```sql
   drop schema `S-T`;
   ```

   

3. **定义基本表**

   *CREATE TABLE <表名> (<列名> <数据类型> [列级完整性约束条件]*

   ​					*[, <列名> <数据类型> [列级完整性约束条件]]*

   ​					*...*

   ​					*[, <表级完整性约束条件>]);*

   ```sql
   drop table SC;
   drop table Course;
   drop table Student;
   
   create table `S-T`.Student
   (
   	Sno char(10) primary key,
       Sname char(20) unique,
       Ssex char(2),
       Sage smallint,
   	Sdept char(20)
   );
   
   create table `S-T`.Course
   (
   	Cno char(4) primary key,
       Cname char(40) not null,
       Cpno char(4),
       Ccredit smallint,
       /* 表级完整性约束条件，Cpno是外码，被参照表是Course，被参照列是Cno */
       foreign key(Cpno)references Course(Cno)
   );
   
   create table `S-T`.SC
   (
   	Sno char(9),
       Cno char(4),
       Grade smallint,
       primary key(Sno, Cno),	/* 主码由两个属性构成 */
       foreign key(Sno)references Student(Sno),
       foreign key(Cno)references Course(Cno)
   );
   ```

   

4. **修改基本表**

   *ALTER TABLE <表名>*

   *[ADD [COLUMN] <新列名> <数据类型> [完整性约束]]*

   *[ADD <表级完整性约束>]*

   *[DROP [COLUMN] <列名> [CASCADE | RESTRICT]]*

   *[DROP CONSTRAINT <完整性约束名> [CASCADE | RESTRICT]]*

   *[ALTER COLUMN <列名> <数据类型>];*

   ```sql
   alter table Student add S_entrance date;
   alter table Student modify column Sage INT;		/* SQL Server: modify => alter */
   alter table Course add unique(Cname);
   alter table Student drop S_entrance;
   ```

   

5. **删除基本表**

   *DROP TABLE <表名> <CASCADE | RESTRICT>;*

   ```sql
   drop table Student restrict;
   ```

   

6. **建立索引**

   *CREATE [UNIQUE] [CLUSTER] INDEX <索引名>*

   *ON <表名>(<列名> [<次序>] [, <列名> [<次序>]] ...);*

   ```sql
   create unique index Stusno on `S-T`.Student(Sno);
   create unique index Coucno on `S-T`.Course(Cno);
   create unique index SCno on `S-T`.SC(Sno asc, Cno desc);
   create unique index Stusname on `S-T`.Student(Sname);
   ```

   

7. **修改索引**

   ```
   /* 教材版本： alter index SCno rename to SCSno */
   alter table `S-T`.SC rename index SCno to SCSno;
   ```

   

8. **删除索引**

   ```sql
   /* drop index Stusname */
   drop index Stusname on `S-T`.Student;
   ```

   

##### 数据查询

1. ###### **表单查询**

   *SELECT [All | DISTINCT] <目标列表达式> [, <目标列表达式>] ...*

   ​	*FROM <表名或视图名> [, <表名或视图名> ... ] | (<SELECT 语句>) [AS] <别名>*

   ​	*[WHERE <条件表达式>]*

   ​	*[GROUP BY <列名 1> [HAVING <条件表达式>]]*

   ​	*[ORDER BY <列名 2> [ASC | DESC]]*

   1. **选择表中若干列**

      1. 查询指定列

         ```sql
         select Sno, Sname, Sdept
                 	from `S-T`.Student;
         
         ```

      2. 查询全部列

         ```sql
         select *
                 from `S-T`.Student;
         
         ```

      3. 查询经过计算的值

         ```sql
         select Sname Name, 'Year of Birth:' Birth, 2014-Sage Birthday, LOWER(Sdept) Department
         		from `S-T`.Student;
         
         ```

      

   2. **选择表中的若干元组**

      1. 消除取值重复的行

         ```sql
         select Sno		/* = select ALL Sno */
         	from `S-T`.SC;
          
         select distinct Sno		/* DISTINCT 去掉重复行 */
         	from `S-T`.SC;
         
         ```

         

      2. 查询满足条件的元组

         ```sql
         /* 比较大小 */
         select Sname
         	from `S-T`.Student
             where Sdept='CS';
             
         select distinct Sno
         	from `S-T`.SC
             where Grade<60;
         
         /* 确定范围 */
         select Sname, Sdept, Sage
         	from `S-T`.Student
             where Sage between 20 and 23;
             
         select Sname, Sdept, Sage
         	from `S-T`.Student
             where Sage not between 20 and 23;
         
         /* 确定集合 */
         select Sname, Ssex
         	from `S-T`.Student
             where Sdept in ('CS', 'MA', 'IS');
             
         select Sname, Ssex
         	from `S-T`.Student
             where Sdept not in ('CS', 'MA', 'IS');
             
         /* 字符匹配 [NOT] LIKE '<匹配窜>' [ESCAPE '<换码字符>']*/
         /* % 代表任意长度(可为 0) */
         /* _代表任意单个字符 */
         select *
         	from `S-T`.Student
             where Sno like '201215121';	/* => Sno='201215121*/
         
         select Sname, Sno, Ssex
         	from `S-T`.Student
             where Sname like '刘%';
             
         select Sname, Sno, Ssex
         	from `S-T`.Student
             where Sname not like '刘%';
             
         select Cno, Cname, Ccredit
         	from `S-T`.Course
             where Cname like 'DB/_Design' escape '/';
             
         select *
         	from `S-T`.Course
             where Cname like 'DB/_%i__' escape '/';		/* 以DB_开头，且倒数第二个字符为 i */
         
         /* 涉及空值的查询 */
         select Sno, Cno
         	from `S-T`.SC
             where Grade is NULL;
         
         select Sno, Cno
         	from `S-T`.SC
             where Grade is not NULL;
         
         /* 多重条件查询 */
         select *
         	from `S-T`.Student
             where Sdept='CS' and Sage < 20;
         
         select Sname, Ssex
         	from `S-T`.Student
             where Sdept='CS' or Sdept='MA' or Sdept='IS';	/* => where Sdept not in ('CS', 'MA', 'IS') */
         
         ```

         

      3. ORDER BY 子句

         *ORDER BY [列名 [ASC | DESC], ... ];	ASC: 升序（默认） DESC: 降序*

         ```sql
         select Sno, Grade
         	from `S-T`.SC
             where Cno = '3'
             order by Grade DESC;
             
         select *
         	from `S-T`.Student
             order by Sdept, Sage DESC;	/* 按所在系的系号升序排列，同系按年龄降序排列 */
         
         ```

         

      4. 聚合函数

         *COUT(*)	统计元组个数

         *COUT([DISTINCT | ALL] <列名>)	统计一列中值的个数*

         *SUM([DISTINCT | ALL] <列名>)	计算一列值的总和（此列必须是数值型）*

         *AVG([DISTINCT | ALL] <列名>)	计算一列值的平均值（此列必须是数值型）*

         *MAX([DISTINCT | ALL] <列名>)	求一列值中的最大值*

         *MIN([DISTINCT | ALL] <列名>)	求一列值中的最小值*

         ```sql
         /* 查询学生总人数 */
         select count(*)
         	from `S-T`.Student;
             
         /* 查询选修了课程的学生人数 */
         select count(distinct Sno)
         	from `S-T`.SC;
         
         /* 计算选修1号课程的学生平均成绩 */
         select avg(Grade)
         	from `S-T`.SC
             where Cno='1';
         
         /* 查询选修1号课程的学生最高分 */
         select max(Grade)
         	from `S-T`.SC
         	where Cno='1';
         
         /* 查询学生201215121选修课程的总学分 */
         select sum(Ccredit)
         	from `S-T`.SC, `S-T`.Course
             where Sno='201215121' and `S-T`.SC.Cno=`S-T`.Course.Cno;
         
         ```

         

      5. GROUP BY 子句

         *WHERE子句中是不能用聚合函数作为条件表达式的，聚合函数只能用于 SELECT子句和 GROUP BY中的 HAVING子句*

         ```sql
         /* 求各个课程号及相应的选课人数 */
         select Cno, count(Sno)
         	from `S-T`.SC
             group by Cno;
             
         /* 查询选修了三门以上课程的学生学号 */
         select Sno
         	from `S-T`.SC
             group by Sno
             having count(*)>2;
         
         /* 查询平均成绩大于等于90分的学生学号和平均成绩 */
         select Sno, avg(Grade)
         	from `S-T`.SC
             group by Sno
             having avg(Grade)>=80
         
         ```

2. ###### **连接查询**

   1. **等值与非等值连接查询**

      *当连接运算符为=时，称为**等值连接**；使用其他运算符称为**非等值连接***

      *[<表名>. ] <列名 1> <比较运算符> [<表名 2>. ] <列名 2>*

      *[<表名 1>. ] <列名 1> BETWEEN [<表名 2>. ] <列名 2> AND [<表名 2>. ] <列名 3>*

      ```sql
      /* 查询每个学生及其选修课程的情况 */
      select Student.*, SC.Cno, SC.Grade
      	from `S-T`.Student, `S-T`.SC
          where Student.Sno=SC.Sno;
      
      /* 查询选修2号课程且成绩在60分以上的所有同学的学号和姓名 */
      select Student.Sno, Sname
      	from `S-T`.Student, `S-T`.SC
          where Student.Sno=SC.Sno and 
      			SC.Cno=2 && SC.Grade > 60;
      
      ```

      

   2. **自身连接**

      *一个表与其自身进行连接，称为表的**自身连接***

      ```sql
      /* 查询每一门课的间接先修课（即先修课的先修课） */
      select First.Cno, First.Cname, Second.Cpno
      	from `S-T`.Course First, `S-T`.Course Second
          where First.Cpno=Second.Cno;
      
      ```

      

   3. **外连接**

      *左外连接列出左边关系中所有的元组，右外连接列出右边关系中所有元组*

      ```sql
      select Student.*, SC.Cno, SC.Grade
      	from `S-T`.Student left outer join `S-T`.SC
          on (Student.Sno=SC.Sno);
      
      ```

      

   4. **多表连接**

      ```sql
      /* 查询每个学生的学号、姓名、选修课的课程名及成绩 */
      select Student.Sno, Sname, Cname, Grade
      	from `S-T`.Student, `S-T`.Course, `S-T`.SC
          where Student.Sno=SC.Sno and Course.Cno=SC.Cno;
      
      ```

      | Sno       | Sname | Cname    | Grade |
      | --------- | ----- | -------- | ----- |
      | 201215121 | 李勇  | 数据库   | 92    |
      | 201215121 | 李勇  | 数学     | 85    |
      | 201215121 | 李勇  | 信息系统 | 88    |
      | 201215122 | 刘晨  | 数学     | 90    |
      | 201215122 | 刘晨  | 信息系统 | 80    |

      

3. ###### 嵌套查询

   1. 带有IN谓词的子查询

      *有些嵌套查询可以用连接运算替代，有些是不能替代的*

      *如果子查询的查询条件不依赖于父查询，称为不相关子查询*

      *如果子查询的查询条件依赖于父查询，称为相关子查询，整个查询语句称为相关嵌套查询*

      *求解相关子查询不能像求解不相关子查询那样一次将子查询求解出来，然后求解父查询*

      ```sql
      /* 查询与“刘晨”在同一个系学习的学生 */
      select *				/* 解法一：嵌套查询 */
      	from `S-T`.Student
          where Sdept in (
      		select Sdept
      			from `S-T`.Student
      			where Sname='刘晨'
          );
          
      select S1.*				/* 解法二：自身连接 */
      	from `S-T`.Student S1, `S-T`.Student S2
          where S1.Sdept=S2.Sdept and S2.Sname='刘晨';
      
      ```

      | Sno       | Sname | Ssex | Sage | Sdept |
      | --------- | ----- | ---- | ---- | ----- |
      | 201215121 | 李勇  | 男   | 20   | CS    |
      | 201215122 | 刘晨  | 女   | 19   | CS    |

      

      ```sql
      /* 查询选修了课程名为”信息系统“的学生学号和姓名 */
      select Sno, Sname
      	from `S-T`.Student
          where Sno in 
      		(select Sno
      			from `S-T`.SC
                  where Cno in
      				(select Cno
      					from `S-T`.Course
      						where Cname='信息系统'		/* 解法一：嵌套查询 */
      				)
      		);
      
      select Student.Sno, Sname
      	from `S-T`.Student, `S-T`.Course, `S-T`.SC		/* 解法二：连接查询 */
          where Student.Sno=SC.Sno and 
      		SC.Cno=Course.Cno and 
      		Course.Cname='信息系统';
      
      ```

      | Sno       | Sname |
      | --------- | :---- |
      | 201215121 | 李勇  |
      | 201215122 | 刘晨  |

      

   2. 带有比较运算符的子查询

      ```sql
      /* 查询与“刘晨”在同一个系学习的学生 */
      select *		/* 解法三：带运算符的嵌套查询，内查询结果唯一，故可用 = => IN */
      	from `S-T`.Student
          where Sdept=
      		(select Sdept
      			from `S-T`.Student
      			where Sname='刘晨'
      		);
      
      ```

      | Sno       | Sname | Ssex | Sage | Sdept |
      | --------- | ----- | ---- | ---- | ----- |
      | 201215121 | 李勇  | 男   | 20   | CS    |
      | 201215122 | 刘晨  | 女   | 19   | CS    |

      

      ```sql
      /* 找出每个学生超过他自己选修课程平均成绩的课程号 */
      select Sno, Cno
      	from `S-T`.SC SC1
          where Grade>=(select avg(Grade)		/* 某学生的平均成绩 */
      					from `S-T`.SC SC2
      					where SC1.Sno=SC2.Sno);
      
      ```

      | Sno       | Cno  |
      | --------- | ---- |
      | 201215121 | 1    |
      | 201215122 | 2    |

      

   3. 带有 ANY(SOME) 或 ALL 谓词的子查询

      子查询返回单值时可以用比较运算符，但返回多值时要用 ANY (有的系统用 SOME ) 或者 ALL 谓词修饰符

      ```sql
      /* 查询非计算机科学系中比计算机科学系任意一个学生年龄小的学生姓名和年龄 */
      select Sname, Sage
      	from `S-T`.Student
          where SDept!='CS' and 
      		Sage<any(select Sage
      					from `S-T`.Student
      					where SDept='CS');
      
      select Sname, Sage		/* 聚集函数实现 */
      	from `S-T`.Student
          where SDept!='CS' and 
      		Sage<(select max(Sage)
      				from `S-T`.Student
      				where Sdept='CS')
      
      ```

      | Sname | Sage |
      | ----- | ---- |
      | 王敏  | 18   |
      | 张立  | 19   |

      

      ```sql
      /* 查询非计算机科学系中比计算机科学系所有学生年龄都小的学生姓名和年龄 */
      select Sname, Sage
      	from `S-T`.Student
          where SDept!='CS' and 
      		Sage< ALL(select Sage
      					from `S-T`.Student
      					where Sdept='CS');
      
      select Sname, Sage		/* 聚集函数实现 */
      	from `S-T`.Student
          where SDept!='CS' and 
      		Sage<(select min(Sage)
      				from `S-T`.Student
      				where Sdept='CS');
      
      ```

      | Sname | Sage |
      | ----- | ---- |
      | 王敏  | 18   |

      

   4. 带有 EXISTS 谓词的子查询

      *带有EXISTS谓词的子查询不返回任何数据，只产生逻辑真值“true”或逻辑假值“false”*

      ```sql
      /* 查询所有选修了1号课程的学生姓名 */
      select Sname
      	from `S-T`.Student
          where exists(select *
      					from `S-T`.SC
                          where SC.Sno=Student.Sno and Cno='1');
      
      ```

      | Sname |
      | ----- |
      | 李勇  |

      ```sql
      /* 查询没有选修1号课程的学生姓名 */
      select Sname
      	from `S-T`.Student
          where not exists(select *
      						from `S-T`.SC
      						where SC.Sno=Student.Sno and Cno='1');
      
      ```

      | Sname |
      | ----- |
      | 刘晨  |
      | 张立  |
      | 王敏  |

      

4. ###### 集合查询

   *集合操作主要包括并操作UNION，交操作INTERSECT和差操作EXCEPT*

   1. 并操作

      ```sql
      /* 查询计算机科学系的学生及年龄不大于19岁的学生 */
      select *
      	from `S-T`.Student
          where Sdept='CS'
      	union
      	select *
      		from `S-T`.Student
      		 where Sage<=19;
      
      ```

      | Sno       | Sname | Ssex | Sage | Sdept |
      | --------- | ----- | ---- | ---- | ----- |
      | 201215121 | 李勇  | 男   | 20   | CS    |
      | 201215122 | 刘晨  | 女   | 19   | CS    |
      | 201215123 | 王敏  | 女   | 18   | MA    |
      | 201215125 | 张立  | 男   | 19   | IS    |

   2. 交操作

      ```
      /* 查询计算机科学系的学生与年龄不大于19岁的学生的交集。 Mysql 不支持交操作*/
      select *
      	from `S-T`.SC
          where Sdept='CS'
          intersect
      	select *
      		from `S-T`.Student
      		where Sage<=19;
      
      ```

      

   3. 差操作

      ```
      /* 查询计算机科学系的学生与年龄不大于19岁的学生的差集。 Mysql 不支持差操作*/
      select *
      	from `S-T`.SC
          where Sdept='CS'
          except
      	select *
      		from `S-T`.Student
      		where Sage<=19;
      
      ```

   

5. ###### 基于派生表的查询

   ```sql
   /* 找出每个学生超过他自己选修课程平均成绩的课程号 */
   select SC.Sno, SC.Cno
   	from `S-T`.SC, (select Sno, Avg(Grade) Grade from `S-T`.SC group by Sno) as SC1
   	where SC.Sno=SC1.Sno and SC.Grade>=SC1.Grade;
   
   ```

   | Sno         | Cno  |
   | ----------- | ---- |
   | '201215121' | '1'  |
   | '201215122' | '2'  |

   
