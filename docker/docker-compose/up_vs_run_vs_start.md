# What’s the difference between up, run, and start?

`docker-compose`의 실행 사용할 때 명령으로 `up`, `run` 그리고 `start`를 사용할 수 있는데, 각각의 차이점이 무엇인지 알아보자.  

## up

`up`명령은 `docker-compose.yml`파일에 정의된 서비스들을 시작하거나 재시작할 때 사용한다. 기본적으로는 `attached`모드로 동작하여 모든 컨테이너들이 출력하는 로그가 화면에 나타난다. `-d`옵션을 사용하면 `detached`모드로 동작하는데 `docker-compose`는 종료되지만 실행 된 모든 컨테이너들은 백그라운드에서 여전히 동작한다. 

### docker-compose up

```sh
$ docker-compose up
Creating greenfrog-redis                   ... done
Creating greenfrog-memcached               ... done
Creating greenfrog-postresql-how-to-django ... done
Creating til_python_adminer_1              ... done
Creating greenfrog-rabbitmq                ... done
Attaching to greenfrog-redis, greenfrog-postresql-how-to-django, greenfrog-memcached, til_python_adminer_1, greenfrog-rabbitmq
greenfrog-redis | 1:C 28 Dec 2018 14:03:31.731 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
greenfrog-redis | 1:C 28 Dec 2018 14:03:31.731 # Redis version=5.0.0, bits=64, commit=00000000, modified=0, pid=1, just started
greenfrog-redis | 1:C 28 Dec 2018 14:03:31.731 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
greenfrog-redis | 1:M 28 Dec 2018 14:03:31.734 * Running mode=standalone, port=6379.
greenfrog-redis | 1:M 28 Dec 2018 14:03:31.734 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
greenfrog-redis | 1:M 28 Dec 2018 14:03:31.734 # Server initialized
greenfrog-redis | 1:M 28 Dec 2018 14:03:31.734 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
greenfrog-redis | 1:M 28 Dec 2018 14:03:31.734 * Ready to accept connections$
```

### docker-compose up -d

```sh
$ docker-compose up -d
Starting greenfrog-redis                   ... done
Starting greenfrog-postresql-how-to-django ... done
Starting greenfrog-memcached               ... done
Starting til_python_adminer_1              ... done
Starting greenfrog-rabbitmq                ... done
```

## run

`run`명령은 일회성 작업을 실행하기 위해서 사용된다. 이 명령은 실행시키고자 하는 서비스의 이름을 요구하며, 실행되는 서비스가 의존하는 서비스의 컨테이너들만 시작된다. 컨테이너의 데이터 볼륨에 데이터를 추가, 삭제하는것과 같은 관리적인 작업을 수행하거나 테스트할 때 `run`을 사용해라. `run`명령은 인터렉티브 터미널을 열고 컨테이너에서 실행되는 프로세스의 종료상태와 매칭되는 종료상태를 반환하는 `docker run -it`처럼 동작한다. 

`run`명령을 통해 전달되는 실행 커맨드는 `docker-compose.yml`의 설정을 변경하여 컨테이너를 생성한다. 예를들어, `web`서비스가 `bash`로 실행되었다고 할 때, `docker-compose run web python app.py`명령을 수행하면 `python app.py`로 설정이 변경되어 컨테이너가 생성된다.

또한 `run`명령은 `docker-compose.yml`설정에 명시된 호스트와의 포트 맵핑을 수행하지 않는다. 이것은 이미 생성 된 포트들과의 충돌을 방지하기 위한것으로 만약 컨테이너에서 실행 중인 포트와 호스트의 포트를 맵핑하고자 한다면 `--service-ports`를 명시해줘야한다.  

다음과 같이 `docker-compose up -d db`명령을 통해 db 서비스를 실행시키면, 다음과 같이 포트가 열리고 호스트와 맵핑된다.  

```sh
$ docker-compose up -d db
greenfrog-postresql-how-to-django is up-to-date
$ docker-compose ps
              Name                             Command              State           Ports
--------------------------------------------------------------------------------------------------
greenfrog-postresql-how-to-django   docker-entrypoint.sh postgres   Up      0.0.0.0:5432->5432/tcp
```

이번에는 위 서비스를 삭제한 후 `start`명령을 통해 db 서비스를 실행시켜보자. 이 경우는 포트는 열리지만 호스트와 맵핑되지는 않는다. 그런데 여기서 또 하나의 특이점을 찾을 수 있는데, `run`명령을 통해 생성 된 컨테이너는 `docker-compose.yml`에 명시된 서비스를 실행시키지만 매번 다른 서비스로 생성된다는 것이다. `up`명령의 경우 `docker-compse.yml`에 명시된 서비스를 일관되게 실행하고 중지한다.  

```sh
$docker-compose run -d db
til_python_db_run_3ab70b386c67
$ docker-compose ps
             Name                           Command              State    Ports
---------------------------------------------------------------------------------
til_python_db_run_3ab70b386c67   docker-entrypoint.sh postgres   Up      5432/tcp
```

이번에는 `--service-ports`옵션을 통해 호스트와 포트를 맵핑해보자. 다음과 같이 호스트와 포트가 맵핑된것을 알 수 있다. 

```sh
$ docker-compose run -d --service-port db
til_python_db_run_a3f5bd1907d7
$ docker-compose ps
             Name                           Command              State           Ports
-----------------------------------------------------------------------------------------------
til_python_db_run_a3f5bd1907d7   docker-entrypoint.sh postgres   Up      0.0.0.0:5432->5432/tcp
```

앞서 `run`명령은 컨테너안에 실행 된 서비스의 포트는 열지만 호스트와 포트를 맵핑하지는 않는다고 했다. 그럼 `up`명령을 통해 db 서비스를 실행 한 후, `run`명령의 `--service-ports`옵션을 통해 서비스를 실행시켜보자.   
다음과 같이 이미 호스트와 맵핑 된 포트를 열려고 시도하다 에러가 발생하였다.   

```sh
$ docker-compose up -d db
Creating greenfrog-postresql-how-to-django ... done
$ docker-compose ps
              Name                             Command              State           Ports
--------------------------------------------------------------------------------------------------
greenfrog-postresql-how-to-django   docker-entrypoint.sh postgres   Up      0.0.0.0:5432->5432/tcp
$ docker-compose run -d --service-port db
ERROR: Cannot start service db: driver failed programming external connectivity on endpoint til_python_db_run_263fa7dfb3ee (1e6298459690feaa634894e44a79660b375c41a179afb2dcbcf7bdf0cf6290d1): Bind for 0.0.0.0:5432 failed: port is already allocated
```

## start

`start`명령은 이미 생성되었거나 종료된 컨테이너들을 재시작하기 위한 용도로만 유용하다. 이 명령은 새로운 컨테이너를 생성하지 않는다. 

# Reference

* [Frequently asked questions](https://docs.docker.com/compose/faq/)
* [Should I use docker-compose up or run?](https://stackoverflow.com/questions/33066528/should-i-use-docker-compose-up-or-run)