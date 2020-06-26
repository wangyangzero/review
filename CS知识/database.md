#### 常见的数据库对象
1. 表（Table ）

数据库中的表与我们日常生活中使用的表格类似，它也是由行（Row） 和列（Column）组成的。列由同类的信息组成，每列又称为一个字段，每列的标题称为字段名。行包括了若干列信息项。一行数据称为一个或一条记录，它表达有一定意义的信息组合。一个数据库表由一条或多条记录组成，没有记录的表称为空表。每个表中通常都有一个主关键字，用于惟一地确定一条记录。

2. 索引（Index）

索引是根据指定的数据库表列建立起来的顺序。它提供了快速访问数据的途径，并且可监督表的数据，使其索引所指向的列中的数据不重复。

3. 视图（View）

视图看上去同表似乎一模一样，具有一组命名的字段和数据项，但它其实是一个虚拟的表，在数据库中并不实际存。在视图是由查询数据库表产生的，它限制了用户能看到和修改的数据。由此可见，视图可以用来控制用户对数据的访问，并能简化数据的显示，即通过视图只显示那些需要的数据信息。

4. 图表（Diagram）

图表其实就是数据库表之间的关系示意图。利用它可以编辑表与表之间的关系。

5. 缺省值（Default）

缺省值是当在表中创建列或插入数据时，对没有指定其具体值的列或列数据项赋予事先设定好的值。

6. 规则（Rule）

规则是对数据库表中数据信息的限制。它限定的是表的列。

7. 触发器（Trigger）

触发器是一个用户定义的SQL事务命令的集合。当对一个表进行插入、更改、删除时，这组命令就会自动执行。

8. 存储过程（Stored Procedure）

存储过程是为完成特定的功能而汇集在一起的一组SQL 程序语句，经编译后存储在数据库中的SQL 程序。

9. 用户（User）

所谓用户就是有权限访问数据库的人。

#### 创建表
```sql
创建表(方式1) 类似于白手起家来创建表
CREATE TABLE myemployees(
ID NUMBER(10),
NAME VARCHAR2(50),
salary NUMBER(10, 2),
hire_date DATE
)


创建表(方式2),依托于现有表来创建一张新表,不关是依托于现有表，连表中的数据也自动导入到了新表中
CREATE TABLE myemployees2
AS
SELECT emp.employee_id AS ID, emp.last_name NAME, emp.hire_date, emp.salary
FROM employees emp
--依托于现有表来创建一张新表,不关是依托于现有表，连表中的数据也自动导入到了新表中
SELECT *
FROM myemployees2

```

#### crud列
```sql
--增加列
ALTER TABLE myemployees
ADD (email VARCHAR2(30))

--删除列
ALTER TABLE myemployees
DROP COLUMN email

--给列重命名
ALTER TABLE myemployees3
RENAME 
COLUMN salary 
TO sal

--删除表(也可以在pl/sql中的图形化界面上删除)
DROP TABLE myemployees2
```

#### 插入数据
```sql
// 明确只插入一条Value

方式1、 INSERT INTO t1(field1,field2) VALUE(v001,v002);           

//在插入批量数据时方式2优于方式、 

方式2、INSERT INTO t1(field1,field2) VALUES(v101,v102),(v201,v202),(v301,v302),(v401,v402);

// 特别常用

方式3.1、  INSERT INTO t2(field1,field2) SELECT col1,col2 FROM t1 WHERE ……

//几乎不用

方式3.2、  INSERT INTO t2 SELECT id, name, address FROM t1

```

#### 更新数据
使用运算式或数据值更新数据
```sql
 --更新terry该GP用戶信息
   update zx_file 
      set zx02='诸葛钱好',
          zxdate = to_date('2012/06/19','YYYY/MM/DD'),  --日期格式化
          zxuser = DEFAULT   --用DEFAULT提供的默认值
    where zx01 = 'terry';
```
使用子查询更新关联数据
```sql
--将Tiptop GP系统中所有没有登录过的用户密码、开立日期更新为与terry用户的一样
update zx_file 
   set (zx10,zxdate) = (select zx10,zxdate from zx_file where zx01 = 'terry')
  where zx19 = 'N';
```
复制表数据：根据一个表的数据更新另外一个表的数据
```sql
--將Tiptop GP系統分群碼imz_file的資料複製更新到料件基本資料ima_file中對應的欄位  
update ima_file 
   set ima_file.ima07= (select imz_file.imz07  from imz_file where imz_file.imz01 = ima_file.ima06),--ABC碼
       ima_file.ima08= (select imz_file.imz08  from imz_file where imz_file.imz01 = ima_file.ima06),--來源碼
       ima_file.ima09= (select imz_file.imz09  from imz_file where imz_file.imz01 = ima_file.ima06),--其它分群碼一
```

