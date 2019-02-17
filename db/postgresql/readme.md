# Postgresql

## Setting Log

어플리케이션 레벨에서 RAW Query를 사용하지 않고 ORM을 사용해서 개발을 하고 있다면, ORM을 통해 생성되는 쿼리를 확인하고 싶은 경우가 있을것이다. 쿼리를 확인하는 목적은 다양한데 원하지 않게 쿼리가 나가는 경우라던가, 쿼리가 비효율적으로 나가는지 등을 확인할 수 있기 때문이다.   

위와같은 목적으로 쿼리를 로그에 기록하기위해서는 몇가지 설정을 해주어야한다.  
이에 대해서 알아보도록하자.  

우선, Postgresql의 설정은 데이터베이스 저장경로의 `postgresql.conf`파일에 저장되는데 여기서 로그설정을 할 수 있다.  

>**주의**
>`postgresql.conf`을 통해 로그설정을 할때 대부분의 속성이 주석처리되어있으므로 주석처리가 되어있는 설정의 경우 반드시 주석처리를 풀어주어야한다.

### log_min_duration_statement

Postgresql에서 실행되는 모든 쿼리를 로그로 남기고자한다면, 다음 설정을 해주도록한다.  
주석의 내용과 같이 `0`은 Postgresql의 모든 상태를 로그로 출력하게 되는데 이를 통해 실해이되는 모든 쿼리도 함께 로그에 남게된다. 

>log_min_duration_statement = 0  # -1 is disabled, 0 logs all statements

### log_line_prefix

로그를 출력할 때 Prefix로 더하고자 하는 정보를 지정할 수 있다. 더할 수 있는 정보는 주석으로 정리되어있으며, 다음과 같은 경우 로그가 찍힌 날짜, 프로세스 아이디, 세션 아아디, 트랜잭션 아이디를 Prefix로 더해서 로그를 찍는다. 

```python
411 log_line_prefix = '[%m][%p][%c][%x]'            # special values:
412                     #   %a = application name
413                     #   %u = user name
414                     #   %d = database name
415                     #   %r = remote host and port
416                     #   %h = remote host
417                     #   %p = process ID
418                     #   %t = timestamp without milliseconds
419                     #   %m = timestamp with milliseconds
420                     #   %i = command tag
421                     #   %e = SQL state
422                     #   %c = session ID
423                     #   %l = session line number
424                     #   %s = session start timestamp
425                     #   %v = virtual transaction ID
426                     #   %x = transaction ID (0 if none)
427                     #   %q = stop here in non-session
428                     #        processes
429                     #   %% = '%'
430                     # e.g. '<%u%%%d> '
```

다음은 위 설정을 통해 출력된 로그이다. 

```sh
[2019-02-10 21:43:56.875 KST][98409][5c601c4b.18069][0]LOG:  duration: 302.489 ms  statement: SELECT "auth_user"."id", "auth_user"."username", "auth_user"."first_name", "auth_user"."last_name", "auth_user"."email", "auth_user"."password", "auth_user"."is_staff", "auth_user"."is_active", "auth_user"."is_superuser", "auth_user"."last_login", "auth_user"."date_joined" FROM "auth_user"
```

## pg_locks view

어떤 락이 할당되어있는지, 어떤 프로세스가 락을 얻기위해 대기하가고 있는지 확인하기 위해서는 `pg_locks`뷰를 확인하도록하자.   
`pg_locks`뷰를 확인하기 위해서는 다음 쿼리를 사용하자. 

```sql
SELECT relation::regclass, * FROM pg_locks WHERE NOT GRANTED;
```

## [SHOW](https://www.postgresql.org/docs/9.1/sql-show.html)

`SHOW`명령은 런타임의 현재 설명을 화면에 출력한다.  이러한 설정값은 `SET`명령, `postgresql.conf` 설정파일 그리고 `PostgreSQL`서버를 실행시킬 때 command line 옵션을 통해 설정할 수 있다. 

다음은 `SHOW`명령을 통해 확인 할 수 있는 유용한 정보들이다. 

### SHOW config_file

설정파일의 경로를 보여준다. 

```sql
postgres=# SHOW config_file;
               config_file
------------------------------------------
 /var/lib/postgresql/data/postgresql.conf
(1 row)
```

# Reference

* [Lock Monitoring](https://wiki.postgresql.org/wiki/Lock_Monitoring)