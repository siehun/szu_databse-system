```
create database lab1;
use lab1;
create table DEPT( DEPTNO int not null primary key,
                   DNAME varchar(128), 
                   LOC varchar(128))ENGINE=INNODB;
```







```
insert into DEPT (DEPTNO, DNAME, LOC) 
VALUES (10, 'ACCOUNTING', 'LONDON'),
(20,'RESEARCH','PRESTON'),
(30,'SALES','LIVERPOOL'),
(40, 'OPERATIONS', 'STAFFORD'),
(50, 'MARKETING', 'LUTON');
```



```
create table EMP(EMPNO int not null primary key,
ENAME char(128) not null check(ename = UPPER(ename)),
JOB char(128),
MGR int,
HIREDATE date check(hiredate <= SYSDATE()),
SAL int check(sal > 500) ,
COMM int default null ,
DEPTNO int not null)ENGINE=INNODB;
```



```
alter table  EMP add constraint fk_emp_dept_id foreign key (DEPTNO) references DEPT(DEPTNO);
```



```
INSERT INTO emp (EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO) VALUES   
(7369, 'SMITH', 'CLERK', 7902, '1990-12-17', 13750, NULL, 20),  
(7499, 'ALLEN', 'SALESMAN', 7698, '1989-02-20', 19000, 6400, 30),  
(7521, 'WARD', 'SALESMAN', 7698, '1993-02-22', 18500, 4250, 30),  
(7566, 'JONES', 'MANAGER', 7839, '1996-04-02', 26850, NULL, 20),  
(7654, 'MARTIN', 'SALESMAN', 7698, '1993-09-28', 15675, 3500, 30),  
(7698, 'BLAKE', 'MANAGER', 7839, '1990-05-01', 24000, NULL, 30),  
(7782, 'CLARK', 'MANAGER', 7839, '1996-06-09', 27500, NULL, 10),  
(7788, 'SCOTT', 'ANALYST', 7566, '1987-04-19', 19500, NULL, 20),  
(7839, 'KING', 'PRESIDENT', NULL, '1993-11-17', 82500, NULL, 10),  
(7844, 'TURNER', 'SALESMAN', 7698, '1992-09-08', 18500, 6250, 30),  
(7876, 'ADAMS', 'CLERK', 7788, '1996-05-23', 11900, NULL, 20),  
(7900, 'JAMES', 'CLERK', 7698, '1995-12-03', 12500, NULL, 30),  
(7902, 'FORD', 'ANALYST', 7566, '1991-12-03', 21500, NULL, 20),  
(7934, 'MILLER', 'CLERK', 7782, '1995-01-23', 13250, NULL, 10),  
(3258, 'GREEN', 'SALESMAN', 4422, '1995-07-24', 18500, 2750, 50),  
(4422, 'STEVENS', 'MANAGER', 7839, '1994-01-14', 24750, NULL, 50),  
(6548, 'BARNES', 'CLERK', 4422, '1995-01-16', 11950, NULL, 50);  
```



```
use lab1;
select *
from emp
where ENAME like '%S';
```



```
select ENAME as 员工姓名, round((sal / 22), 2) 日工资, round(sal / (22 * 8), 2) 时工资
from emp
where DEPTNO = 30;
```



```
select JOB, avg(SAL) * 12 as 年薪
from emp
group by JOB
having count(*) > 2;
```



```
select emp.DEPTNO
from emp where emp.COMM
group by emp.DEPTNO having avg(emp.COMM) > 0.25 * avg(emp.SAL);
```



```
select dept.DNAME, avg(SAL) * 12 年薪不含管理者和总理
from emp,
     dept
where emp.JOB != 'MANAGER'
  and emp.JOB != 'PRESIDENT'
  and emp.DEPTNO = dept.DEPTNO
group by emp.DEPTNO;
```



```
create view emp_view as
select ENAME, EMPNO, JOB, HIREDATE
from emp;
```