#### 删除数据
```sql
delete from studentinfo where studentid=1;
//提交数据
commit;
//回退
rollback

//example
INSERT INTO STUDENTINFO VALUES('017','SMITH',0,'19-9月-88','北京市怀柔区');
INSERT INTO STUDENTINFO VALUES('018','SMITH LEE',0,'25-11月-86','上海市松江区');
--SAVEPOINT sp1;
INSERT INTO STUDENTINFO VALUES('019','SMITH LEE',0,'25-11月-86','上海市松江区');
--SELECT * FROM STUDENTINFO ;
--ROLLBACK TO sp1;
COMMIT;

```

#### 约束
```sql
--创建表
CREATE TABLE emp8(
--唯一约束
ID NUMBER(12) CONSTRAINT emp8_id_uk UNIQUE, --列级约束
--非空约束
NAME varchar2(30) CONSTRAINT emp8_name_nn NOT NULL, --列级约束
email VARCHAR2(50),
salary NUMBER(15, 2),
--表级约束 
CONSTRAINT emp8_email_uk UNIQUE(email) --指明该约束作用于email这一列上
)
--查询emp8表数据
SELECT * 
FROM emp8
--往emp8表中插入数据
INSERT INTO emp8
VALUES(1002, '韦小宝', 'weixiaobao@qq.com', NULL)
 
--
/*
注意：
email这一列定义了UNIQUE唯一约束
如下：插入了2条数据，插入这2条数据时，email都是插入的null值,数据都能正常插入，说明
了，多条记录都插入null值，并不会违反UNIQUE唯一约束
*/
--往emp8表中插入数据(UNIQUE唯一约束是可以插入null空值的，并且每条记录都可以插入null空值，这并不违反UNIQUE唯一约束)
INSERT INTO emp8
VALUES(1003, '令狐冲', NULL, 2500)
--往emp8表中插入数据(UNIQUE唯一约束是可以插入null空值的，并且每条记录都可以插入null空值，这并不违反UNIQUE唯一约束)
INSERT INTO emp8
VALUES(1004, '张无忌', NULL, 2900)
 
--
--创建表(主键约束)
CREATE TABLE emp9(
--主键约束
ID NUMBER(12) CONSTRAINT emp9_id_pk PRIMARY KEY, --列级约束
--非空约束
NAME varchar2(30) CONSTRAINT emp9_name_nn NOT NULL, --列级约束
email VARCHAR2(50),
salary NUMBER(15, 2),
--表级约束 
CONSTRAINT emp9_email_uk UNIQUE(email) --指明该约束作用于email这一列上
)

--
SELECT * 
FROM emp9
/*
PRIMARY KEY 主键约束 (主键约束：不能为null,并且唯一)
*/
--往emp9表中插入数据(插入数据失败)
INSERT INTO emp9
VALUES(NULL, '张无忌', 'zhangwuji@qq.com', NULL)
--往emp9表中插入数据
INSERT INTO emp9
VALUES(100, '郭靖', 'test@qq.com', NULL)
--往emp9表中插入数据(插入数据失败)
INSERT INTO emp9
VALUES(100, '杨过', 'yangguo@qq.com', 8050.67)
--往emp9表中插入数据(插入数据成功)
INSERT INTO emp9
VALUES(108, '双儿', 'shuanger@qq.com', 9865.32)
 
 
--创建表(主键约束)
CREATE TABLE emp10(
--主键约束
ID NUMBER(12),
--非空约束
NAME varchar2(30) CONSTRAINT emp10_name_nn NOT NULL, --列级约束
email VARCHAR2(50),
salary NUMBER(15, 2),
--表级约束 
CONSTRAINT emp10_email_uk UNIQUE(email), --指明该约束作用于email这一列上
CONSTRAINT emp10_id_pk PRIMARY KEY(ID)
)
--NAME列已经定义了非空约束，我这里又定义了name列的默认值为null
ALTER TABLE emp10 
MODIFY (NAME VARCHAR2(30) DEFAULT NULL);
--
SELECT * 
FROM emp10
--插入数据失败，插入数据报错
INSERT INTO emp10 (id, email, salary)
VALUES(10, 'test@qq.com', 8500.66)
--插入数据成功
INSERT INTO emp10 (id, name, email, salary)
VALUES(12, '张三', 'test@qq.com', 8500.66)
 
 
--
/*
外键约束
*/
 
--创建表(外键约束)
CREATE TABLE emp11(
--主键约束
ID NUMBER(12),
--非空约束
NAME varchar2(30) CONSTRAINT emp11_name_nn NOT NULL, --列级约束
email VARCHAR2(50),
salary NUMBER(15, 2),
department_id NUMBER(10),
--表级约束 
CONSTRAINT emp11_email_uk UNIQUE(email), --指明该约束作用于email这一列上
CONSTRAINT emp11_id_pk PRIMARY KEY(ID),
--外键约束
CONSTRAINT emp11_department_id_fk FOREIGN KEY(department_id) REFERENCES departments(department_id)
)
--

SELECT * 
FROM emp11
--
SELECT * 
FROM departments
--插入数据成功(因为departments表中的department_id列中有10这个值，也就是说departments表中有10号部门)
INSERT INTO emp11
VALUES(100, 'AA', 'aa@q63.com', 9650.55, 10)
--违反了主外键约束，插入数据失败(因为departments表中的department_id列中没有680这个值，也就是说departments表中没有680号部门)
INSERT INTO emp11
VALUES(101, 'CC', 'cc@q63.com', 8265.36, 680)
 
/*
```

