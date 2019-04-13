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

   
