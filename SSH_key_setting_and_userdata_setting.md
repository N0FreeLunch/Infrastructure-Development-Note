# SSH key setting in Userdata on AWS server Instance

## Reference
https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/


## Reason
### userdata
- AWS의 인스턴스를 새로 만들어 기동할 때 초기 구동과 동시에 서버에 세팅해 줄 수 있는 옵션이 있다.
- AWS가 REPL 기반이므로 bash 쉘을 이용한 세팅을 집어 넣어 줄 수 있다.

### git clone
- 깃허브나 비트버킷의 레포지토리를 클론할 때, ssh 키로 클론을 하는 방법이 있다.
- 비트버킷의 경우, 쓰기 및 삭제 권한이 없는 읽기 전용의 권한으로 프라이빗 레포지토리의 클론을 할 수 있게 한다. 이를 위해서는 ssh 키를 사용하여 사용할 줄 알아야 한다.
- ssh 키를 사용하지 않는다면 유저의 아이디와 비밀번호를 세팅을 해서 클론을 해야 하는데 이는 노출되어서는 안 되는 정보이기 때문에 ssh 키를 사용하는 방법을 알아야 한다.


## 주의 할 점
- CPU, OS, OS버전 등의 여러 요인에 의해 어떤 컴퓨터에서 생성된 공개키 비밀키가 다른 컴퓨터에서는 사용할 수 없는 경우가 있다.
- 로컬 환경에서 발급 받은 공개키, 비밀키 페어가 실제 서버에서는 동작하지 않을 가능성이 존재한다.
- 그래서 AWS의 서버 인스턴스에 공개키 비밀키를 세팅하려고 한다면 동일한 환경의 OS를 세팅하고 발급 받아야 한다.


## ssh 키 생성
```
ssh-keygen
```

> Generating public/private rsa key pair. Enter file in which to save the key ($home/.ssh/id_rsa):
- 공개키와 비밀키를 만드는데 경로와 파일명을 생성할 것이냐고 묻는 것.
- 지정을 하지 않으면 생성을 하게 되면 다음 경로에 공개키와 비밀키 쌍을 생성
```
ls $home/.ssh
```
- 기본적으로 위 경로에 ssh 키 세팅이 되어 있을 수 있다. 새로 만들게 되면 덮어 씌워버리기 때문에 다른 이름으로 만들어 주느 편이 좋으며, 기존 ssh 키를 사요하지 않는다면, 덮어 씌워서 만들어도 된다. 그런데 은근히 알지 못하는 중요한 부분에서 쓰이더라
- 공개키, 비밀키를 세팅하려는 서버의 환경(CPU, OS, OS version)에 따라 호환 되지 않을 수 있기 때문에 세팅할 서버와 동일한 환경에서 만들어 준다면 새로 만든 경우가 많으므로 특별히 경로와 파일명을 지정해 주지 않아도 된다.

### 생성확인
```
ls $home/.ssh
```
>  id_rsa, id_rsa.pub
- 위 두 파일이 생성 되는 것을 확인할 수 있다.
- 공개키 : id_rsa.pub
- 비밀키 : id_rsa

## ssh 키로 레포지토리 클론을 하기위해 비트버킷에 설정해야 하는 과정
1. 비트버킷의 원격 레포지토리로 가서 'Repository settings > access key'의 'add key' 부분에 id_rsa.pub 파일에 적힌 코드를 입력한다.
2. 레포지토리 우측 상단의 클론 메뉴에서 ssh 방식을 선택한다.
3. 공개키와 비밀키를 만들어준 컴퓨터에서 'git clone git@bitbucket.org:accountOrGroup/repository.git'를 해서 클론이 되는지 확인하자.

클론을 시도하면 
```
The authenticity of host 'bitbucket.org (123.456.789.0)' can't be established. 
RSA key fingerprint is 12:23:34:56:78:90:ab:bc:cd:ef:gh:hi:jp:lm:mn:op. 
Are you sure you want to continue connecting (yes/no)?     
```
yes누르면 클론이 되고 
```
ls $home/.ssh
```
경로에 known_hosts라는 파일이 생긴다.

### known_hosts의 역할
- git 주소에 대한 접속 기록을 생성하고 접속 기록이 생겼다면 다음번 git clone 명령을 사용할 때는 위와 같이 묻지 않는다. 물론 남겨진 기록과 같은 레포지토리 주소여야 할 것이다.


최종적으로`ls $home/.ssh`에는 3개의 파일이 생긴다.
- id_rsa
- id_rsa.pub
- known_hosts


