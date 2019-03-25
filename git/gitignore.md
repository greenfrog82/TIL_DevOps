# [.gitignore](https://www.atlassian.com/git/tutorials/saving-changes/gitignore)

## How to include subfile or subdirectory in excluded parent directory.

다음과 같은 경로가 있다고 가정하자. 

```sh
~/develop/TIL_DevOps/db/postgresql/repo $ ll
total 104
drwx------@ 26 greenfrog  staff    832 Mar  1 21:09 .
drwxr-xr-x   5 greenfrog  staff    160 Mar  1 20:57 ..
-rw-------@  1 greenfrog  staff      3 Jan 13 21:39 PG_VERSION
drwx------@  5 greenfrog  staff    160 Jan 13 21:39 base
drwx------@ 60 greenfrog  staff   1920 Mar  1 21:09 global
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_commit_ts
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_dynshmem
-rw-------@  1 greenfrog  staff   4537 Jan 13 21:39 pg_hba.conf
-rw-------@  1 greenfrog  staff   1636 Jan 13 21:39 pg_ident.conf
drwx------@  5 greenfrog  staff    160 Mar  1 21:09 pg_logical
drwx------@  4 greenfrog  staff    128 Jan 13 21:39 pg_multixact
drwx------@  3 greenfrog  staff     96 Mar  1 21:09 pg_notify
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_replslot
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_serial
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_snapshots
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_stat
drwx------@  3 greenfrog  staff     96 Mar  1 21:27 pg_stat_tmp
drwx------@  3 greenfrog  staff     96 Jan 13 21:39 pg_subtrans
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_tblspc
drwx------@  2 greenfrog  staff     64 Jan 13 21:39 pg_twophase
drwx------@  4 greenfrog  staff    128 Jan 13 21:39 pg_wal
drwx------@  3 greenfrog  staff     96 Jan 13 21:39 pg_xact
-rw-------@  1 greenfrog  staff     88 Jan 13 21:39 postgresql.auto.conf
-rw-------@  1 greenfrog  staff  23759 Mar  1 21:08 postgresql.conf
-rw-------@  1 greenfrog  staff     36 Mar  1 21:09 postmaster.opts
-rw-------   1 greenfrog  staff     94 Mar  1 21:09 postmaster.pid
```

그리고 위 경로(repo)는 현재 .gitignore는 통해 제외되어있다. `.gitignore`파일의 설정은 다음과 같다. 

```
db/postgresql/repo
```

그런데 `./db/postgresql/repo`경로의 파일들 중 특정 디렉토리 또는 파일을 git에 포함하고싶다면 어떻게 해야할까?  
`postgresql.conf`파일과 우선 다음과 같이 `!(무효화)`표현을 통해 문제를 해결해보자. 

```
db/postgresql/repo
!db/postgresql/repo/postgresql.conf
```

이제 `git status`명령을 통해 원하는데로 동작하는지 확인해보자. 

```sh
~/develop/TIL_DevOps/db/postgresql/repo $ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

결과는 위와 같다. 왜 무효화가 되지 않았을까? Atlassian Bithubket의 git tutorial 중 [.gitignore](https://www.atlassian.com/git/tutorials/saving-changes/gitignore)을 보면 다음과 같은 내용이 나온다. 

> 
```
logs/
!logs/important.log
```
>Wait a minute! Shouldn't logs/important.log be negated in the example.
>Nope! Due to a performance-related quirk in Git, you can not negate a file that is ignored due to a pattern matching a directory

이 문제를 해결하기 위해서는 다음고 같이 설정해주면 된다. 

```
db/postgresql/repo/*
!db/postgresql/repo/postgresql.conf
```



