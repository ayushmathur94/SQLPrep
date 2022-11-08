SQL prep 



show all tables in current db 
>  select table_name from user_tables;

-----------------------------------

create sequence on pk for auto increment 
>  CREATE SEQUENCE  "JCP"."AGENT_SEQ"  MINVALUE 1 MAXVALUE 
    9999999999999999999999999999 INCREMENT BY 1 START WITH 1 CACHE 
    20 NOORDER  NOCYCLE ;


cycle: When sequence reaches its set_limit 
it starts from beginning.

nocycle: An exception will be thrown 
if sequence exceeds its max_value.

-----------------------
trigger for auto increment of a sequence 

create or replace trigger agent_trig before insert on agents for each row begin select agent_seq.NEXTVAL into :new.AGENTS_IDPK from dual ; END;


-------

create table 

create table employees ( 
emp_idpk number(10) primary key  not null ,
firstname varchar(200),
lastname varchar(200),
salary  number(10)
);
-----
or 
------

create table employees ( 
emp_idpk number(10)   not null ,
firstname varchar(200),
lastname varchar(200),
salary  number(10),
primary key (emp_idpk)
);

-------
create a table with both primary key and foreign key ()

create table employees ( 
emp_idpk number(10)   not null ,
firstname varchar(200),
lastname varchar(200),
salary  number(10),
primary key (emp_idpk),
foreign key (department_idfk) references department(department_idpk)
);

-----------
remove constraint of a column of a table
>  alter table customer modify (customer_name null);

--------
describe logical structure of a table 
>    desc tablename ;


--------

add a new column in existing table 
>      alter table employees  add  contact NUMBER (10);

--------
rename column in a table 
>      alter table employees rename column jobcode to roleid ;

-------
remove all data inside a table at once  (only table structure will remain , data will be empty)
>     truncate table temptabledemo ;

--------------------- 
drop table (delete a table from db)

>     drop table  temptabledemo ;

--------------------------------------
DML  (Data Manipulation Language  insert , update ,  delete)
---------------------------------------

insert data / row in table 

>    insert into temptabledemo values (10 , 'ayushmathur02@gmail.com' , 9799044212 , 30 );

------------
or
-------------

>    insert into employees(emp_idpk , firstname , lastname, salary, contact, roleid) values (1, 'ayush' , 'mathur' , 10000 , 9790212220, 1); 


skip a column  (null data by default) (eg , null will be inserted in lastname)
>    insert into employees(emp_idpk, firstname , salary, contact, roleid) values (2 , 'ragini' , 120000, 9081221100, 5);

-------------------------------------

update column value : 
>   update employees set lastname = 'sworn' where emp_idpk = 3;

---------------------------------------
 delete specific rows from table 
>   delete from employees where emp_idpk = 5 ;

>   delete from employees where emp_idpk in (6,7);

------------------------------------------

DCL (Data Control Language  GRANT,  REVOKE  privileges)

Grant <privilege list> on <Relation Name>  to <user>

revole <privilege list> on <Relation Name> to <user>

grant columnlist on tablename to username

revoke columnlist on tablename to username

---------------------------------------------------------------------------------------

TCL ( Transaction Control     COMMIT ,  ROLLBACK,  SAVEPOINT )

-----------------------------------------------------


SQL Operators -  


WHERE  clause : 

select * from employees where firstname like '%alice%' ; 

select * from employees where firstname='alice';

-------------------------------------------

Logical Operators 

AND         (5<2) AND (5>3)         FALSE
OR           (5<2) OR (5>3)           TRUE
NOT           NOT(5<2)                  TRUE


select * from employees where firstname = 'alice' and salary = 40000;

select * from employees where firstname = 'alice' or lastname = 'jackson';
  

--------------------------------------------------
Comparision operators 

=      equal to 
>     greater than 
>=   greater than or equal to 
<     less than 
<=   less than or equal to 
<> or !=    not equal to

select * from employees where firstname = 'alice' and salary <= 40000;

<>  is NOT 
select * from employees where firstname = 'alice' and lastname <> 'jackson'  ;
select * from employees where firstname = 'alice' and lastname != 'jackson'; 
------------------------------------------------

SQL Operators  - Special Operators   (BETWEEN , LIKE , IS NULL , IN , DISTINCT)

