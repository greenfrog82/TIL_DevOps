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

PostgreSQL에서 위 네개의 `Transaction Isolation Level`중 어떤것이든 사용할 수 있다. 하지만 내부적으로는 세가지만 구현이되어있는데, 예를들어, `Read Uncommitted`모드은 `Read Committed`와 동일하다. 

위 테이블은 PostgreSQL의 `Repeatable Read`의 구현이 `Phantom read`를 허용하지 않음을 나타내고 있다. 


## How to know transaction isolation level

Postgresql의 command에서 다음 명령을 통해 현재 설정 된 Transaction Isolation Level을 확인할 수 있다. 

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
