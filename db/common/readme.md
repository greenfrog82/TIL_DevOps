# Transaction Isolation Level

## What is the transaction?

`Transaction`은 데이터베이스의 데이터를 읽고 쓰고 수정하기위한 하나의 작업단위로 더 이상 쪼갤 수 없는 가장 작은 작업단위를 말한다. `Transaction`의 전후로 데이터베이스의 일관성을 유지하기위한 네가지 속성이 있는데 이를 `ACID`라고한다.  

### **A**tomicity

`Transaction`의 모든 로직은 모두 수행되거나 그렇지 않아야한다. 때문에 `Atomicity`를 `All or nothing rule`이라고도 한다.  
여기서는 다음 **두 가지 Operation**이 수반된다.  

* **Abort** : `Transaction`이 중단되면, 모든 로직의 수행은 취소된다. 
* **Commit** : `Transaction`이 commit되면, `Transaction`로직이 반영된다. 

### **C**onsistency



## What is Transaction Isolation level?

`Transaction`은 `RDBMS`에서 데이터를 변경하기 위한 근본적인 방법이다. `RDBMS`에서는 하나 이상의 `Transaction`이 동시에 동작하는것을 허용한다. 그리고 개발자가 각각의 `Transaction`이 서로 어떻게 상호작용할것인가를 명시하기 위한 표준 또는 특정 RDBMS에서 정의한 툴을 제공한다. 

SQL표준은 4개의 Transaction Isolation Level을 정의한다. 가장 엄격한 것은 `Serializable`이며,  `Serializable` 트랜잭션들의 동시 실행은 한 번에 하나씩 순서대로 실행하는 것과 동일하게 동작한다. 다른 세가지 레벨에서는 각 레벨에서 발생하면 안되는 동시 트랜잭션 사이의 상호작용으로 인한 현상에 의해 정의된다.  

다음은 각 레벨에서 금지된 현상들이다. 

* dirty read  
    트랜잭션이 커밋되지 않은 동시 트랜잭션에 의해 작성된 데이터를 읽는 것.  
    예를들어, B라는 사용자가 데이터를 쓰고, A라는 사용자가 해당 데이터를 읽어갔는데 B가 해당 데이터를 롤백한 경우 데이터가 불일치 하게 된다. 
* nonrepeatable read  
    트랜잭션이 같은 데이터를 두번 읽을 때, 다른 트랜잭션(초기 읽기 이후 커밋 된 트랜잭션)에 의해 데이터가 수정되었음을 알게되는 것.
    예를들어, A라는 사용자가 같은 쿼리를 두번 실행한다. 그 사이에 B라는 사용자가 데이터를 수정한다. A라는 사용자의 두 쿼리 결과가 달라진다. 
* phantom read
    트랜잭션은 검색 조건을 만족하는 행 집합을 반환하는 쿼리를 다시 실행하고 조건을 만족하는 행 집합이 최근에 커밋 된 다른 트랜잭션으로 인해 변경되었음을 알게되는 것.
    예를들어, A라는 사용자가 같은 쿼리를 두번 실행한다. 그 사이에 B라는 사용자가 데이터를 삭제하거나 추가한다. A라는 사용자의 두 쿼리 결과가 달라진다. 
* serialization anomaly
    성공적으로 커밋 된 트랜잭션 그룹은 각각 트랜잭션을 하나씩 실행할 수 있는 순서와 일치하지 않는다. 

SQL표준과 PostgreSQL은 다음과 같이 `Transaction Isolation Level`을 구현하고 있다.  

|Isolation Level|Dirty Read|Nonrepeatable Read|Phantom Read|Serialization Anomaly|
|---------------|----------|----------|----------|----------|
|Read uncommitted|Allowed, but not in PG|Possible|Possible|Possible|
|Read committed|Not possible|Possible|Possible|Possible|
|Repeatable read|Not possible|Not possible|Allowed, but not in PG|Possible|
|Serializable|Not possible|Not possible|Not possible|Not possible|

PostgreSQL에서 위 네개의 `Transaction Isolation Level`중 어떤것이든 사용할 수 있다. 하지만 내부적으로는 세가지만 구현이되어있는데, 예를들어, `Read Uncommitted`모드는 `Read Committed`와 동일하다. 

