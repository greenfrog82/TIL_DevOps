# Psql Command(s)

`Psql`은 `Postgres`서버에 쿼리를 날리거나 특정 명령을 실행하여 결과를 확인하기 위한 `Interfactive Terminal`이다.   

`Psql`을 실행하기 위한 파라메터들은 다음과 같다. 

* **-h** : `Postgres`서버가 실행 중이 Host Name 또는 IP Address.
* **-U** : `Postgres`서버의 사용자 이름.
* **-p** : `Postgres`서버의 포트(default 5432).

위 파라메터들을 이용해서 다음과 같은 형식으로 `Postgres`서버에 접속 할 수 있다. 

>$ psql -h localhost -U username database-name

앞서 설명한 방법 이외에 다른 파라메터들도 사용할 수 있는데, 이 경우에는 파라메터의 Full Name을 사용하고 이들을 하나의 문자열로 `Psql`에 전달해야한다. 

>$ psql "dbname=dbhere host=hosthere user=userhere password=pwhere port=5432 sslmode=require"

위와같이 `Psql`에 접속하면 쿼리를 실행하거나 특정 명령들을 실행할 수 있다. 본 문서에서는 언급한 특정 명령들에 대해서 알아볼 것인데 이러한 명령들은 `Psql`의 쉘에서 `\?`을 통해 확인할 수 있다. 

```sql
postgres=# help
You are using psql, the command-line interface to PostgreSQL.
Type:  \copyright for distribution terms
       \h for help with SQL commands
       \? for help with psql commands
       \g or terminate with semicolon to execute query
       \q to quit
```

다음과 같이 `\?`명령을 사용하면 쉘에서 사용할 수 있는 명령들을 카테고리 별로 보여준다. 

```sql
postgres=# \?
General
  \copyright             show PostgreSQL usage and distribution terms
  \crosstabview [COLUMNS] execute query and display results in crosstab
  \errverbose            show most recent error message at maximum verbosity
  \g [FILE] or ;         execute query (and send results to file or |pipe)
  \gdesc                 describe result of query, without executing it
  \gexec                 execute query, then execute each value in its result
  \gset [PREFIX]         execute query and store results in psql variables
  \gx [FILE]             as \g, but forces expanded output mode
  \q                     quit psql
  \watch [SEC]           execute query every SEC seconds

...

Large Objects
  \lo_export LOBOID FILE
  \lo_import FILE [COMMENT]
  \lo_list
  \lo_unlink LOBOID      large object operations
```

## Informational

`Postgres`서버의 데이터베이스, 테이블 목록등의 정보를 제공한다.  
해당 명령에는 다음과 같은 추가 옵션들이 존재한다.

* **S** : show system objects
* **+** : additional detail

### \d[S+] - list tables, views, and sequences

```sql
postgres=# \d
                           List of relations
 Schema |                  Name                   |   Type   |  Owner
--------+-----------------------------------------+----------+----------
 public | app_model_article                       | table    | postgres
 public | app_model_article_id_seq                | sequence | postgres
 public | app_model_category                      | table    | postgres
 public | app_model_category_id_seq               | sequence | postgres
 public | app_model_category_subcategories        | table    | postgres
 ...
 public | django_migrations                       | table    | postgres
 public | django_migrations_id_seq                | sequence | postgres
 public | django_session                          | table    | postgres
 (33 rows)
```

### \d[S+]  NAME - describe table, view, sequence, or index

```sql
postgres=# \d auth_user
                                        Table "public.auth_user"
    Column    |           Type           | Collation | Nullable |                Default
--------------+--------------------------+-----------+----------+---------------------------------------
 id           | integer                  |           | not null | nextval('auth_user_id_seq'::regclass)
 password     | character varying(128)   |           | not null |
 last_login   | timestamp with time zone |           |          |
 is_superuser | boolean                  |           | not null |
 username     | character varying(150)   |           | not null |
 first_name   | character varying(30)    |           | not null |
 last_name    | character varying(150)   |           | not null |
 email        | character varying(254)   |           | not null |
 is_staff     | boolean                  |           | not null |
 is_active    | boolean                  |           | not null |
 date_joined  | timestamp with time zone |           | not null |
Indexes:
    "auth_user_pkey" PRIMARY KEY, btree (id)
    "auth_user_username_key" UNIQUE CONSTRAINT, btree (username)
    "auth_user_username_6821ab7c_like" btree (username varchar_pattern_ops)
Referenced by:
    TABLE "app_model_userprofile" CONSTRAINT "app_model_userprofile_user_id_234cee38_fk_auth_user_id" FOREIGN KEY (user_id) REFERENCES auth_user(id) DEFERRABLE INITIALLY DEFERRED
    TABLE "auth_user_groups" CONSTRAINT "auth_user_groups_user_id_6a12ed8b_fk_auth_user_id" FOREIGN KEY (user_id) REFERENCES auth_user(id) DEFERRABLE INITIALLY DEFERRED
    TABLE "auth_user_user_permissions" CONSTRAINT "auth_user_user_permissions_user_id_a95ead1b_fk_auth_user_id" FOREIGN KEY (user_id) REFERENCES auth_user(id) DEFERRABLE INITIALLY DEFERRED
    TABLE "django_admin_log" CONSTRAINT "django_admin_log_user_id_c564eba6_fk_auth_user_id" FOREIGN KEY (user_id) REFERENCES auth_user(id) DEFERRABLE INITIALLY DEFERRED
```

### \dt[S+] [PATTERN] - list tables \dt

```sql
postgres=# \d
                      List of relations
 Schema |               Name               | Type  |  Owner
--------+----------------------------------+-------+----------
 public | app_model_article                | table | postgres
 public | app_model_category               | table | postgres
 public | app_model_category_subcategories | table | postgres
 public | app_model_person                 | table | postgres
 public | app_model_point                  | table | postgres
 public | app_model_product                | table | postgres
 public | app_model_userprofile            | table | postgres
 public | auth_group                       | table | postgres
 public | auth_group_permissions           | table | postgres
 public | auth_permission                  | table | postgres
 public | auth_user                        | table | postgres
 public | auth_user_groups                 | table | postgres
 public | auth_user_user_permissions       | table | postgres
 public | django_admin_log                 | table | postgres
 public | django_content_type              | table | postgres
 public | django_migrations                | table | postgres
 public | django_session                   | table | postgres
(17 rows)
```

### \l - list of databases

```sql
postgres-# \l
                                List of databases
   Name    |  Owner   | Encoding | Collate | Ctype |   Access privileges   
-----------+----------+----------+---------+-------+-----------------------
 postgres  | postgres | UTF8     | C       | C     | 
 template0 | postgres | UTF8     | C       | C     | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
 template1 | postgres | UTF8     | C       | C     | =c/postgres          +
           |          |          |         |       | postgres=CTc/postgres
 testdb    | postgres | UTF8     | C       | C     | 
(4 rows)
```

### \c select databases

```sql
postgres-# \c testdb
psql (11.2 (Debian 11.2-1.pgdg90+1))
Type "help" for help.
You are now connected to database "testdb" as user "postgres".
testdb=# 
```

# Reference

* [Postgres Guide - Psql](http://postgresguide.com/utilities/psql.html)
* [PostgreSQL - CREATE Database](https://www.tutorialspoint.com/postgresql/postgresql_create_database.htm)
* [PostgreSQL - SELECT Database](https://www.tutorialspoint.com/postgresql/postgresql_select_database.htm)