#### 视图
创建视图
```
CREATE VIEW   empvu

AS SELECT  employee_id, last_name, salary

FROM    employees;
```
删除视图
```
DROP VIEW empvu;
```

#### 序列
生成主键
1)  INCREMENT BY用于定义序列的步长，如果省略，则默认为1，如果出现负值，则代表Oracle序列的值是按照此步长递减的。

2)  START WITH 定义序列的初始值(即产生的第一个值)，默认为1。

3)  MAXVALUE 定义序列生成器能产生的最大值。选项NOMAXVALUE是默认选项，代表没有最大值定义，这时对于递增Oracle序列，系统能够产生的最大值是10的27次方;对于递减序列，最大值是-1。

4)  MINVALUE定义序列生成器能产生的最小值。选项NOMAXVALUE是默认选项，代表没有最小值定义，这时对于递减序列，系统能够产生的最小值是?10的26次方;对于递增序列，最小值是1。

5)  CYCLE和NOCYCLE 表示当序列生成器的值达到限制值后是否循环。CYCLE代表循环，NOCYCLE代表不循环。如果循环，则当递增序列达到最大值时，循环到最小值;对于递减序列达到最小值时，循环到最大值。如果不循环，达到限制值后，继续产生新值就会发生错误。

6)  CACHE(缓冲)定义存放序列的内存块的大小，默认为20。NOCACHE表示不对序列进行内存缓冲。对序列进行内存缓冲，可以改善序列的性能。

大量语句发生请求，申请序列时，为了避免序列在运用层实现序列而引起的性能瓶颈。Oracle序列允许将序列提前生成 cache x个先存入内存，在发生大量申请序列语句时，可直接到运行最快的内存中去得到序列。但cache个数也不能设置太大，因为在数据库重启时，会清空内存信息，预存在内存中的序列会丢失，当数据库再次启动后，序列从上次内存中最大的序列号+1 开始存入cache x个。这种情况也能会在数据库关闭时也会导致序号不连续。

7)  NEXTVAL 返回序列中下一个有效的值，任何用户都可以引用。

8)  CURRVAL 中存放序列的当前值,NEXTVAL 应在 CURRVAL 之前指定 ，二者应同时有效。
```
　　CREATE SEQUENCE 序列名

　　[INCREMENT BY n]

　　[START WITH n]

　　[{MAXVALUE/ MINVALUE n| NOMAXVALUE}]

　　[{CYCLE|NOCYCLE}]

　　[{CACHE n| NOCACHE}];
　　
　　//修改示例
　　ALTER SEQUENCE emp_sequence INCREMENT BY 10 MAXVALUE 10000 CYCLE -- 到10000后从头开始 NOCACHE ;
```

#### 过滤
其中salary表示工资条件过滤需要where
```
select name,age,salary from pesion where salary>5000
```
工资在5000至8000之间查询两种方法，建议用第二种
```
select name, salary from pesion where salary>5000 and salary<=8000

select name,salary from pesion where between 4000 and 8000
```
–查询时间段
举例：  
查询雇用时间在1998年2月1日到1998年5月20日之间员工的姓名工号和时间  
姓名：job_name  
工号：job_id  
雇用时间：hire_date  
```
select job_name,job_id,hire_date from Pesion where to_char(hire_date,'yyyy-mm-dd')
between '1998-02-01' and '1998-05-20'
```
模糊查询
–like  
举例：  
一个公司中员工名字含有a的员工有那些  
```
select name from pesion where name like'%a%'    //其中%代表a前面的字符串和a后面的字符串
```
名字中第二位字符是a 员工
```
select name from pesion where name like '_a%'   //一个"_"代表一个字符
```

