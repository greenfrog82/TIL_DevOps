# Raw Level Git Commands

본 문서에서는 git의 저수준 명령들에 대해서 알아본다. 

## symbolic-ref

`symbolic refs`를 읽고, 수정하고 삭제한다. 

### Getting branch name of HEAD

>git symbolic-ref --short HEAD

```sh
analyze-fab $ git symbolic-ref --short HEAD
analyze-fab
```

## rev-list

`git log`와 근본적으로는 동일한 명령으로 commit 목록을 최신순으로 나열한다. 

>git rev-list HEAD

```sh
$ git log --pretty=oneline
a659bc1 (HEAD -> master, origin/master, origin/HEAD) git - How to print changed file list.
a782a93 move git from study to here.
ebd3d1e Performance - About Apdex
a212f36 Jenkins - add Poll SCM and Strategy for choosing what to build.
f2a26b1 Merge branch 'master' of https://github.com/greenfrog82/TIL_DevOps
07d65df VIM - How to perform external command in vim.
06c4bbf Merge branch 'master' of https://github.com/greenfrog82/TIL_DevOps
4b30a12 Postgres - psql commands.
$
$ git pre-list HEAD
659bc140c2cd1467f4432cb8fadd6d1344c29a1
a782a9302e125479acd9b8f0f029c770b36cd04a
ebd3d1ead6b43c9d2c7b5d6631463d26b44208bf
a212f36253abeaf8914a21d0b1b92ce64e77d990
f2a26b1071bb83f7d975003ab94daa9043e80866
07d65df53808d509cf2171c62bc5d46d16ff6b84
06c4bbf708e9cc3cce29c6225556fedc03b1b90d
4b30a127f26bbf598efc047909dafae79073f88c
```

## rev-parse

### Getting SHA1 of HEAD

>git rev-parse HEAD

```sh
$ git rev-parse HEAD
ae9c45b66eb7e31bd4d990590817de381eb37174
```

## cherry

특정 브랜치에 merged되지 않은 커밋을 찾는다.  

### Option(s)

* -v : SHA1과 함께 커밋 코멘트도 함께 출력 

### Getting unmerged commit

>git cherry -v origin/master HEAD

```sh
$ git cherry origin/master HEAD
+ 6c8738800941c569539cb5b491b8912e9b9e5ac2
$ git cherry -v origin/master HEAD
+ 6c8738800941c569539cb5b491b8912e9b9e5ac2 test
```





