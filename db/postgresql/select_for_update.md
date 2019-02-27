# for update 

`select ... for update`쿼리는 쿼리하는 데이터(들)에 `lock`을 걸어준다. 따라서, 이미 다른 트랜잭션에서 `update`쿼리가 실행 중이라면 해당 쿼리는 대기하게 된다. 그리고 `update`쿼리가 완료되면, 변경된 데이터를 일게 된다. 


## Test Case 1. On the default transaction isolation level

**default transaction isolation(read committed)** 레벨을 사용하는 트랜잭션 `tx-1`과 `tx-2`를 열어두고 다음과 같은 경우를 테스트한다. 

1. **tx-1** : select ... for update를 통해 특정 사용자를 읽는다.
2. **tx-2** : select 쿼리를 통해 tx-1과 같은 데이터를 읽고, update 쿼리를 통해 username을 변경해보자. 동작하는가?

### tx-1, select ... for update

`tx-1`에서 `select ... for update`를 통해 id 149997에 대한 데이터를 쿼리하자. 이때 해당 데이터에는 `lock`이 걸릴것이다. 

```sql
greenfrog=# begin;
BEGIN
greenfrog=# show transaction_isolation;
 transaction_isolation
-----------------------
 read committed
(1 row)

greenfrog=# select id, username from auth_user where id = 149997 for update
greenfrog-# ;
   id   | username
--------+----------
 149997 | tx-2-2
(1 row)
```

### tx-2, select and update

이제 `tx-2`에서 `select`쿼리를 통해 해당 `tx-1`과 같은 데이터를 읽어보자. 아래 결과와 같이 정상적으로 데이터가 읽히는것을 알 수 있다.  

```sql
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-2-2
(1 row)

```

이번에는 업데이트를 시도해보자. 예상을 해보면 앞서 `select ... for update`를 통해 해당 데이터에 `lock`을 걸어두었기 때문에 업데이트가 안되고 대기하고 있을 것이다. 예상대로 동작할까?   
예상대로 업데이트를 수행하지 못하고 대기하고 있는것을 알 수 있다.

```sql
greenfrog=# update auth_user set username = 'tx-2-3' where id = 149997;


```

### tx-1, commit 

이제 `tx-1`에서 `commit`을 수행해보자. 아마도 `commit`의 실행이 완료되는 순간 `tx-2`의 `update`쿼리가 완료될 것이다.  

```sql
greenfrog=# commit;
COMMIT
```

### tx-2, UPDATE 1

앞서 `tx-1`에서 `commit`을 수행하는 순간, 다음과 같이 `tx-2`의 `update`쿼리가 완료되었음을 확인할 수 있다. 

```sql
UPDATE 1
```


## Test Case 2. On the repeatable read transaction isolation level

앞선 시나리오를 `repeatable read`가 적용된 두 트랜잭션에서 똑같이 실험해보면 결과는 완전히 동일하다. 


## Test Case 3. Check when update query is running on tx-1, select ... for update query run on tx-2

`default transaction isolation level(read committed)`에서 `tx-1`에서 `update query`를 실행하고 있는 동안 `tx-2`에서 `select ... for update`쿼리를 수행해보자. 어떻게 동작할까?   

추측해보면 다음과 같이 흘러갈것이다. 

1. `tx-1`에서 `update`쿼리 실행.
2. `tx-1`에서 `udpate`를 실행 중이므로 `tx-2`에서 실행한 `select ... for update`쿼리는 블락.
3. `tx-1`에서 `commit`을 실행하면, `tx-2`는 `tx-1`에서 변경한 값이 반영된 데이터를 반환.

### tx-1, perform update query

```sql
greenfrog=# begin;
BEGIN
greenfrog=# show transaction_isolation;
 transaction_isolation
-----------------------
 read committed
(1 row)

greenfrog=# select id, username from auth_user;
greenfrog=# select id, username from auth_user where id = 149997;
   id   | username
--------+----------
 149997 | tx-2-4
(1 row)

greenfrog=# update auth_user set username = 'tx-1' where id = 149997;
UPDATE 1
```

### tx-2, perform select ... for update query

예상대로, `tx-1`에서 `update`쿼리가 실행 중이므로 락이 걸려서 쿼리가 블락된다. 

```sql
begin ;
BEGIN
greenfrog=# show transaction_isolation;
 transaction_isolation
-----------------------
 read committed
(1 row)

greenfrog=# select id, username from auth_user where id = 149997 for update;


```

### tx-1, perform commit 

```sql
greenfrog=# commit;
COMMIT
```

### tx-2, print updated data by tx-1

역시 예상대로, `tx-1`이 `commit`을 수행하자, `tx-2`에서는 블락되었던 쿼리가 실행되면서 `tx-1`에 의해 변경된 데이터가 반환된다. 

```sql
   id   | username
--------+----------
 149997 | tx-1
(1 row)
```
