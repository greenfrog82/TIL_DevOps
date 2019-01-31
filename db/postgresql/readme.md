# Postgresql

## pg_locks view

어떤 락이 할당되어있는지, 어떤 프로세스가 락을 얻기위해 대기하가고 있는지 확인하기 위해서는 `pg_locks`뷰를 확인하도록하자.   
`pg_locks`뷰를 확인하기 위해서는 다음 쿼리를 사용하자. 

```sql
SELECT relation::regclass, * FROM pg_locks WHERE NOT GRANTED;
```

# Reference

* [Lock Monitoring](https://wiki.postgresql.org/wiki/Lock_Monitoring)