위 테이블은 PostgreSQL의 `Repeatable Read`의 구현이 `Phantom read`를 허용하지 않음을 나타내고 있다. 반면, MySQL의 InnoDB의 경우 SQL표준에 따른 `Transaction Isolation Level`을 구현하고 있다. (본 문서에서 MySQL에 대한 설명은 InnoDB에 국한한다.)

이제 앞서 설명했던 네가지 `Transaction Isolation Level`에대해서 `PostgreSQL`과 `MySQL`의 예제를 통해 좀 더 자세히 알아보도록하자.  

* Read Uncommitted (dirty read)
* Read Committed (nonrepeatable read)
* Repeatable Read (phantom read)
* Serialzable (serialize anomaly)

## Read Uncommitted (dirty read)

앞서 `Read Uncommitted (dirty read)`에 대해서 다음과 같이 설명했었다. 

>트랜잭션이 커밋되지 않은 동시 트랜잭션에 의해 작성된 데이터를 읽는 것.  
예를들어, B라는 사용자가 데이터를 쓰고, A라는 사용자가 해당 데이터를 읽어갔는데 B가 해당 데이터를 롤백한 경우 데이터가 불일치 하게 된다.

### Case Of PostgreSQL

위 설명을 재현해보자. 이를 재현하기 위해서는 별도의 세션을 열어야한다. 따라서 별도의 command화면에서 각각 `psql`을 통해서 DB에 연결을 맺도록 하자.  

우선, 다음과 같이 테스트를 위한 테이블을 생성한 후 `begin`명령을 통해 트랜잭션을 열자. 그리고 데이터를 하나 입력한 후 `commit`은 하지 않았다. 

```sql
$ psql -h localhost -p 5432 -U postgres
postgres=# create table  t (a int, b int);
CREATE TABLE
postgres=# begin ;
BEGIN
postgres=# insert into t values (1, 100);
INSERT 0 1
```

다른 세션에서 테스트를 위해 생성해둔 테이블을 읽어보자. 

```sql
$ psql -h localhost -p 5432 -U postgres
postgres=# select * from t;
 a | b
---+---
(0 rows)
```

`dirty read`를 확인하기 위해서는 앞서 입력한 데이터가 보여야한다. 하지만 결과는 위와같다. 이는 `PostgreSQL`의 default`Transaction Isolation Level`이 `Read Committed`이기 때문이다. 따라서 `PostgreSQL`에서는 `dirty read`를 확인할 수 없다.  

### Case Of MySQL

우선 테스트를 진행하기 전에, 테스트를 진행하기 위한 준비부터하자.   

1. 테스트를 위한 데이터베이스 생성.
2. 테스트를 위한 테이블 생성.
3. 테스트를 위한 데이터 입력. 

```sql
mysql> use sdy;
Database changed
mysql> CREATE TABLE user (
    ->     id INTEGER PRIMARY KEY AUTO_INCREMENT,
    ->     name VARCHAR(20)
    -> );
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO user (name) VALUES ("jupiny");
Query OK, 1 row affected (0.07 sec)

mysql> INSERT INTO user (name) VALUES ("jupiny2");
Query OK, 1 row affected (0.08 sec)

mysql> INSERT INTO user (name) VALUES ("jupiny3");
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM user;
+----+---------+
| id | name    |
+----+---------+
|  1 | jupiny  |
|  2 | jupiny2 |
|  3 | jupiny3 |
+----+---------+
3 rows in set (0.00 sec)
```

이제 첫번째 Session-1에 `Transaction Isolation Level`을 `Read Uncommitted`를 설정한 후 잘 설정되었는지 확인하자.  

```sql
mysql> SET SESSION transaction isolation level READ UNCOMMITTED;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@SESSION.transaction_isolation, @@SESSION.transaction_read_only;
+---------------------------------+---------------------------------+
| @@SESSION.transaction_isolation | @@SESSION.transaction_read_only |
+---------------------------------+---------------------------------+
| READ-UNCOMMITTED                |                               0 |
+---------------------------------+---------------------------------+
1 row in set (0.00 sec)
```

이제 트랜잭션을 열고, `user`테이블의 데이터를 읽어보자. 앞서 넣어뒀던 데이터가 그대로 읽힌것을 확인 할 수 있다. `Session-1`에 열어놓은 트랜잭션을 `Transaction-1`이라고 하자.

