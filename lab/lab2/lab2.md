### lab2

```
select *
from s
where not exists
    (select * from p where not exists
        (select * from sp where SNO = s.SNO and PNO = p.PNO));


select distinct *
from s
join sp on s.SNO = sp.SNO
join p on sp.PNO = p.PNO
where p.COLOR = 'Red'
group by s.SNO
having count(distinct p.PNO) = (
    select count(DISTINCT p2.PNO)
    from p p2 where p2.COLOR = 'Red'
    );


SELECT DISTINCT S.*
FROM s S
WHERE NOT EXISTS (
    SELECT SP.PNO
    FROM sp SP
    WHERE SP.SNO = 'S2' AND NOT EXISTS (
        SELECT SP2.PNO
        FROM sp SP2
        WHERE SP2.PNO = SP.PNO AND SP2.SNO = S.SNO
    )
);


SELECT DISTINCT S.*
FROM s S
WHERE NOT EXISTS (
    SELECT SP.PNO
    FROM sp SP
    WHERE SP.SNO = 'S2' AND NOT EXISTS (
        SELECT SP2.PNO
        FROM sp SP2
        WHERE SP2.PNO = SP.PNO AND SP2.SNO = S.SNO
    )
)
AND NOT EXISTS (
    SELECT SP3.PNO
    FROM sp SP3
    WHERE SP3.SNO = 'S3' AND SP3.PNO IN (
        SELECT SP4.PNO
        FROM sp SP4
        WHERE SP4.SNO = S.SNO
    )
)
```



2.4 2.5 2.6 6.3 6.6

```
	4.	List all departments that do not have any employees.

	5	For each employee whose salary exceeds his manager's salary, list the 		employee's name and salary and the manager's name and salary.	

	6. 	List the employees who have BLAKE as their manager.

```

```
	3	List the name, job, and department of employees who have the same job as Jones or a salary greater than or equal to Ford.
	
		6	Find all the employees that earn more than the average salary of 			employees in their department.
```

```
# 2.4
select *
from dept
         left join emp on dept.DEPTNO = emp.DEPTNO
where emp.EMPNO is null;


# 2.5
select e1.ENAME, e1.SAL, e2.ENAME, e2.SAL
from emp e1
         join emp e2 on e1.MGR = e2.EMPNO and e1.SAL > e2.SAL;

# 2.6
select e1.ENAME
from emp e1
         join emp e2 on e1.MGR = e2.EMPNO and e2.ENAME = 'BLAKE';

# 6.3
select ENAME, JOB, (select DNAME from dept where emp.DEPTNO = dept.DEPTNO) DNAME
from emp
where JOB = (select JOB from emp where ENAME = 'Jones')
   or SAL >= (select SAL from emp where ENAME = 'Ford');



# 6.6
select e1.ENAME
from emp e1
where SAL > (select avg(SAL) from emp e2 where e2.DEPTNO = e1.DEPTNO);

```

