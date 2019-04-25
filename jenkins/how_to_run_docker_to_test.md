# How to run docker to test

Jenkins 설정 시 `freestyle template`을 선택해서 Project를 생성했다고 가정하고 내용을 설명한다. 

* **Build** 섹션으로 이동해서 `Add Build step`선택
* `Execute shell` 선택
* `sudo docker-compose up --exit-code-from \<service name\>`작성

`docker-compose`명령에서 `--exit-code-from`옵션을 사용하는 이유는 다음 내용을 참고하자. 

```sh
$ docker-compose up --help
    --abort-on-container-exit  Stops all containers if any container was
                               stopped. Incompatible with -d.
    --exit-code-from SERVICE   Return the exit code of the selected service
                               container. Implies --abort-on-container-exit.
```

위와같이 설정하고 실제로 빌드를 수행해보면 다음과 같은 에러가 떨어진다. 

>\+ sudo docker-compose up --exit-code-from api  
sudo: no tty present and no askpass program specified  
Build step 'Execute shell' marked build as failure  
Finished: FAILURE  

위 문제는 Jenkins가 sudo권한이 없기 때문이다. 이 경우 다음과 같이 `/etc/sudoers`파일에 Jenkins를 추가해주면된다. **(그런데 이렇게 하는게 보안상 좋은 방법인지는 확인 필요!)**

```sh
$ sudo chmod 644 /etc/sudoers
$ vi /etc/sudoers

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
jenkins ALL=(ALL) NOPASSWD: ALL

$ sudo chmod 444 /etc/sudoers
```

# Reference

* [Jenkins sudo: no tty present and no askpass program specified with NOPASSWD
](https://stackoverflow.com/questions/37603621/jenkins-sudo-no-tty-present-and-no-askpass-program-specified-with-nopasswd)