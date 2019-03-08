# wc command

파일 또는 표준입력에 포함 된 단어, 라인, 문자 그리고 바이트의 갯수를 출력한다.  

## Option(s)

* -c : 파일 또는 표준입력에 포함 된 바이트 수를 출력한다. 만약 `-m`옵션이 앞에 사용된다면 `-m`옵션은 무시된다. 
* -l : 파일 또는 표준입력에 포함 된 라인 수를 출력한다. 
* -m : 파일 또는 표준입력에 포함 된 문자 수를 출력한다. 만약 `-c`옵션이 앞에 사용된다면 `-c`옵션은 무시된다. 
* -w : 파일 또는 표준입력에 포함 된 단어 수를 출력한다. 

## Example

`/etc/passwd`가 몇 라인으로 구성되었는지 확인해보자.

```sh
$ wc -l /etc/passwd
     108 /etc/passwd
```

이번에는 단어수도 확인해보자. 

```sh
$ wc -lw /etc/passwd
     108     292 /etc/passwd
```

앞서 `-c`옵션은 바이트 수, `-m`은 문자수라고 했었다. 각각 어떻게 다른지 영문일때와 한글일때를 확인해보자.  

```sh
$ echo "test" | wc -m
       5
$ echo "test" | wc -c
       5
$ echo "한글" | wc -m
       3
$ echo "한글" | wc -c
       7
```

이번에는 `-c`옵션과 `-m`옵션의 선후관계에 따라 옵션이 어떻게 무시되는지 확인해보자.  

```sh
$ echo "한글" | wc -cm
       3
$ echo "한글" | wc -mc
       7
```