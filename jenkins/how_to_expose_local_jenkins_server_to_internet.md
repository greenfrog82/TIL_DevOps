# How to expose local jenkins server to internet

Github과 연결하기 전 개발 PC에 설치해둔 Jenkins 서버를 인터넷에 노출시켜야한다. 이를 위해 [ngrok](https://ngrok.com/)을 사용할 것이다.

### Install ngrok on Mac

>$ brew cask install ngrok

### Run ngrok

개발 PC에서 실행 중인 Jenkins의 포트를 `ngrok`의 파라메터로 전달하여 실행시키자. 

>$ ngrok http 8080

다음은 ngrok을 실행한 모습이다. `Forwarding`이 표시하고 있는 URL을 통해 Jenkins가 인터넷으로 노출된다. 

```sh
ngrok by @inconshreveable                                                              (Ctrl+C to quit)

Session Status                online
Session Expires               7 hours, 59 minutes
Version                       2.3.25
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://6d69fa0c.ngrok.io -> http://localhost:8080
Forwarding                    https://6d69fa0c.ngrok.io -> http://localhost:8080

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

# Reference

* [ngrok으로 로컬 네트워크의 터널 열기](https://blog.outsider.ne.kr/1159)