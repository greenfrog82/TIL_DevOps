# cut command

`cut` command는 파일의 각 라인의 선택 된 부분만을 잘라낸다.   
기본적인 사용형태는 다음과 같다. `file`은 옵션은 옵션으로 파일명이 전달되지 않으면 `Standard Input`을 입력값으로 사용한다. 

>cut \<option\> \<list\> [file]

## Option(s)

### -d delim

`tab`이 아닌 구분자를 통해 필드를 나눈다. 해당 옵션은 반드시 다른 옵션들과 함께 사용해야한다.  

### -f list

`list`옵션 파라메터는 콤마 또는 공백으로 부분되는 숫자나 범위 목록이다. 
`list`옵션은 분리 된 특정 필드를 특정한다. 

## Example

`/etc/passwd`의 내용은 다음과 같다. 

```
##
# User Database
#
# Note that this file is consulted directly only when the system is running
# in single-user mode.  At other times this information is provided by
# Open Directory.
#
# See the opendirectoryd(8) man page for additional information about
# Open Directory.
##
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
```

이제 위 데이터를 읽어서,  `:`를 기준으로 데이터를 분리한 후 첫번째와 일곱번째 필드를 화면에 출력해보자. 


```sh
$ cut -d : -f 1,7 /etc/passwd
##
# User Database
#
# Note that this file is consulted directly only when the system is running
# in single-user mode.  At other times this information is provided by
# Open Directory.
#
# See the opendirectoryd(8) man page for additional information about
# Open Directory.
##
nobody:/usr/bin/false
root:/bin/sh
daemon:/usr/bin/false
```

이번에는 위 데이터를 첫번째부터 세번째 필드와 일곱번째 필드를 출력해보자. 

```sh
$ cut -d : -f 1-3,7 /etc/passwd
##
# User Database
#
# Note that this file is consulted directly only when the system is running
# in single-user mode.  At other times this information is provided by
# Open Directory.
#
# See the opendirectoryd(8) man page for additional information about
# Open Directory.
##
nobody:*:-2:/usr/bin/false
root:*:0:/bin/sh
daemon:*:1:/usr/bin/false
```