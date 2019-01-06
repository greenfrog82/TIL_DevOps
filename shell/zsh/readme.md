# zsh Tip and Trick 

## Ctrl + r

`history`에서 특정 키워드ㄹ를 검색할 때 유용한 단축키이다. 해당 단축키를 사용하면 검색하고자하는 키워드를 입력하는 프롬프트가 출력되고 여기에 키워드를 입력하면 검색이된다. 키워드가 검색된 상태에서 `Ctrl+r`은 해당 키워드로 `history`를 이전으로 뒤지고 `Ctrl+s`는 앞으로 뒤진다.  

에를들어, 다음과 같은 `history`가 있다고 가정하자. 

```sh
$ history
1788* cd TIL_Python
1789* git config --local
1790* git config
1791* git config --local username
1792* git config --local user
1793* git config --global
1794* ll
1795* ls -al
1796* vi .git/config
1797* git config --local user.name
1798* git config --local user.email
1799* cd ../TIL_DevOps
1800* ll
1801* git config --local user.name
1802* git config --local user.name greenfrog82
1803* git config --local user.email greenfrog82@naver.com
1804* git config --local user.name
1805* git config --local user.email
```

위 `history`에서 `Ctrl+r`을 통해 `--local` 키워드로 검색을해보자.  

```sh
git config --local user.email
bck-i-search: --local_
```

위와 같이 검색이 된 상태에서 `Ctrl+r`을 계속해서 누르면 `history`의 이전단계를 하나씩 검색한다. 

```sh
git config --local user.name
bck-i-search: --local_
```

다시 `Ctrl+s`를 누르면 `history`의 앞 단계를 하나씩 검색한다. 

```sh
git config --local user.email
bck-i-search: --local_
```

