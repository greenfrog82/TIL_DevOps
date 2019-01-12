# Transaction Isolation Level

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

위 테이블은 PostgreSQL의 `Repeatable Read`의 구현이 `Phantom read`를 허용하지 않음을 나타내고 있다. 

이제 앞서 설명했던 다음 네가지 현상에 대해서 좀 더 자세히 알아보도록하자.  

* dirty read
* nonrepeatable read
* phantom read
* serialize anomaly

## dirty read

앞서 `dirty read`에 대해서 다음과 같이 설명했었다. 

>트랜잭션이 커밋되지 않은 동시 트랜잭션에 의해 작성된 데이터를 읽는 것.  
예를들어, B라는 사용자가 데이터를 쓰고, A라는 사용자가 해당 데이터를 읽어갔는데 B가 해당 데이터를 롤백한 경우 데이터가 불일치 하게 된다.

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

`dirty read`를 확인하기 위해서는 앞서 입력한 데이터가 보여야한다. 하지만 결과는 위와같다. 이는 `PostgreSQL`의 default`Transaction Isolation Level`이 `Read uncommitted`이기 때문이다. 따라사 `PostgreSQL`에서는 `dirty read`를 확인할 수 없다.  
## Repeatable Read

앞서 `nonrepeatable read`에 대해서 다음과 같이 설명했었다. 

>트랜잭션이 같은 데이터를 두번 읽을 때, 다른 트랜잭션(초기 읽기 이후 커밋 된 트랜잭션)에 의해 데이터가 수정되었음을 알게되는 것.
예를들어, A라는 사용자가 같은 쿼리를 두번 실행한다. 그 사이에 B라는 사용자가 데이터를 수정한다. A라는 사용자의 두 쿼리 결과가 달라진다. 

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

## How to know transaction isolation level

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

# Reference

* [13.2. Transaction Isolation](https://www.postgresql.org/docs/11/transaction-iso.html)
* [Isolation (database systems)](https://en.wikipedia.org/wiki/Isolation_%28database_systems%29#Non-repeatable_reads)
* [Transaction Isolation Levels in PostgreSQL](http://shiroyasha.io/transaction-isolation-levels-in-postgresql.html)
* [Postgres Transaction Isolation Levels](https://malisper.me/postgres-transaction-isolation-levels/)
* [Transaction Isolation in PostgreSQL](https://pgdash.io/blog/postgres-transactions.html)