# How to install Jenkins

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

# Reference

* [Auto Merge](https://andrewtarry.com/jenkins_git_merges/)
