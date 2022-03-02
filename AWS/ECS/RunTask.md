## Task란 무엇인가?
- 하드웨어 위에 소프트웨어를 구동할 수 있는 컨테이너를 올려서 개발한다.
- AWS의 ESC 환경에서 테스크란 ESC 환경에 도커와 같은 컨테이너를 올려서 실행하는 것을 의미한다.

## Task 정의하기
### Task를 정의해야 하는 이유
- Task란? 하드웨어 위에 소프트웨어를 구동할 수 있는 환경을 올리는 것이다.

### Task를 정의하기 위해 고려해야 할 것
- 컨테이너에 올릴 도커 이미지
- 컨테이너를 올리기 위한 하드웨어의 CPU 선정 CPU 개수 메모리 양
- 도커의 네트워킹 모드
- 작업에 사용할 로깅 구성
- 컨테이너가 완료되거나 실패하는 경우 작업을 계속 실행해야 하는지 여부
- 컨테이너가 시작될 때 실행해야 하는 명령
- 작업의 컨테이너와 함께 사용해야 하는 모든 데이터 볼륨
- 작업에서 사용해야 하는 IAM 역할

### 정의 방법
- https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html


## 배치작업이란?
- 비대화형 작업처리를 의미한다.
- 배치 작업의 한 종류로 스케쥴러가 있다. 스케쥴러는 배치형태로 실행될 수 있으며, 배치작업이 모두 스케쥴러인 것은 아니다.



### Reference
- https://docs.aws.amazon.com/cli/latest/reference/ecs/run-task.html
