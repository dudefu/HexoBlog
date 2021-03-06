---
title: 常见SQL语句
categories: Java
tags: JavaSE
abbrlink: c20e1edd
date: 2018-01-10 19:24:42
---
```SQL
create database employee
select * from emp;
delete from emp where empno=9999;
select ename,sal from emp ;
select * from emp where sal > 2000;
select * from emp where deptno = 20 and sal > 2000 ;
select * from emp where deptno = 20 or sal > 2000 ;
select * from emp where sal between 1000 and 3000 ;
select * from emp where empno = 7788 or empno = 7369 or empno=7521;
<!---more--->
-- in
select * from emp where empno in(7788,7369,7521);
-- DISTINCT 去重
select job from emp ;
select DISTINCT job from emp ;
-- 别名（字段，表）
select empno  员工编号,ename 员工姓名 from emp ;
select ename,sal,sal*1.05 新工资 from emp;

select ename,sal from emp e ;
select e.ename,e.sal from emp e ;
-- null 的判断
select * from emp where comm is not null ;

--模糊查询
--查询所有S打头的员工信息（模糊查询） % 代表0到多个字符
select * from emp where ename like 'S%';
--查询所有N结尾的
select * from emp where ename like '%N';
--查询所有包含S的
select * from emp where ename like '%S%';
--查询所有第二个字符为L的员工信息
select * from emp where ename like '__L%';

--排序  (默认升序   order by字段 [asc]  | desc)
--默认  升序
select * from emp order by sal ;
--降序
select * from emp order by sal desc ;
--按照工资的降序排序，如果工资一样的，则按照empno的升序排序(用逗号)
select * from emp order by sal desc , empno ASC;
--限制结果查询（limit m,n)  m代表起始索引，n代表记录的数目,limit 仅适用于MySQL
select * from emp limit 5,5;

--查询20号部门工资最高的员工的信息
select * from emp where deptno = 20 order by sal desc limit 0,1 ;

--计算3的15次方

--数学函数
select ABS(10);  --绝对值
select CEIL(-12.3);   --向上取整
select FLOOR(12.5);   --向下取整
select ROUND(12.6);   --四舍五入
select ROUNT(12.49，1);   
select POW(3,3);   --幂运算
select RAND();     --随机数[0,1)


--字符串函数
desc emp ;
select LENGTH(ename) from emp ;    --获取字符串长度
select length('this is a apple');

select LOWER(ename) from emp ;  --转换为小写
select UPPER('this is a page');   -- 转换大小写
select substr('aabbcc',1,2);      -- 从1开始
select LPAD('smith',10,'*');  -- 开始字符串，总长度，padstr填充的字符
select RPAD('smith',10,'*');  -- 右填充
select TRIM('     smi  th');  --去空格

-- 日期
select NOW();
select sysdate();
select CURRENT_DATE();     --当前日期
select current_time();     --当前时间
select YEAR('1998-09-09');
select MONTH('1998-09-09');
select DAY('1998-09-09');
select DATE_ADD('1998-09-09',INTERVAL 2 YEAR);
select * from student ;
alter table student add birthday date AFTER age;

-- 聚合函数
--count /sum/avg/max/min

-- 员工数（统计记录数）
select count(*) from emp ;
select count(1) from emp ;

-- 统计非空字段数目
select count(comm) from emp;
-- SUM
select sum(sal) from emp ;
-- AVG 
select avg(sal) from emp ;
-- MIN
select min(sal) from emp ;
-- MAX 
select max(sal) from emp ;


-- 分组函数 GROUP BY deptno
-- 每个部门的平均工资
-- group by 根据条件字段的值返回相应的记录数；但是在select字句中，只能出现聚合函数或者分组的条件字段。
select deptno, avg(sal) from emp GROUP BY deptno ;

-- 各个职位员工数？ job
select job, count(*) from emp group by job;
-- 平均工资大于2000的部门的部门编号和平均工资？   where 不能放group by之后，having可以，group by 和having  经常配套使用
   -- 1.求出每个部门的平均工资
	 -- 2.平均工资>2000
select deptno,avg(sal) from emp GROUP BY deptno having avg(sal) > 2000;


-- where 和 having 的区别
-- 查询工资大于1500的每个部门的平均工资
select deptno, avg(sal) from emp where sal > 1500 GROUP BY deptno ;
-- 查询平均工资大于1500的部门编号和平均工资
select deptno,avg(sal) from emp GROUP BY deptno having avg(sal) > 1500 ;


-- 加密函数
select MD5('root');
select SHA('root');
select PASSWORD('root');

-- 外键约束： foreign key
create table classroom(
	cid int PRIMARY key,
	cname varchar(20)
);
alter table student add CONSTRAINT FK_CID FOREIGN KEY(cid) REFERENCES classroom(cid);

select * from emp ;


-- 高级查询
-- 多表查询
-- 查询的结果集分布于多张表
-- 查询员工编号为7788的员工姓名和部门名称。
-- 内连接 ，注意：1 内连接的结果集与连接顺序无关 2 在多张表中都出现的数据才会出现在内连接的结果集中
select e.ename,d.dname from emp e ,dept d where e.empno = 7788 and e.deptno = d.deptno;
-- inner join ...on....
select ename,dname from emp inner join dept on emp.deptno = dept.deptno ;
-- inner join ...using... (局限,当deptno不同时)
select ename,dname  from emp inner join dept using(deptno)

```