#### 排序
```
select name,salary  from pesion  order by salary desc   //desc升序排序（由大到小）

select name,salary  from pesion  order by salary asc    //asc降序排序（由小到大）

select name,salary  from pesion  order by salary    //不写desc或者asc 默认从小到大排序
```
多层排序
```
select name,salary  from pesion  order by salary asc,name asc
```
别名排序
```
select name,salary,12*salary annual_sal from pesion  order by annual_sal
```

#### nvl函数
nvl()函数比较常用的是这样的nvl(E1,0)，意思是E1参数查询到为null的情况，就返回0，不为null就返回E1，常用于非空校验
1.NVL函数

NVL函数的格式如下：NVL(expr1,expr2)

含义是：如果oracle第一个参数为空那么显示第二个参数的值，如果第一个参数的值不为空，则显示第一个参数本来的值。
```
例如：

SQL> select ename,NVL(comm, -1) from emp;

 

ENAME NVL(COMM,-1)

------- ----

SMITH -1

ALLEN 300

WARD 500

JONES -1

MARTIN 1400

BLAKE -1

FORD -1

MILLER -1

其中显示-1的本来的值全部都是空值的
```


2 NVL2函数

NVL2函数的格式如下：NVL2(expr1,expr2, expr3)

含义是：如果该函数的第一个参数为空那么显示第二个参数的值，如果第一个参数的值不为空，则显示第三个参数的值。

```
SQL> select ename,NVL2(comm,-1,1) from emp;

 

ENAME NVL2(COMM,-1,1)

------- -----

SMITH 1

ALLEN -1

WARD -1

JONES 1

MARTIN -1

BLAKE 1

CLARK 1

SCOTT 1

上面的例子中。凡是结果是1的原来都不为空，而结果是-1的原来的值就是空。
```


3. NULLIF函数

NULLIF(exp1,expr2)函数的作用是如果exp1和exp2相等则返回空(NULL)，否则返回第一个值。
下面是一个例子。使用的是oracle中HR schema，如果HR处于锁定，请启用

这里的作用是显示出那些换过工作的人员原工作，现工作。
```
SQL> SELECT e.last_name, e.job_id,j.job_id,NULLIF(e.job_id, j.job_id) “Old Job ID”

FROM employees e, job_history j

WHERE e.employee_id = j.employee_id

ORDER BY last_name;

 

LAST_NAME JOB_ID JOB_ID Old Job ID

----------------- ------- ------- -------

De Haan AD_VP IT_PROG AD_VP

Hartstein MK_MAN MK_REP MK_MAN

Kaufling ST_MAN ST_CLERK ST_MAN

Kochhar AD_VP AC_MGR AD_VP

Kochhar AD_VP AC_ACCOUNT AD_VP

Raphaely PU_MAN ST_CLERK PU_MAN

Taylor SA_REP SA_MAN SA_REP

Taylor SA_REP SA_REP

Whalen AD_ASST AC_ACCOUNT AD_ASST

Whalen AD_ASST AD_ASST

可以看到凡是employee。job_id和job_histroy.job_id相等的，都会在结果中输出NULL即为空，否则显示的是employee。job_id
```
4.Coalesce函数

Coalese函数的作用是的NVL的函数有点相似，其优势是有更多的选项。

格式如下：

Coalesce(expr1, expr2, expr3….. exprn)

表示可以指定多个表达式的占位符。所有表达式必须是相同类型，或者可以隐性转换为相同的类型。
返回表达式中第一个非空表达式，如有以下语句： 　　SELECT COALESCE(NULL,NULL,3,4,5) FROM dual 　　其返回结果为：3
如果所有自变量均为 NULL，则 COALESCE 返回 NULL 值。 　　COALESCE(expression1,...n) 与此 CASE 函数等价：
这个函数实际上是NVL的循环使用，在此就不举例子了。

If opportunity doesn’t knock, build a door

