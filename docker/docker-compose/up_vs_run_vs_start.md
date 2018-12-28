# What’s the difference between up, run, and start?

`docker-compose`의 실행 사용할 때 명령으로 `up`, `run` 그리고 `start`를 사용할 수 있는데, 각각의 차이점이 무엇인지 알아보자.  

## up

`up`명령은 `docker-compose.yml`파일에 정의된 서비스들을 시작하거나 재시작할 때 사용한다. 기본적으로는 `attached`모드로 동작하여 모든 컨테이너들이 출력하는 로그가 화면에 나타난다. `-d`옵션을 사용하면 `detached`모드로 동작하는데 `docker-compose`는 종료되지만 실행 된 모든 컨테이너들은 백그라운드에서 여전히 동작한다. 

### Example

#### docker-compose up

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

#### docker-compose up -d

```sh
$ docker-compose up -d
Starting greenfrog-redis                   ... done
Starting greenfrog-postresql-how-to-django ... done
Starting greenfrog-memcached               ... done
Starting til_python_adminer_1              ... done
Starting greenfrog-rabbitmq                ... done
```

## run

`run`명령은 일회성 작업을 실행하기 위해서 사용된다. 이 명령은 실행시키고자 하는 서비스의 이름을 요구하며, 실행되는 서비스가 의존하는 서비스의 컨테이너들만 시작된다. 컨테이너의 데이터 볼륨에 데이터를 추가, 삭제하는 것과 같은 관리적인 작업을 수행하거나 테스트할 때 `run`을 사용해라. `run`명령은 인터렉티브 터미널을 열고 컨테이너에서 실행되는 프로세스의 종료상태와 매칭되는 종료상태를 반환하는 `docker run -it`처럼 동작한다. 

## start

`start`명령은 이미 생성되었거나 종료된 컨테이너들을 재시작하기 위한 용도로만 유용하다. 이 명령은 새로운 컨테이너를 생성하지 않는다. 

# Reference

* [Frequently asked questions](https://docs.docker.com/compose/faq/)