```sql
mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM user;
+----+---------+
| id | name    |
+----+---------+
|  1 | jupiny  |
|  2 | jupiny2 |
|  3 | jupiny3 |
+----+---------+
3 rows in set (0.00 sec)
```

이제 `Session-2`에서 `Transaction-2`을 열어서 데이터를 변경하고 추가해보자.  
이때 중요한것은 해당 트랜잭션을 절대 커밋해서는 안된다. 

```sql
mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)

mysql> update user set name = "greenfrog";
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> insert into user(name) values("mysql");
Query OK, 1 row affected (0.00 sec)

mysql> select * from user;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | greenfrog |
|  2 | greenfrog |
|  3 | greenfrog |
|  4 | mysql     |
+----+-----------+
4 rows in set (0.00 sec)
```

`Transaction-1`에서 다시 user 테이블을 읽어보자. `Transaction-2`에서 변경한 데이터들이 반영되어 나타나는것을 확인 할 수 있다.  

```sql
mysql> select * from user;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | greenfrog |
|  2 | greenfrog |
|  3 | greenfrog |
|  4 | mysql     |
+----+-----------+
4 rows in set (0.00 sec)
```

위와 같이 MySQL의 InnoDB의 `Read Uncommitted`에서는, `Transaction-2`에서 아직 커밋을하지 않았음에도 불구하고, `Transaction-1`에서는 그 데이터들을 읽어옴을 볼 수 있다. 
READ UNCOMMITTED level에서는 아래 세 가지 현상이 모두 발생함을 알 수 있다.

* 아직 COMMIT 되지 않은 신뢰할 수 없는 데이터를 읽어옴(dirty read)
* 한 트랜잭션에서 동일한 SELECT 쿼리의 결과가 다름(non-repeatable read)
* 이전의 SELECT 쿼리의 결과에 없던 row가 생김(phantom read) 

## Repeatable Read

앞서 `nonrepeatable read`에 대해서 다음과 같이 설명했었다. 

>트랜잭션이 같은 데이터를 두번 읽을 때, 다른 트랜잭션(초기 읽기 이후 커밋 된 트랜잭션)에 의해 데이터가 수정되었음을 알게되는 것.
예를들어, A라는 사용자가 같은 쿼리를 두번 실행한다. 그 사이에 B라는 사용자가 데이터를 수정한다. A라는 사용자의 두 쿼리 결과가 달라진다. 

### In Case Of PostgreSQL

위 문제를 재현해보자. 먼저 첫번째 세션에서 트랜잭션을 연 후 SELECT 쿼리를 통해 테이블의 데이터를 확인해보자. 앞서 `dirty read`때 입력 했던 데이터가 보인다. 

```sql
$ docker exec -it 71 psql -U postgres
psql (9.6.11)
Type "help" for help.

postgres=# begin;
BEGIN
postgres=# select * from t;
 a |  b
---+-----
 1 | 100
(1 row)
```

이제 두번째 세션에서 트랜잭션을 연 후 동일하게 SELECT를 쿼리를 통해 테이블의 데이터를 확인해보자. 첫번째 세션과 동일한 결과가 출력된다. 

```sql
docker exec -it 71 psql -U postgres
psql (9.6.11)
Type "help" for help.

postgres=# begin;
BEGIN
postgres=# select * from t;
 a |  b
---+-----
 1 | 100
(1 row)
```

역시 두번째 세션에서 해당 테이블의 데이터를 100에서 200으로 변경한 후 `COMMIT`을 수행한다.

```sql
postgres=# update t set b = 200 where a = 1;
UPDATE 1
postgres=# commit;
COMMIT
postgres=#
```

첫번째 세션에서 테이블의 데이터를 읽으면, 두번째 세션에서 변경한 데이터를 확인 할 수 있다. 

```sql
postgres=# select * from t;
 a |  b
---+-----
 1 | 200
(1 row)

postgres=#
```

T1의 `Transaction Isolation Level`을 `Repeatable Read`로 바꾼 후 account 테이블의 데이터를 읽어보자. 

```sql
postgres=# BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN
postgres=#  SHOW transaction_isolation;
 transaction_isolation
-----------------------
 repeatable read
(1 row)

postgres=# select * from account;
 id | name
----+------
  2 | a
  3 | b
  4 | c
  5 | d
(4 rows)
```

