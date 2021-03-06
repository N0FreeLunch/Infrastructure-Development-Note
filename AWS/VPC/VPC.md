# Amazon VPC란?
- Amazon VPC는 Amazon EC2의 네트워킹 계층

## VPC의 핵심 개념
#### Virtual Private Cloud(VPC)
- 비공개 가상 클라우드 : 

#### 서브넷
- VPC의 IP 주소 범위

#### 라우팅 테이블
- 네트워크 트래픽을 전달할 위치를 결정하는 데 사용하는 라우팅이라는 이름의 규칙 집합

#### 인터넷 게이트웨이
- VPC의 리소스와 인터넷 간의 통신을 활성화하기 위해 VPC에 연결하는 게이트웨이

#### CIDR 블록
- 클래스 없는 도메인 간 라우팅. 인터넷 프로토콜 주소 할당 및 라우팅 집계 방법

#### VPC 앤드포인트
- 인터넷 게이트웨이, NAT 디바이스, VPN 연결 또는 AWS Direct Connect 연결을 필요로 하지 않는다.
- PrivateLink 구동 지원 AWS 서비스 및 VPC 엔드포인트 서비스에 VPC를 비공개로 연결할 수 있다. 
- VPC의 인스턴스는 서비스의 리소스와 통신하는데 퍼블릭 IP 주소를 필요로 하지 않는다.
- VPC와 기타 서비스 간의 트래픽은 Amazon 네트워크를 벗어나지 않는다.

## Amazon VPC에 액세스 할 수 있는 방법
#### AWS Management Console
- VPC에 액세스할 때 사용할 수 있는 웹 인터페이스를 제공

#### AWS Command Line Interface(AWS CLI)
- AWS의 다양한 기능을 커멘드라인을 통해 접근 제어할 수 있는 명령을 제공한다.
- Windows, macOS 및 Linux에서 지원

#### AWS SDK
- 언어별 API, 서명 계산, 요청 재시도 처리 및 오류 처리와 같은 많은 연결 세부 정보를 관리

#### 쿼리 API
- HTTPS 요청을 사용하여, 호출하는 하위 수준의 API 작업 제공



