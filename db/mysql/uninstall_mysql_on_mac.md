# How to uninstall MySQL on Mac

`MySQL`을 `brew`를 통해서 설치했으면 좋았을텐데 맥을 처음쓰는거라 그냥 설치했더니 지우는 과정도 복잡하다.   

다음 과정을 따라하면 된다.  

`System Preferences`에서 `MySQL`아이콘을 우클릭한다. 그러면 `Remove "MySQL" Preference Pane`라는 팝업메뉴가 나타나는데 이를 통해 해당 아이콘을 삭제한다.  

`Finder`의 `Applications`에서 `MySQL` 아이콘을 삭제한다. 

`Shell Command`에서 다음 명령들을 차례로 실행시키면 끝!

```sh
$ sudo rm /usr/local/mysql
$ sudo rm -rf /usr/local/mysql*
$ sudo rm -rf /private/var/db/receipts/*mysql*
```

# Reference

* [How to Uninstall MySQL on macOS](https://nektony.com/how-to/uninstall-mysql-on-mac)