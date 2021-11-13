## AWS fargate
- Reference : https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/userguide/what-is-fargate.html
- AWS에서 컨테이너 이미지 그대로를 인스턴스화 하는 기능을 제공

### fargate의 장점
- EC2 인스턴스의 서버나 클러스터를 관리할 필요가 없다.
- 컨테이너를 실행하기 위해 가상 머신의 클러스터를 프로비저닝, 구성 또는 조정할 필요가 없다.
- 서버 유형을 선택하거나, 클러스터를 조정할 시점을 결정하거나, 클러스터 패킹을 최적화할 필요가 없다.

### fargate의 생성 과정
1. 애플리케이션을 컨테이너에 패키징하기
2. CPU 및 메모리 요구 사항을 지정하기
3. 네트워킹 및 IAM 정책을 정의하기
4. 애플리케이션을 시작

### fargate의 특징
- 자체 격리 경계가 있어서 다른 작업과 기본 커널, CPU 리소스, 메모리 리소스 또는 탄력적 네트워크 인터페이스를 공유하지 않음.

---

## 요금
- Reference : https://aws.amazon.com/ko/fargate/pricing/

---
## IAM 설정
- Reference : https://docs.aws.amazon.com/ko_kr/AmazonECS/latest/userguide/get-set-up-for-amazon-ecs.html
- | 표시는 메뉴, [] 표시는 버튼을 의미

1. IAM 페이지에 접속
2. |사용자| -> [사용자 추가]

---

## Docker
- 도커란? Linux 컨테이너를 기반으로 하는 분산 애플리케이션을 빌드, 실행, 테스트 및 배포할 수 있도록 해주는 기술

### Docker를 ECS에 연결
- Amazon ECS는 작업 정의에서 Docker 이미지를 사용하여 클러스터의 작업의 일부로 컨테이너를 시작한다.

### ECS에 디플로이 과정
1. Docker Desktop 및 Docker Compose를 사용하여 컨테이너를 로컬로 빌드하고 테스트
2. Amazon ECS및 Docker 통합에서 Docker 데스크톱을 다운로드하고 선택적으로 Docker ID에 등록

### ECS에 도커 컨테이너 배포
- Reference : https://docs.docker.com/cloud/ecs-integration/

### ECR에 도커 이미지 등록
- ECR은 관리형 AWS 도커 레지스트리 서비스
- 도커 CLI를 사용하여 이미지를 푸시, 가져오기 및 관리할 수 있다.

### ECR에 도커 이미지 푸시
1. AWS CLI 설치 및 구성
2. ECR 저장소 생성
```bash
aws ecr create-repository --repository-name ECR_리포지토리_이름 --region 생성할_리전_지정
```
3. 생성한 ECR 저장소에 도커 이미지 추가
```bash
docker tag 이미지_이름 aws_account_id.dkr.ecr.region.amazonaws.com/ECR_리포지토리_이름
```
4. [옵션] ECR을 통한 도커 버전관리를 위해 인증하기
```
aws ecr get-login-password | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com
```
- IAM 사용자는 Amazon ECR API를 호출할 수 있고 프라이빗 리포지토리로부터 이미지를 푸시하거나 가져올 수 있는 권한이 필요하다.
- 프라이빗 레지스트리에 대해 Docker 클라이언트를 인증하면 docker push 및 docker pull 명령을 사용하여 해당 레지스트리의 리포지토리에 이미지를 푸시하고 가져올 수 있다. ECR을 사용하여 이미지에 대한 버전관리를 하려면 ECR에 대한 프라이빗 인증을 한다.
5. [옵션] 이미지를 업로드 할 때 ECR을 사용해야 하는데, ECR은 저장 비용을 청구한다. 이미지를 저장할 필요가 없다면 저장 비용을 빼기 위해 다음 명령을 사용하여 사용한 ECR 저장소를 지운다.
```
aws ecr delete-repository --repository-name ECR_리포지토리_이름 --region region --force
```
