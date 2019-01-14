# [cherry-pick](https://git-scm.com/docs/git-cherry-pick)

`cherry-pick`은 특정 브랜치의 커밋을 선택해서 현재 선택된 브랜치로 `Merge`를 수행한다. 

먼저 다음과 같이 `release`브랜치를 구성한다.

```git
* ad381e2 - (HEAD -> release) release - 3 (7 minutes ago) <test>
* 9a365bf - release - 2 (8 minutes ago) <조재영(Cho>
* 55cc767 - release - 1 (9 minutes ago) <조재영(Cho>
```

`release`브랜치에는 `sample.txt`파일이 존재하는데 각 커밋별 저장된 데이터는 다음과 같다. 

```
1 <- 55cc767 - release - 1 (9 minutes ago) <조재영(Cho>
2 <- 9a365bf - release - 2 (8 minutes ago) <조재영(Cho>
3 <- ad381e2 - (HEAD -> release) release - 3 (7 minutes ago) <test>
```

이번에는 initial commit인 55cc767로 이동해서 `feature`브랜치를 생성한 후 다음과 같이 커밋 이력을 만들자.  

```git
* 36c7e99 - (HEAD -> feature) feature - 5 (5 minutes ago) <test>
* 4ff6c25 - feature - 4 (5 minutes ago) <test>
* fb8121b - feature - 3 (6 minutes ago) <test>
* 8859af4 - feature - 2 (8 minutes ago) <test>
* 55cc767 - release - 1 (13 minutes ago) <조재영(Cho>
```

`feature`브랜치에는 `release`브랜치와 동일한 `sample.txt`파일이 존재한다. 각 커밋별 저장된 데이터는 다음과 같다.  

```
1 <- release - 1 (13 minutes ago) <조재영(Cho>
2 <- 8859af4 - feature - 2 (8 minutes ago) <test> 
3 <- fb8121b - feature - 3 (6 minutes ago) <test>
4 <- 4ff6c25 - feature - 4 (5 minutes ago) <test>
5 <- 36c7e99 - (HEAD -> feature) feature - 5 (5 minutes ago) <test>
```

이제 `release`브랜치의 `sample.txt`에 `feature`브랜치의 4, 5에 해당하는 데이터를 `cherry pick`해서 추가해보자.   
`release`브랜치로 체크아웃한 후 `feature`브랜치에서 `cherry pick`하고자하는 SHA1값을 입력해주면 된다.  

```sh
$ git cherry-pick 4ff6c25
[release 9dbc290] feature - 4
 Date: Mon Jan 14 20:35:30 2019 +0900
 1 file changed, 1 insertion(+)
$ git cherry-pick 36c7e99
[release c384427] feature - 5
 Date: Mon Jan 14 20:35:50 2019 +0900
 1 file changed, 1 insertion(+)
```

이제 git log를 찍어보면 기존 `ad381e2`커밋 위로 새로운 SHA1값으로 `feature`브랜치의 각각의 commit 이력이 추가된것을 확인 할 수 있다. 

```
* c384427 - (HEAD -> release) feature - 5 (19 seconds ago) <test>
* 9dbc290 - feature - 4 (4 minutes ago) <test>
* ad381e2 - release - 3 (21 minutes ago) <test>
* 9a365bf - release - 2 (22 minutes ago) <조재영(Cho>
* 55cc767 - release - 1 (23 minutes ago) <조재영(Cho>
```

앞서는 `cherry-pick`이 충돌없이 이루어졌지만, 만약 conflict가 발생한다면 일반적으로 merge conflict가 발생했을때와 마찬가지로 수동으로 conflict를 해소하고 merge 할 수 있도록 유도한다.
예를들어, 앞서 `release`브랜치의 '2'값을 'greenfrog'로 수정하여 커밋한 후 `feature`브랜치의 '2'값에 해당하는 SHA1값(8859af4)을 `cherry-pick`해보자.  

```
1
greenfrog
3
4
5
```

```sh
$ git cherry-pick 8859af4
error: could not apply 8859af4... feature - 2
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
$ git status
On branch release
You are currently cherry-picking commit 8859af4.
  (fix conflicts and run "git cherry-pick --continue")
  (use "git cherry-pick --abort" to cancel the cherry-pick operation)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   sample.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

다음과 같이 sample.txt을 확인하면 충돌이 난 부분을 확인 할 수 있다. 

```sh
$ vi sample.txt
1 1
2 <<<<<<< HEAD
3 test
4 3
5 4
6 5
7 =======
8 2
9 >>>>>>> 8859af4... feature - 2
```

적절히 수정한 후 다음 명령을 통해 conflict를 해결하도록 한다.  

```sh
$ git add sample.txt
$ git cherry-pick --continue
```

다시 commit 로그를 확인해보면 `feature`브랜치의 `8859af4`에 해당하는 commit이 새로운 SHA1값으로 commit 된것을 확인할 수 있다.  

```sh
* 3c27143 - (HEAD -> release) feature - 2 (4 minutes ago) <test>
* 2b05d0b - release - test (12 minutes ago) <test>
* c384427 - feature - 5 (32 minutes ago) <test>
* 9dbc290 - feature - 4 (36 minutes ago) <test>
* ad381e2 - release - 3 (53 minutes ago) <test>
* 9a365bf - release - 2 (54 minutes ago) <조재영(Cho>
* 55cc767 - release - 1 (55 minutes ago) <조재영(Cho>
```







# Reference

[Git Cherry Pick](https://medium.com/@coviamtech/git-cherry-pick-7c41b317d034)