BETWEEN      checks an attribute value within range 

>  select * from employees where salary between 40000 and 50000;

-----

LIKE     checks an attribute value matches a given string pattern

>  select * from employees where firstname like 'ay%'  ;

>  select * from employees where firstname like '%ush'  ;

>  select * from employees where firstname like '%yu%' ;

-----

IS NULL    checks an attribute with null value

>  select * from employees where contact is null;

-----

IN     checks an attribute value matchers any value within  the given value list

select * from employees where firstname in ('ayush' , 'ragini' );

-----

DISTINCT       Limits values to unique values

select distinct firstname from employees;

---------------------------------

SQL    Aggregation Functions  ( Avg() , Count() , Max() , Min() , Sum() ) 

Avg()       Returns the average value from specified columns

> select avg(age) from employees;     (output gives avg age of all age values: 24.12)

> select avg(salary) from employees where deptname in ('sales');

output: avg salary of employees in sales deptartment :  97500 (125000 + 70000 / 2) 
-----
 
Count()    Returns number of table rows

>  select count(*) from employees ;        
>  select count(*) from employees where salary <=40000;

-----

Max ()      Returns largest value among the records

>  select  MAX(salary) from employees

>  select firstname , lastname , salary from employees where salary in (
select  MAX(salary) from employees  ) ;

-----

Min()        Returns smallest value among the records

>  select MIN(salary) from employees ; 

-----
sum()        Returns the sum of specified column values 

> select sum(salary) from employees;

--------

GROUP BY    Clause    (arrange identical data into groups )

>   select deptname, max(salary)  from employees  group by  deptname ;


---------

HAVING      Clause  ( mostly used with aggregate functions ie avg , count , max , min , sum )

must follow group by in query and must precede order by if used.

select  max(salary) , deptname from employees group by deptname having count(deptname) = 2; 

select  max(salary) , deptname from employees group by deptname having count(deptname) < 3; 

select  max(salary) , deptname from employees group by deptname having count(deptname) <= 2; 

select  max(salary) , deptname from employees group by deptname having count(deptname) >= 2; 


--------------

ORDER BY     Clause 

used to sort output of select statement .

default sort is ASC ( ascending order) 

desc - descending order  

select * from employees order by salary desc;

-------------

UNION

used to combine result of two or more select statements removing duplicates .

 * each select statement of union must have the same no. of columns 

data type and order of columns in both select must also be same.

select productname from product1 union  select productname from product2 ; 

----------

UNION ALL

works similar to union just that it  does not remove duplicates .

select productname from product1 union all  select productname from product2 ; 


------------

INTERSECT 


--------------


JOINS

combines rows/columns from two or more tables based on a related column between them.

INNER JOIN -  Returns rows only when there is a match in both tables.

select e.firstname , e.lastname , e.salary, d.worklocation from employees e  inner join department d on e.deptname = d.deptname ;
-----

LEFT JOIN -    Returns all rows from left table , even if there are no matches in right table.

select e.firstname , e.lastname , e.salary, d.worklocation from employees e  left join department d on e.deptname = d.deptname where e.deptname = 'videoediting' ;

* videoediting is not present in right table ie in dept table , still record is returned from employee table. 

-----
RIGHT JOIN -  Return all rows from the right table , even if there are no matches in the left table.

--
FULL OUTER JOIN -   Return rows when there is a match in one of the tables. 
gives  null  value if not matched.  
combines result of both left and right outer joins . 
contains all the record from both tables and null for non matching on either sides.

select e.firstname , e.lastname ,e.deptname , d.deptname, e.salary, d.worklocation from employees e  left join department d on e.deptname = d.deptname  UNION  select 
e.firstname , e.lastname  ,e.deptname , d.deptname,
 e.salary, d.worklocation from employees e  right join department d on e.deptname = d.deptname ;

------
SELF JOIN -  Used to join a table to itself using two alias of the same table.   (on the basis of a condition )

-----

CARTESIAN JOIN (Cross Join)

Returns corss product of records from two or more joined tables.

eg table a has 3 records and table b has 4 ,  records
then after cross join or cartesian join, 
we will get 12 records as result.
3 x 4 = 12 


select * from employees cross join department; 

9 x 6 =  54 records are returned.

     




