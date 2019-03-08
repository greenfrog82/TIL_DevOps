# Two-phase locking vs MVCC and Vaccum

트랜잭션은 `ACID(Atomicity, Consistency, Isolation, Durability)`라는 속성을 가지고 있다. 데이터베이스는 여러 트랜잭션이 동시에 동작할 때 `ACID`를 보장하기 위해서 `Two-phase locking` 또는 `MVCC`를 사용한다.  

## Two phase locking

`Two phase locking`은 데이터를 읽고 쓸때, lock을 사용하는 것이다. 데이터를 읽을 때는 `Shared Lock`을 사용하고 데이터를 쓸 때는 `Exclusive Lock`을 사용한다. 이들 lock이 여러 트랜잭션이 동일한 데이터를 동시에 읽고 쓰려고 할 때 동작은 다음과 같다.  

### 먼저 읽고 있을 때 쓰려고한다면?

1. tx-1이 2번 row를 읽고 있다. (Shared Lock)
2. tx-2가 2번 row를 읽고 있다. (Shared Lock)
3. tx-3가 2번 row를 갱신하려고 한다. 하지만 tx-1과 tx-2는 `Shared Lock`을 풀지 않다. 이 경우 tx-3는 `Shared Lock`이 풀릴때 까지 대기한다. 

### 먼저 쓰고 있을 때 읽으려한다면?

1. tx-1이 2번 row를 갱신하고 있다. (Exclusive Lock)
2. tx-2가 2번 row를 읽으려고 한다. 하지만 tx-1이 `Exclusive Lock`을 풀지 않았다. 이 경우 tx-2는 `Exclusive Lock`이 풀릴때까지 대기한다. 

### 먼저 쓰고 있을 때 쓰려한다면?

1. tx-1이 2번 row를 갱신하고 있다. (Exclusive Lock)
2. tx-2가 2번 row를 갱신하려고 한다. 하지만 tx-1이 `Exclusive Lock`을 풀지 않았다. 이 경우 tx-2는 `Exclusive Lock`이 풀릴때까지 대기한다. 

앞서 살펴본바와 같이 `Shared Lock`과 `Exclusive Lock`은 상호 배타적이고, `Exclusive Lock`끼리도 마찬가지다. 때문에 **여러 트랜잭션이 동시에 특정 데이터에 접근할 때 쓰기작업이 존재한다면 race-condition이 발생하게 된다. 이는 동시성 성능에 악영향을 미치게되는데, 이를 해결하기 위해 등장한 방법이 `MVCC`이다.**

## MVCC (Multi-Version Concurrency Control)

`MVCC`는 `Multi-Version Concurrency Control`는 트랜잭션이 데이터를 읽을 때 스냅샷을 사용한다. 그리고 데이터를 업데이트 할 때 새로운 버전의 데이터를 insert하기 때문에 `race-condition`이 발생하지 않는다. 단, `Exclusive Lock`에 대해서는 여전히 `Two phase locking`과 마찬가지고 상호배타적이기 때문에 `race-condition`이 발생한다. 

`MVCC`를 사용함에도 불구하고 트랜잭션이 `Read-Committed Isolation Level`을 사용하고 있을 때 다른 트랜잭션이 commit한 데이터를  읽어오는 이유는 `Read-Committed Isolation Level`을 사용하는 경우는 읽기를 할 때 매번 새로운 스냅샷을 뜨기 때문이다. 반면에 `Repeatable Read Isolation Level`을 사용할 때는 트랜잭션이 시작한 후 읽은 데이터를 유지하는 것은 처음에 뜬 스냅샷을 재사용하기 때문이다.

`MVCC`은 읽기와 쓰기에 대한 `race-condition`이슈를 없애주기 때문에 동시성 성능을 향상시켜주지만, 물리적 공간에 대한 이슈를 만들게 된다.   
앞서 설명한 바와 같이 `MVCC`는 데이터를 업데이트 할 때, 해당 데이터를 직접 업데이트하지 않고 새로운 버전에 데이터를 추가한다. 그리고 업데이트 전의 데이터는 old version으로 마킹을 하고 해당 데이터를 참조하는 트랜잭션이 모두 종료되면 `dead row`로 마킹을 해서 더 이상 읽지 않도록 한다. 결국 시간이 지나면 이 `dead row`의 데이터가 쌓여서 디시크 공간을 차지하게 된다. 

이러한 문제를 해결하기 위한 방법이 `VACCUM`이다.

## VACCUM

# Reference

* [MVCC and VACUUM](http://rhaas.blogspot.com/2017/12/mvcc-and-vacuum.html?m=1)
* [Databases 101: ACID, MVCC vs Locks, Transaction Isolation Levels, and Concurrency](http://ithare.com/databases-101-acid-mvcc-vs-locks-transaction-isolation-levels-and-concurrency/#rabbitref-Postgre)
* [Postgresql - MVCC Introduction](https://www.postgresql.org/docs/current/mvcc-intro.html)