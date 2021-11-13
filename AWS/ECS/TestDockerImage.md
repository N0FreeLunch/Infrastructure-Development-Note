# 태스트 도커 이미지 만들기

## 리눅스 사용자라면 패키지 버전 업데이트를 하자.
- 패도라, CentOS, REPL 계열인 경우
```
sudo yum update -y
```


## OS에 도커 프로그램 설치
- 도커 홈페이지에서 제공하는 여러 설치 방법들을 활용해 설치하자.
- 다음 과정은 로컬의 컴퓨터가 아닌 AWS 인스턴스를 통해서 도커 이미지를 만들기 위한 과정이다.

#### AMI2의 경우
```
sudo amazon-linux-extras install docker
```
#### AMI1의 경우
```
sudo yum install docker
```
#### 도커 서비스 시작
```
sudo service docker start
```

#### 도커 서비스 실행 권한 부여
```
sudo usermod -a -G docker ec2-user
```


## Reference
- https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/userguide/docker-basics.html
