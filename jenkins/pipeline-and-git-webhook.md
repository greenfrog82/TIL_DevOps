# How to set pipeline and webhook of github

Jenkins를 Docker를 통해서 설치하고 webhook을 통해 빌드를 실행시키는 방법에 대해서 설명한다. 

## Run Jenkins

다음 명령을 통해 `DockerHub`에서 Jenkins 이미지를 내려받자.

>$ docker pull jenkins/jenkins

다음 명령을 통해 Jenkins를 실행시키자. 

>$ docker run -d -v $(pwd)/jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000  jenkins/jenkins

## Getting Started

Jenkins가 처음 실행되면 `Getting Started` 대화상자가 실행된다. 

### Unlock Jenkins

다음 명령을 통해 volume으로 묶인 호스트 PC의 jenkins_home에서 초기화 암호를 복사해서 대화상자의 텍스트박스에 입력한다. 

>$ cat jenkins_home/secrets/initialAdminPassword

### Customize Jenkins

Jenkins의 플러그인들을 설치하기 위한 화면이 출력된다. `Install suggested plugins`를 선택하자.

### Create First Admin User

마지막 단계로 Jenkins의 Admin 계정을 생성한다. 

## ngrok

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

* [DockerHub - Jenkins](https://hub.docker.com/r/jenkins/jenkins)
* [Adding a GitHub Webhook in Your Jenkins Pipeline
](https://dzone.com/articles/adding-a-github-webhook-in-your-jenkins-pipeline)
* [젠킨스 파이프라인 정리 - 1. 파이프라인 샘플 만들기](https://jojoldu.tistory.com/355)
* [젠킨스 파이프라인 정리 - 2. Scripted 문법 소개](https://jojoldu.tistory.com/356?category=777282)
* [Jenkins 로 빌드 자동화하기 1 - GitHub 에 push 되면 자동 빌드하도록 구성](https://yaboong.github.io/jenkins/2018/05/14/github-webhook-jenkins/)
* [ngrok으로 로컬 네트워크의 터널 열기](https://blog.outsider.ne.kr/1159)
* [Jenkins and github integration using webhooks](https://blog.tentamen.eu/jenkins-and-github-integration-using-webhooks/)