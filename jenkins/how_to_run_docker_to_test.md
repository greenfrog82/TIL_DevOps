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

# Reference

* [Jenkins sudo: no tty present and no askpass program specified with NOPASSWD
](https://stackoverflow.com/questions/37603621/jenkins-sudo-no-tty-present-and-no-askpass-program-specified-with-nopasswd)