#### if-else
1. if语句
```

IF v=... THEN
 END IF;
 
 IF v=... THEN
 ELSE
  t....;
  END IF;
  
IF v=... THEN
 ELSIF v=... THEN
  t...;
  END IFL  
```
2. decode
表示如果value等于if1时，DECODE函数的结果返回then1,...,如果不等于任何一个if值，则返回else
```
DECODE(VALUE,IF1,THEN1,IF2,THEN2,IF2,THEN2,..,ELSE)
```
3. case when
```
CASE WHERE v=... THEN 'vvvvv';
   WHERE v=... THEN 'ffff';
 ELSE
   .....;
END AS '别名';
```

#### 多表连接
1. 外连接
分为左外连接和右外连接（左连接就是左表完全显示而右边只显示和左边连接所匹配的部分）
```
例子1、如果别人要的数据是那个部门没有员工？

Select * from departments d , employees e
    where e.department_id(+)= d.department_id 
    and e.employee_id is null;
-------------------------------------------------------
 SQL1999语法：
     Select * from departments d Left  join employees e
       On d.department_id=  e.department_id
      Where e.employee_id is null;
      

例子2、所有员工那个没有分配部门的员工？
Select * from departments d , employees e
    where e.department_id= d.department_id(+) 
    and e.department_id is null;
----------------------------------------------------
SQL1999语法如下：
     Select * from departments d Right  join employees e
         On d.department_id=  e.department_id
         Where e.department_id is null; 
```
2. 自连接
讲明白的就是自己跟自己连接
```
例子：Select * from shuxin a , shuxin b  where a.zi_id=b.fu_id   
```
3. 笛卡尔集 与叉集(CROSS JOIN)
笛卡尔集的意思是：两张表的连接关系跟没用连一样，就会发生笛卡尔集,
(所以要注意连接条件不然数据过多就会电脑的卡死，数据出现时间长)。
```
  例子：
          Select * from Employees e, Departments d
            Where (不写还是连接关系不成立的条件）;
            
  叉集的写法：
          Select * from Employees  Cross join Departments；
          
        **上面两种条件都结果都相同**。

```
4. 等值连接 (连接条件成立）
```
例子：Select * from Employees ,Departments 
       Where Employees.department_id=Departments.department_id;

```

5. 自然连接与（Using子句）
NATURAL JOIN 子句，会以两张表之间相同的列作为等值连接的条件
如果表的列名相同而类型不同，则连接是会出现错误。
```
例子：
     Select * from employees natural join departments;
       
   Using子句(指定连接的列名）
例子：
     Select * from employees join departments
     Using（department_id）;
     
```
6. 使用on子句来多表连接
```
语法：
Select  * from 
   Table1 table1 Join Table table2
        On table1.table_id =table2.table_id
   Join Table3 table3
        On table2.table3_id=table3.table3_id;
        
```
7. 满外连接
```
例子：Select * from employees e full join departments d
        On e.department_id=d.department_id;
```

#### 组函数

1. COUNT() 统计行数
```
select count(*) from scott.emp;
```
2. AVG(number) 平均值
```
select sum(sal)/count(ename) from scott.emp;
select avg(sal) from scott.emp;
```
3. SUM(number) 求和
```
select sum(sal) from scott.emp;
```
4. MAX() 最大值
```
select max(sal) from scott.emp;
```
5. MIN() 最小值
```
select min(sal) from scott.emp;
```

#### group by 和 having
select语句中含有组合函数时才能使用group by
having类似于where，只是在条件筛选时含有组函数则必须用having代替where
```sql
//将person表中同名的组筛选出来
SELECT name FROM person 
GROUP BY name;

select AVG(age),id from emploies
group by id
having sum(time) > 10
```

#### 子查询
例如：查找收入大于员工7566的其他员工的姓名
```
分类：通过分析是单行子查询
实现步骤：先找7566员工的收入
select sal from emp where empno=7566;
第二步：查找工资大于2975的员工姓名（2975是7566的工资）
select ename from emp where sal>‘2975’;
最后将代码合并:
select ename from emp where sal>(select sal from emp where empno=7566) order by ename;
```
任务：显示和雇员7369从事相同的工作并且工资大于雇员7876的雇员的姓名和工作
通过分析：单行子查询
```
第一步：查找7369员工的工种
select job from emp where empno=7369;
第二步：查找员工7876的工资
select sal from emp where empno=7876;
第三步：查找工种为CLERK（7369的工种）、工资大于1100（7876的工资）员工的姓名和工种
select ename,job from emp where job=‘CLERK’ and sal>1100;
汇总代码：
select ename,job from emp where sal>(select sal from emp where empno=7876) and job=(select job from emp where empno=7369);
```