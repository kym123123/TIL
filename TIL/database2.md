# 데이터베이스2

## 관계형 데이터베이스

* joke 테이블을 생성하고 엔트리를 추가하는 쿼리문

  ```mysql
  CREATE TABLE joke (
  	id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    joketext TEXT,
    jokedate DATE NOT NULL
  ) DEFAULT CHARACTER SET utf8 ENGINE = InnoDB;
  ```

```mysql
# 글 추가 쿼리
INSERT INTO joke SET
	joketext = "why was the empty array stuck outsie? it didn\'t have any keys",
	jokedate = "2017-04-01";

INSERT INTO joke
(joketext, jokedate) VALUES (
"!false - it\'s funny because it\'s true",
"2017-04-01"
);
```



## SQL 쿼리 종류

1) 데이터 정의 언어(Data definition language, DDL) 쿼리 : DDL 쿼리는 데이터가 저장되는 구조를 묘사한다. CREATE TABLE, CREATE DATABASE, ALTER TABLE 쿼리가 DDL에 해당한다.

2) 데이터 조작 언어(Data manipulate language, DML) 쿼리: DML 쿼리는 데이터베이스에 저장된 데이터를 가공하는 쿼리. INSERT, UPDATE, DELETE, SELECT 등이 있다.

* 단순 관계 : 단순한 일대일 관계는 단독 테이블로 구성한다. 글 작성자와 작성자의 이메일 주소의 관계이다. 각 작성자는 이메일 주소를 하나씩 보유하며, 각 이메일 주소는 작성자 한명에 소속된다. 따라서 두 데이터를 별개 테이블에 나눠 담을 필요가 없다.

* 다대일 관계 : 글 작성자는 한명이지만, 작성자는 여러 글을 작성할 수 있다. 이때 글과 작성자의 관계가 다대다 관계돠. 작성자와 글 정보를 한 테이블에 담으면 문제가 발생한다. 같은 데이터가 여러 벌 생기고, 이를 동기화 시키기 힘들며 공간이 낭비된다. 따라서 이런 경우는 데이터를 두 테이블로 나누고 id 칼럼으로 연결하면 문제를 해결할 수 있따.

* 일대다 관계: 다대일 관계의 반대 형태이다. 글과 글 작성자는 다대일, 작성자와 글은 일대다 관계. 작성자 한 명을 기준으로 놓으면 글이 여러 개 연결된다.

  EX> 한 작성자가 여러 이메일을 등록할 수 있을경우, 작성자는 일, 이메일 주소는 다 이다. 이러한 디자인으로 데이터를 작성한다면, 한 email 칼럼에, 한 작성자의 이메일 여러 값을 저장해야 한다. 이 구조는 단일값 원칙을 위배한 테이블이다. 단순하지도 않고 데이터를 가져올 경우 더 복잡한 과정을 거쳐야한다. 주소를 여러개 넣을 것을 대비해서 email 칼럼의 최대 허용 문자수도 훨씬 길게 설정해야 한다. 따라서 이메일 엔티티를 전용 테이블로 분리하여 id값을 연결해 주면 문제를 해결할 수 있다.

* 다대다 관계: 다대다 관계를 올바르게 구현하려면 lookup 테이블을 도입해야 한다. 이 테이블은 실제 데이터가 아니라 상관 관계로 맺어진 엔트리 쌍을 저장한다, (다른 테이블과 또 다른 테이블을 연결하는 구조) 룩업 테이블은 한 칼럼의 값으로 고유성을 부여할 수 없다. 여러 글 ID가 등록되며, 한 글이 여러 카테고리에 속하므로, 각 카테고리 ID도 여러 번 등록된다. 룩업 테이블은 일반적으로 기본 키에 다중 칼럼을 지정한다.

  ```mysql
  CREATE TABLE jokecategory (
  	jokeid INT NOT NULL,
    categoryid INT NOT NULL,
    PRIMARY KEY (jokeid, categoryid)
  ) DEFAULT CHARACTER SET utf8 ENGINE=InnoDB
  # 다중 칼럼을 기본키로 설정하는 쿼리, 테이블을 생성할 때 두칼럼 모두에게 PK 설정을 하면 위와같은 쿼리 생성
  ```

  