T2에서 다음과 같이 데이터를 입력하자. 

```sql
postgres=# begin;
postgres=# insert into account(name) values('d');
INSERT 0 1
postgres=# commit;
```

이제 다시 T1에서 데이터를 다시 읽어보자. T1은 아직 트랜잭션이 열려있는 상태이다. `Transaction Isolation Level`이 `Repeatable Read`이기 때문에 원래 읽었던 데이터만 읽어지는것을 확인 할 수 있다. 

```sql
postgres=# select * from account;
 id | name
----+------
  2 | a
  3 | b
  4 | c
  5 | d
(4 rows)
```

마지막으로 T1에서 `COMMIT`을 한 후 다시 데이터를 읽어보면, T2에서 입력을 데이터가 보이는것을 확인할 수 있다.

```sql
postgres=# commit;
COMMIT

postgres=# select * from account;
 id | name
----+------
  2 | a
  3 | b
  4 | c
  5 | d
  6 | e
(5 rows)

postgres=#
```

#### Update same data when two transaction are set repeatable read

트랜잭션 2개를 모두 `repeatable read`로 설정한 후 같은 데이터를 업데이트 해보자. 어떻게 동작할까?

먼저, 아래와 같이 `tx-1`트랜잭션을 열고 id 149997에 해당하는 사용자의 이름을 `tx-1-1`으로 업데이트해보자.  
아래와 같이 정상적으로 업데이트가 된것을 알 수 있다.

**[tx-1]**

```sql
greenfrog=# begin transaction isolation level repeatable read;
BEGIN
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-1
(1 row)

greenfrog=# update auth_user set username = 'tx-1-1' where id = 149997;
UPDATE 1
```

이번에는 `tx-2`에서 동일한 사용자의 이름을 `tx-2`으로 업데이트해보자. `tx-1`과 달리 `UPDATED 1`이 출력되지 않고 **대기하고있음**을 알 수 있다. 
확인이 필요하겠지만, `tx-1`에서 동일한 사용자의 이름을 변경하면서 `wrtie lock`이 해당 row에 걸렸을 것이고 아직 해지가 되지 않았기 때문일 것이다. 

**[tx-2]**

```sql
greenfrog=# begin transaction isolation level repeatable read;
BEGIN
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-1
(1 row)

greenfrog=# update auth_user set username = 'tx-2' where id = 149997;


```

이제 `tx-1`에서 `commit`을 수행해보자. 그럼 `tx-1`에서 변경한 데이터가 테이블에 반영이되고 `write lock`도 풀리것이다.  

**[tx-1]**

```sql
greenfrog=# commit
COMMIT
```

다시 `tx-2`을 확인해보면, 다음과 같이 `ERROR:  could not serialize access due to concurrent update`에러가 발생한 것을 알 수 있다. 조금 의아한것은 이 에러는 `serializable isolation level`이 적용되어 있을 떄 발생하는 에러라는 것이다. 결국, `tx-2`는 자신의 변경사항을 테이블에 반영하지 못했다. 

```sql
ERROR:  could not serialize access due to concurrent update
greenfrog=# commit;
ROLLBACK
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-1-1
(1 row)
greenfrog=#
```

#### Update same data when tx-1 is set repeatable read and tx-2 is set default isolation level (read commited)

트랜잭션 하나는 `repeatable read isolation level`을 사용하고, 다른 트랜잭션 하나는 `default isolation level(read committed)`를 사용하여 동일한 데이터를 업데이트해보자. 어떻게 동작할까?

먼저, 아래와 같이 `tx-1`트랜잭션을 열고 id 149997에 해당하는 사용자의 이름을 `tx-1-1-1`으로 업데이트해보자.  
아래와 같이 정상적으로 업데이트가 된것을 알 수 있다.

**[tx-1]**

```sql
greenfrog=# begin transaction isolation level repeatable read;
BEGIN
greenfrog=# select id, username from auth_user;
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-1-1
(1 row)

greenfrog=# update auth_user set username = 'tx-1-1-1' where id = '149997';
UPDATE 1 
```

