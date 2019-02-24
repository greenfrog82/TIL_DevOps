# Memcached

`Memcached`가 무엇인지 어떻게 사용하는지에 대해서 알아보자. 
일단 공부를 위한 `Memcached`는 도커통해 다음과 같이 설치되어있다.

```
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                           NAMES
e64f7d792d88        memcached:latest      "docker-entrypoint.s…"   2 months ago        Up 2 days           0.0.0.0:11211->11211/tcp        greenfrog_memcached
```

## How to connect to Memcached 

Memcached에 접속을 할 때는 `telnet`을 사용한다. 
`telnet`을 통해 접속하는 형식은 다음과 같다.  

>$ telnet \<server host name or ip address\> \<memcached port\>

위 명령을 통해 로컬의 도커 컨테이너에서 동작 중이냐 `Memcaced`에 접속해보자. 

```sh
$ telnet localhost 11211
Trying ::1...
Connected to localhost.
Escape character is '^]'.

```
