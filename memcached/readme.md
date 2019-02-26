# Memcached

`Memcached`가 무엇인지 어떻게 사용하는지에 대해서 알아보자. 
일단 공부를 위한 `Memcached` 최신버전을 도커를 통해 다음과 같이 설치하였다. 

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

## How to list cached item

캐쉬 된 목록
>$ stats items

```sh
stats items
STAT items:1:number 2
STAT items:1:number_hot 0
STAT items:1:number_warm 0
STAT items:1:number_cold 2
STAT items:1:age_hot 0
STAT items:1:age_warm 0
STAT items:1:age 127
STAT items:1:evicted 0
STAT items:1:evicted_nonzero 0
STAT items:1:evicted_time 0
STAT items:1:outofmemory 0
STAT items:1:tailrepairs 0
STAT items:1:reclaimed 0
STAT items:1:expired_unfetched 0
STAT items:1:evicted_unfetched 0
STAT items:1:evicted_active 0
STAT items:1:crawler_reclaimed 0
STAT items:1:crawler_items_checked 0
STAT items:1:lrutail_reflocked 0
STAT items:1:moves_to_cold 3
STAT items:1:moves_to_warm 1
STAT items:1:moves_within_lru 0
STAT items:1:direct_reclaims 0
STAT items:1:hits_to_hot 0
STAT items:1:hits_to_warm 0
STAT items:1:hits_to_cold 3
STAT items:1:hits_to_temp 0
STAT items:3:number 1
STAT items:3:number_hot 0
STAT items:3:number_warm 0
STAT items:3:number_cold 1
STAT items:3:age_hot 0
STAT items:3:age_warm 0
STAT items:3:age 90689
STAT items:3:evicted 0
STAT items:3:evicted_nonzero 0
STAT items:3:evicted_time 0
STAT items:3:outofmemory 0
STAT items:3:tailrepairs 0
STAT items:3:reclaimed 0
STAT items:3:expired_unfetched 0
STAT items:3:evicted_unfetched 0
STAT items:3:evicted_active 0
STAT items:3:crawler_reclaimed 0
STAT items:3:crawler_items_checked 54
STAT items:3:lrutail_reflocked 2167
STAT items:3:moves_to_cold 880
STAT items:3:moves_to_warm 880
STAT items:3:moves_within_lru 1
STAT items:3:direct_reclaims 0
STAT items:3:hits_to_hot 38
STAT items:3:hits_to_warm 22
STAT items:3:hits_to_cold 939
STAT items:3:hits_to_temp 0
END
```

## How to set value

`set`명령은 이미 존재하는 key에 값을 업데이트하거나, 해당 key에 값을 저장한다. (`add`명령의 경우는 이미 존재하는 key에 대해서 `NOT_STORED`에러가 발생한다.)

>set key flags exptime bytes [noreply]

### Parameter(s)

* key : 캐쉬하고자하는 value에 대한 key
* flags : 32-bit unsigned 

## How to get value



# Reference

* [telnet을 이용한 memcached 관리](https://andromedarabbit.net/telnet%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-memcached-%EA%B4%80%EB%A6%AC/)
* [Slabs, Pages, Chunks and Memcached](https://www.mikeperham.com/2009/06/22/slabs-pages-chunks-and-memcached/)
* [tutorialspoint](https://www.tutorialspoint.com/memcached/index.htm)