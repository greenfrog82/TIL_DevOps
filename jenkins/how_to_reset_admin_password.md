# How to reset admin password

관리자 게정의 비번을 잃어버린 경우 /var/lib/jenkins.config.xml 파일은 열어서 다음 노드들을 안내와 같이 처리한다. 

* `useSecurity`노드의 값을 true -> false로 변경
* `authoizationStrategy`노드를 주석처리

```xml
  <useSecurity>false</useSecurity>
  <!--authorizationStrategy class="hudson.security.AuthorizationStrategy$Unsecured"/-->
```

그리고 Jenkins 서버를 재시작한다. 

```sh
$ /etc/init.d/jenkins restart 
or 
$ docker restart \<jenkins container id\> 
```

재시작 된 Jenkins 서버로 이동해서 다음 절차대로 admin계정의 비밀번호를 변경한다. 

1. `Jenkins 관리` 메뉴 선택
2. `Configure Global Security`메뉴 선택
3. `Enable security`체크 박스 선택
4. `Security Realm`의 `jenkins own user database`선택
5. `Manage Users` 메뉴 선택
6. 관리자 계정의 비번 변경 

# Reference

* [관리자 암호 분실시 대처법](https://noota.tistory.com/entry/Jenkins-%EA%B4%80%EB%A6%AC%EC%9E%90-%EC%95%94%ED%98%B8-%EB%B6%84%EC%8B%A4%EC%8B%9C-%EB%8C%80%EC%B2%98%EB%B2%95)
* [Jenkins Manageing Users](https://confluence.curvc.com/display/ASD/8.+Jenkins+Managing+Users)