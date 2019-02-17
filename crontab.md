# crontab

crontab은 특정작업을 주지적으로 실행시키기 위한 스케쥴러이다.  

## Command

### crontab -e

crontab을 shell의 EDITOR 환경변수에 지정된 편집기를 통해 편집한다. 

crontab에 항목을 추가하는 기본적인 방법은 다음과 같다. 맨 뒤에 실행되야할 명령어를 작성하고 앞에 동작해야하는 주기를 입력한다.  
다음과 같은 경우 **매분마다 실행**된다. 

>\* \* \* \* \* ls -al

### crontab -l

crontab에 등록된 목록을 화면에 나열한다.

### crontab -r 

crontab을 삭제합니다. **등록된 모든 내용이 삭제되므로 사용에 주의해야한다.**

## How to set period

crontab에 스케쥴을 등록할 때 주기가 어떻게 표현되고 어떻게 실행되는지 알아보자.  
앞서 알아본 바와 같이 crontab에 주기는 다음과 같이 다섯가지로 구분된다. 

>\*        \*       \*      \*       \*
>분(0-59) 시간(0-23) 일(1-31) 월(1-12) 요일(0-7)

crontab에서 주기는 위와같이 **분-시간-일-월-요일**순으로 작성되며, 각각 입력가능한 숫자 범위는 각각의 괄호에 표시한대로다. 

요일은 0과 7은 일요일이고, 1 ~ 6까지는 월요일부터 토요일까지이다. 

## Example

### Every Minute

```
# 매분 command.py 실행
\* \* \* \* \* python /opt/command.py
```

### Specific Time

```
# 매주 금요일 오전 5시 45분에 command.py 실행
45 5 \* \* 5 python /opt/command.py
```

### Repeated Execution

```
# 매일 매시간 0분, 20분, 40분에 command.py 실행
0,20,40 * * * * python /opt/command.py
```

### Range Execution

```
# 매일 1시 0분부터 30분까지 매분 command.py 실행
0-30 1 * * * python /opt/command.py
```

# Reference

* [How to Display (List) All Jobs in Cron / Crontab](https://www.liquidweb.com/kb/how-to-display-list-all-jobs-in-cron-crontab/)
* [리눅스 크론탭(Linux Crontab) 사용법](https://jdm.kr/blog/2)