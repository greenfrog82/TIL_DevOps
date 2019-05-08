# Join

## Can each tables which does not have relation join?

서로 Relation 설정이 없는 테이블끼리 조인이 가능한지 확인해봤다. 가능하다.

```sql
postgres=# create table person ( id integer, name varchar(100) );
CREATE TABLE
postgres=# create table job (id integer, person_id, name varchar(100));
ERROR:  syntax error at or near ","
LINE 1: create table job (id integer, person_id, name varchar(100));
                                               ^
postgres=# create table job (id integer, person_id integer, name varchar(100));
CREATE TABLE
postgres=# insert into person values (1, 'a'), (2, 'b')
postgres-# ;
INSERT 0 2
postgres=# insert into job values (1, 1, 'aa'), (2, 2, 'bb');
INSERT 0 2
postgres=# select * from person join job on person.id == job.person_id;
ERROR:  operator does not exist: integer == integer
LINE 1: select * from person join job on person.id == job.person_id;
                                                   ^
HINT:  No operator matches the given name and argument types. You might need to add explicit type casts.
postgres=# select * from person join job on person.id = job.person_id;
 id | name | id | person_id | name 
----+------+----+-----------+------
  1 | a    |  1 |         1 | aa
  2 | b    |  2 |         2 | bb
(2 rows)
```