이번에는 `tx-2`에서 동일한 사용자의 이름을 `tx-2`으로 업데이트해보자. `tx-1`과 달리 `UPDATED 1`이 출력되지 않고 **대기하고있음**을 알 수 있다. 
확인이 필요하겠지만, `tx-1`에서 동일한 사용자의 이름을 변경하면서 `wrtie lock`이 해당 row에 걸렸을 것이고 아직 해지가 되지 않았기 때문일 것이다. 

**[tx-2]**

```sql
greenfrog=# begin transaction isolation level repeatable read;
BEGIN
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-1-1
(1 row)

greenfrog=# update auth_user set username = 'tx-2' where id = 149997;


```

이제 `tx-1`에서 `commit`을 수행해보자. 그럼 `tx-1`에서 변경한 데이터가 테이블에 반영이되고 `write lock`도 풀리것이다.  

**[tx-1]**

```sql
greenfrog=# commit
COMMIT
```

다시 `tx-2`을 확인해보면, `tx-1`이 `commit`되자마자 `UPDATE 1`이 출력된것을 확인 할 수 있다. `select`쿼리를 통해 결과를 확인해보면 `tx-2`의 값이 테이블에 반영된것을 알 수 있다.  
**이와 같은 결과가 나온 이유는 `tx-1`이 데이터를 업데이트했지만 뒤에 `tx-2`의 변경사항으로 다시 업데이트되었기 때문이다.** 이 결과는 두 트랜잭션이 모두 `default isolation level(read committed)`로 설정되어 있을때도 마찬가지이다.

**[tx-2]**

```sql
UPDATE 1
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-2
(1 row)
```

## How to know transaction isolation level

### Case Of PostgreSQL

Postgresql의 command에서 다음 명령을 통해 현재 설정 된 Transaction Isolation Level을 확인할 수 있다. 

```sql
psql> SHOW transaction_isolation;   
read commited
```

PostgreSQL의 기본 Transaction Isolation Level은 다음 명령을 통해 확인할 수 있다. 

```sql
psql> SHOW default_transaction_isolation;
read commited
```

사실 위 명령은 `SHOW ALL`명령을 통해 확인할 수 있는 속성 중 하나이다. 

```sql
SHOW ALL;  
transaction_deferrable | off            | Whether to defer a read-only serializable transaction until it can be executed with no possible serialization failures.  
transaction_isolation  | read committed | Sets the current transaction's isolation level.  
transaction_read_only  | off            | Sets the current transaction's read-only status.  
```

### Case Of MySQL

MySQL의 경우 `Global Transaction Isolation Level`과 `Session Transaction Isolation Level`이 각각 존재한다. 

#### Global Transaction Isolation Level

```sql
mysql> SELECT @@GLOBAL.transaction_isolation, @@GLOBAL.transaction_read_only;
+--------------------------------+--------------------------------+
| @@GLOBAL.transaction_isolation | @@GLOBAL.transaction_read_only |
+--------------------------------+--------------------------------+
| REPEATABLE-READ                |                              0 |
+--------------------------------+--------------------------------+
1 row in set (0.00 sec)
```

#### Session Transaction Isolation Level

```sql
mysql> SELECT @@SESSION.transaction_isolation, @@SESSION.transaction_read_only;
+---------------------------------+---------------------------------+
| @@SESSION.transaction_isolation | @@SESSION.transaction_read_only |
+---------------------------------+---------------------------------+
| REPEATABLE-READ                 |                               0 |
+---------------------------------+---------------------------------+
1 row in set (0.00 sec)
```

# Reference

* [13.2. Transaction Isolation](https://www.postgresql.org/docs/11/transaction-iso.html)
* [Isolation (database systems)](https://en.wikipedia.org/wiki/Isolation_%28database_systems%29#Non-repeatable_reads)
* [Transaction Isolation Levels in PostgreSQL](http://shiroyasha.io/transaction-isolation-levels-in-postgresql.html)
* [Postgres Transaction Isolation Levels](https://malisper.me/postgres-transaction-isolation-levels/)
* [Transaction Isolation in PostgreSQL](https://pgdash.io/blog/postgres-transactions.html)
* [MySQL의 Transaction Isolation Levels](https://jupiny.com/2018/11/30/mysql-transaction-isolation-levels/)
* [13.3.7 SET TRANSACTION Syntax](https://dev.mysql.com/doc/refman/8.0/en/set-transaction.html)
* [ACID Properties in DBMS](https://www.geeksforgeeks.org/acid-properties-in-dbms/)