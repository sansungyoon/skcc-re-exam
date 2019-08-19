## 1.
~~~
select a.id as id, a.type as type, a.status as status, a.amount as amount, a.amount-b.average as difference
from account a join (select avg(amount) as average, type from account where status = 'Closed' group by type) b
on a.type = b.type
where a.status = 'Active'
;
~~~

![](/img/2-1.PNG)
![](/img/2-3.PNG)
![](/img/2-2.PNG)

## 2.
~~~
create database problem2;

create external table solution
(id INT, fname STRING, lname STRING, address STRING, city STRING, state STRING,
zip STRING, birthday STRING, hireday STRING)
row format SERDE 'parquet.hive.serde.ParquetHiveSerDe'
STORED AS
INPUTFORMAT "parquet.hive.DeprecatedParquetInputFormat"
OUTPUTFORMAT "parquet.hive.DeprecatedParquetOutputFormat"
LOCATION '/user/training/problem2/data/employee';
~~~
![](/img/2-4.PNG)

## 3.

~~~
create external table solution (id STRING, fname STRING, lname STRING, hphone STRING);

insert into problem3.solution
select b.id as id , b.fname as fname, b.lname as lname, regexp_replace(b.hphone,'[ )(-]','') as hphone
from account a join customer b
on a.custid=b.id
where a.amount >= 0
;

~~~
![](/img/2-5.PNG)
![](/img/2-6.PNG)

## 4.
~~~
create database problem4;

create external table employee1
(
cust_id int,
lname string,
fname string,
address string,
city string,
state string,
zip_cd string
)
row format delimited
fields terminated by '\t'
location '/user/training/problem4/data/employee1/.'
~~~
![](/img/2-7.PNG)

~~~
create external table employee2
(
cust_id int,
number int,
lname string,
fname string,
address string,
city string,
state string,
zip_cd string
)
row format delimited
fields terminated by ','
location '/user/training/problem4/data/employee2/.'
~~~
![](/img/2-8.PNG)


~~~
create table solution as
select res.cust_id, res.fname, res.lname, res.address, res.city, res.state, res.zip_cd
from
(
select cust_id, fname, lname, address, city, state, substr(zip_cd,0,5) zip_cd
from employee1
where state = 'CA'

union all

select cust_id, initcap(fname) fname, initcap(lname) lname, address, city, state, zip_cd
from employee2
where state = 'CA'
) res
;
~~~
![](/img/2-9.PNG)

~~~
insert OVERWRITE DIRECTORY '/user/training/problem4/solution/'
row format delimited
FIELDS TERMINATED BY '\t'
SELECT * FROM solution
;
~~~

![](/img/2-10.PNG)

## 5.
~~~
select concat_ws(',', cus.fname, cus.lname, cus.zip)
from customer cus
where city='Palo Alto'
and state = 'CA'

union all

select concat_ws(',', emp.fname, emp.lname, emp.zip)
from employee emp
where city='Palo Alto'
and state = 'CA'
;
~~~
![](/img/2-11.PNG)
