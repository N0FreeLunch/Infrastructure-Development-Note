## 노드 JS 설치 코드
- Reference : https://stackoverflow.com/questions/36399848/install-node-in-dockerfile

#### root 권한으로 NVM, NODE 실행
```dockerfile
ENV NODE_VERSION=16.13.0
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version
```
- 현재 NodeJS 버전을 입력 해 준다.
- 최신 NVM 버전을 입력 해 준다. (`nvm/v0.34.0/install.sh` 부분, `https://github.com/nvm-sh/nvm`에서 최신 NVM 버전을 확인한다.)
- 종속성 패키지를 인스톨 할 때는 root 권한을 사용하는 것을 권장하지 않는다. 패키지 인스톨이 될 때 패키지 내의 코드를 실행할 수 있고 root 권한으로 패키지 내의 코드가 실행될 수 있기 때문이다. 이로 인해서 서버의 제어권 및 프로젝트 폴더의 모든 파일 및 폴더에 대한 조작 권한을 인스톨 될 패키지에게 주게된다.

#### USER 권한으로 NVM, NODE 실행
```dockerfile
ENV NODE_VERSION=16.13.0
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
ENV NVM_DIR=/home/유저_네임/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/home/유저_네임/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version
```
- 특정 리눅스 유저로 nvm, node 명령어를 실행하기 위한 설치 방법이다.
- 설치하기에 앞서 리눅스 유저를 만들어야 하며, 해당 리눅스 유저는 홈 디렉토리가 생성되는 옵션인 \-m 옵션을 붙여서 만들어 줘야 한다. (linux의 useradd 명령어를 통해 유저 생성할 때)

## nodeJS로 빌드하기
- `npm start` 또는 라라벨 mix의 `npm run watch`를 사용할 때 작업이 끝나지 않고 프로세스가 계속 실행되어 버리기 때문에 도커의 CMD 명령의 실행이 멈춰 버린다. 따라서 도커에서 이런 watch 모드를 실행하기 위해서는 별도의 프로세스로 분리해서 실행하는 것이 필요하며, 하나의 프로세스로 켜지며 외부에서 컨트롤 되어야 한다. 따라서 PM2를 사용해서 컨트롤 하는 방식을 사용한다.
- 로컬에서는 `npm run watch` 명령을 실행해야 하며 태스트 서버나 프로덕션 서버에서는 `npm run production`을 실행해야 한다.
- `npm run watch`는 서버가 켜질 때 항상 실행되어야 하며, `npm run production`는 서버가 빌드 될 때 1회 실행되어야 한다.
- 노드 JS를 빌드할 때 여러 문제가 발생할 수 있다. `npm run watch`가 컨테이너에서 실행될 때 여러가지 중단 문제를 야기한다. 호스트라면 이런 처리가 상대적으로 낫지만 컨테이너 내부에서 일어나는 동작의 문제는 상대적으로 관리하기 어렵다. 또한 에러가 발생했을 때 다시 실행되는 속도 및 빌드 속도가 굉장히 느린 문제가 발생하므로 이런 문제를 최소화하기 위해서 호스트 환경에서 번들러를 실행하도록 한다.

## PM2
- https://pm2.keymetrics.io/docs/usage/quick-start/
- 로컬에서  도커에서 명령을 실행할 폴더로 가서 `pm2 start "npm run watch"`을 실행한다.
- watch 모드를 실행하지 않는다면 굳이 pm2를 통해서 관리할 필요가 없다.
- 로컬 환경에서 도커 기동 시 webpack의 로그를 확인하고 싶다면 `docker-compose exec target_docker_name pm2 logs`를 실행해서 도커에서 실행되고 있는 webpack의 watch 모드의  확인할 수 있다.

### watch 모드 시 에러 해결
- node_modules 폴더에 실행 권한을 부여하지 않으면 watch 모드가 동작하지 못하고 권한 에러를 뱉을 수 있다.
```
RUN chmod a+x node_modules
```
- 프로덕션 서버에서는 빌드된 파일을 사용하기 때문에 node_modules의 종속성 패키지를 직접 실행할 필요가 없다. 따라서 서버 환경에서는 실행 권한을 주지 않도록 한다.
- node_modules 폴더에 실행 권한을 주는 것은 로컬 환경에서만 권한을 주도록 하자.

## 설치 및 실행 시 리눅스 권한 바꾸기
- `npm install`을 하거나 `npm run ...` 명령어를 사용할 때, root 유저로 실행하는 것은 실행되는 패키지에 악성 코드가 있을 경우 보안 문제가 발생할 수 있다. 따라서 가능하면 user 계정으로 리눅스 권한을 바꾼 뒤에 npm 명령을 실행하는 방법을 사용해야 한다.

