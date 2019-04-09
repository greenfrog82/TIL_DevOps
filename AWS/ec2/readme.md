# EC2

## How to connect to EC2 

>$ chmod 400 private_key
>$ ssh -i private_key public_dns

```sh
$ chmod 400 jaeyoungcho.pem
$ ssh -i jaeyoungcho.pen ubuntu@ec2-xx-xxx-xxx-xxx.us-xxxx.compute.amazonaws.com
```

## How to translate file to EC2

>$ scp -i private_key user@public_dns:path

```sh
$ scp -i jaeyoungcho.pem ubuntu@ec2-xx-xxx-xxx-xxx.us-xxxx.compute.amazonaws.com:~
```

## How to connect to server on EC2

EC2는 방화벽에 의해 보호를 받는다. 따라서 별도 서버를 실행시킨 후 외부에서 해당 서버에 접속하기 위해서는 `보안 그룹`에서 `인바운드`규칙을 추가해주어야한다.  
기본설정을 `SSH`만 오픈되어있다. 

1. EC2 대시보드
2. 인스턴스 
3. 보안 그룹
4. 인바운드
5. 편집을 통해 서버 정보 입력 

# Reference

* [SSH를 사용하여 Linux 인스턴스에 연결](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)
