# database class

```mysql
# SELECT FROM : 데이터 조회시 사용
SELECT *
FROM world.country;

SELECT code, name, continent, population, gnp
from world.country;

use world;

select code, name, gnp
from country;

# 테이블 구조 확인 desc: description
desc city;

# WHERE절 : 특정 조건에 맞는 데이터를 검색하는 쿼리문
# 비교, 논리 연산자, IN, LIKE

# 인구가 1억 이상인 국가를 출력(국가코드, 국가이름, 인구수)
select code, name, population
from country
where population >= 100000000;

# 논리연산자사용. 인구가 8천만 ~ 1억인 국가를 출력(code, name, population)
select code, name, population
from country
where population >= 80000000 and population <= 100000000;

#between (범위내의 데이터 출력시 사용, 논리연산자보다 편할수 있음)
select code, name, population
from country
where population between 80000000 and 100000000;

# 아시아와 아프리카 대륙의 데이터를 출력
# continent "Asia", "Africa" ,, = 는 비교연산자로 논리연산자보다 우선순위가 높다.
select code, name, continent
from country
where continent = "Asia" OR continent = "Africa";

# IN
select code, name, continent
from country
where continent not in ("Asia", "Africa");

# LIKE : 특정 문자열이 포함된 데이터 출력시 사용
# 정부 형태가 republic이 포함된 데이터를 출력하고 싶을때...
select code, name, governmentform
from country
where governmentform like "%Republic%";

# ORDER BY: 데이터를 정렬
# 인구수를 기준으로 오름차순으로 국가 리스트를 출력
select code, name, population
from country
order by population desc;

# city에서 국가 code를 오름차순으로 정렬, 인구수를 내림차순 정렬
select name, countrycode, population
from city
order by countrycode asc, population desc;

# country 테이블에서 아시아 국가중 인구수가 5000만이 넘는 데이터를 인구수 순으로 내림차순해서 출력하기
select code, name, continent, population
from country
where continent = "Asia" and population >= 50000000
order by population desc;

# LIMIT : 데이터의 출력수를 제한할때 사용, 업데이트시에 자주 사용한다.
# 인구가 가장 많은 상위 3개 도시를 출력
select countrycode, name, population
from city
order by population desc
limit 10;
# limit 2, 3; -> 상위2개 스킵후 3가지.

# country 테이블에서 아시아,유럽 국가중에 기수명이 70세 이상인 국가를 출력, GNP 순으로 4 ~ 8위 정렬후 출력
select code, name, continent, gnp,lifeExpectancy
from country
where continent in ('ASIA', "EUROPE") and lifeExpectancy >= 70
order by GNP desc
limit 3, 5;

# 국가별 인구 밀도를 출력하세요.
select code, name, surfaceArea, population, population / surfacearea as pps
from country
where (population / surfacearea) >= 100;

# GROUP BY : 특정 컬럼을 기준으로 동일한 데이터를 합쳐서 출력하는 방법
# 기준 데이터가 아닌 컬럼은 결합함수를 이용해서 출력.
# 결합함수 : count, max, min, avg, sum ....

# COUNT
# 대륙별 몇개의 국가가 있는지 출력하세요.
select count(name), continent
from country
group by continent;

# 대륙별 총 인구수를 출력하세요. 내림차순으로
# 대륙별 총 인구수가 5억 이상인 데이터만 출력하세요
select sum(population) as population, continent
from country
group by continent
having population >= 500000000;

# 대륙별 평균 인구수, 평균 GNP, 1인당 GNP 출력
# 1인당 GNP가 0.01인 대륙을 조회하고 1인당 GNP 순으로 내림차순하세요
select continent, avg(population), avg(gnp), avg(gnp) / avg(population) as GPA
from country
group by continent
having GPA >= 0.01
order by GPA desc;

# DDL : 데이터 정의어
# 데이터베이스, 테이블 CRUD
# 데이터베이스 생성
create database test;
use test;
select database();

# 테이블 생성
create table user1(
	user_id INT,
	name varchar(20),
    email varchar(30),
    age int,
    rdate DATE
); # 제약조건없는 테이블 user1
show tables;
desc user1;

create table user2(
	user_id INT primary key auto_increment,
	name varchar(20) not null,
    email varchar(30) unique not null,
    age int default 30,
    rdate timestamp
);
desc user2;

# 수정 DDL : ALTER 사용
show variables like "character_set_database";
alter database test character set = utf8;

# ALTER ADD : 컬럼 추가
desc user2;
alter table user2 add tmp text;

# ALTER MODIFY : 컬럼 수정
alter table user2 modify tmp int;

# ALTER DROP : 컬럼삭제
alter table user2 drop tmp;

# DROP : 데이터베이스 삭제
create database tmp;
show databases;
drop database tmp;

show tables;
create table tmp(id int);
drop table tmp;

# INSERT
use test;
desc user1;
insert into user1(user_id, name, email, age, rdate)
values (1, "peter", "p@gamil.com", 23, "2017-03-23");
select * from user1;

desc user2;
insert into user1(user_id, name, age)
values ("peter", "p2@gamil.com", 21);
select * from user2;


# select 결과 데이터를 insert
use world;
create table tmp(
	name varchar(50),
    code char(3),
    population int
);
desc tmp;
insert into tmp
select name, code, population
from country
order by population desc
limit 3;

select * from tmp;

# UPDATE
use test;
select * from user1;

update user1
set email = "fds@fc.com"
where age = 21
limit 3;

# DELETE(DML)데이터 조작어, truncate(DDL) 데의터 정의어 -> 내용만 지우고 schema는 지우지 않는다.
select * from user1;

delete from user1
where rdate > "2018-01-01"
limit 10;

truncate user1;

# foreign key
# 데이터의 무결성을 지킬 수 있습니다.
# unique나 primary 제약조건이 있어야 설정 가능.
use test;
create table user(
	user_id int primary key auto_increment,
    name varchar(20),
    addr varchar(20)
);
create table money(
	money_id int primary key auto_increment,
    income int,
    user_id int
);
show tables;
desc money;

# 수정해서 생성
alter table money
add constraint fk_user
foreign key (user_id)
references user (user_id);

desc money;

insert into user (name, addr)
value ("po", "seoul"), ("andy", "pusan");
select * from user;

insert into money (income, user_id)
values (5000, 1), (7000, 2);
select * from money;

insert into money (income, user_id)
values (2000, 3);


delete from user
where user_id = 1;

drop table user;

# on delete, on update 설정
drop table money;

create table money(
	money_id int primary key auto_increment,
    income int,
    user_id int,
    # 외래키 설정
    foreign key (user_id) references user(user_id)
    on update cascade on delete set null # delete하면 null로 바뀌게 하는 설정, cascade라고 쓰면 삭제됨
    # cascade : 참조되는 테이블에서 삭제하거나 수정하면 참조하는 테이블에서도 삭제하거나 수정한다.
);

insert into money (income, user_id)
values (5000, 1), (7000, 2);
select * from money;
select * from user;

update user
set user_id = 3
where user_id = 2
limit 1;

delete from user
where user_id = 1
limit 1;

```



사용 쿼리문 정리