## 인스턴스 생성과 동시에 클론 하게 만들기
### userdata
- AWS의 인스턴스 세팅을 하는 부분의 옵션을 보면 userdata라는 부분이 존재
- userdata를 통해 ssh 방식의 클론을 하기 위해서는 공개키와 비밀키를 `$home/.ssh` 부분에 세팅해 줘야 한다.

### known_hosts 파일 세팅하기
- 처음 클론을 시도하면 known_hosts에 파일이 없어서 기록된 허가 주소도 없기 때문에 클론을 시도할 때 나오는 메시지에 yes를 입력해야 한다. 하지만 유저가 아니면 yes를 입력할 방법이 없다. (여러가지로 검색 해 보았지만 불가능 한 것으로 보였음 아마 보안을 위해서 꼭 유저가 입력을 해야하는 방식으로 만든 것 같음)

### userdata 세팅의 문제
- 인스턴스 최초 기동시의 세팅을 리눅스의 root 권한으로 이뤄진다. 권한 문제로 `$home/.ssh` 경로에 파일에 쓰기 권한이 작동하지 않아서 저장이 가능한 다른 경로에 키 파일을 먼저 만들어야 했다.

### 클론준비
- 비트버킷의 경우 공개키가 git clone 하려는 레포지토리의 access key 부분에 등록이 된 공개키-비밀키 쌍이어야 한다.


## userdata에 세팅할 bash 명령
- 다음 명령어들은 userdata에 넣는 bash shell 명령어들이다.
- 태스트를 하고 싶을 때는 직접 입려해서 태스트를 해도 된다. 대신 sudo로 root 권한을 사용해야 한다. 
```
sudo sh test.sh
```
### 공개키 생성
```
echo "creating... -> public key"
sudo sh -c "cat >  /home/ec2-user/id_rsa.pub << 'EOF'
ssh-rsa  공개키 코드
EOF"
```
### 비밀키 생성
```
echo "creating... -> secret key"
sudo sh -c "cat >  /home/ec2-user/id_rsa << 'EOF'
-----BEGIN RSA PRIVATE KEY-----
비밀키 코드
-----END RSA PRIVATE KEY-----
EOF"
```

### known_hosts 생성
```
echo "creating... -> known_hosts"
sudo sh -c "cat >  /home/ec2-user/known_hosts << 'EOF'
|1|코드=|코드= ssh-rsa 코드
|1|코드=|코드= ssh-rsa 코드
EOF"
```

### 만든 키 파일들을 `$home/.ssh`에 배치
```
sudo mv /home/ec2-user/id_rsa /home/ec2-user/.ssh/id_rsa
sudo mv /home/ec2-user/id_rsa.pub /home/ec2-user/.ssh/id_rsa.pub
sudo mv /home/ec2-user/known_hosts /home/ec2-user/.ssh/known_hosts
```


ssh를 통한 git 명령어를 사용하기 위해서는 각 파일에 ec2-user 권한을 줘야 한다.
다음 비트버킷의 설명을 참고 
`You shouldn't use sudo when cloning, pushing, or pulling because the ssh-agent runs on the user level, not the root level. `
Reference : https://support.atlassian.com/bitbucket-cloud/docs/troubleshoot-ssh-issues/

### 소유권 부여
```
chown ec2-user:ec2-user /home/ec2-user/.ssh/id_rsa
chown ec2-user:ec2-user /home/ec2-user/.ssh/id_rsa.pub
chown ec2-user:ec2-user /home/ec2-user/.ssh/known_hosts
```

### 퍼미션 부여
- 공개키와 비밀키는 읽기 권한으로 세팅되지 않으면 작동하지 않는 문제가 있으므로 다름과 같이 세팅한다.
```
chmod 400 /home/ec2-user/.ssh/id_rsa
chmod 400 /home/ec2-user/.ssh/id_rsa.pub
```
id_rsa, id_rsa.pub는 고정 값이므로 읽기 전용만 설정하며, known_hosts는 파일에 추가적으로 타겟 주소가 저장될 수 있기 때문에 `chmod 400`을 세팅하지 않는다.

## root 권한으로 실행하지 못하는 문제 해결 방법
- ec2-user 권한으로 권한 스위칭을 한 후, -c 옵션을 통해 원하는 실행할 명령어를 입력한다.

```
su ec2-user -c "git clone -b branch_name git@bitbucket/userOrGroupName/repository_name.git"
```

