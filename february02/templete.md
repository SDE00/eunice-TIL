# Today - 2025-02-11 (Tue)

## Scrub
- 어제 학습한 내용 50% 복습 -> 특강 내용 복습으로 대체
- Weekly Mission 마무리

## Learned
- Docker와 krampoline 배포의 방식


## 로컬 기반 배포
- local 기반 → 각종 요구 사항(OS 및 패키지 버전)을 모두 맞춰줘야 함
- 1 서버 1 서비스 배포가 이뤄지는 것이 아니기 때문에 요구사항으로 인해 변경 후
→ 개발 환경 변경으로 동작한던 **다른 애플리케이션**에서 **버전 충돌 이슈 발생** 위험

**서버에 종속적**이라는 단점을 보완하기 위해 → VM 기반 배포 방식 등장

## VM 기반 배포 활용 (격리된 환경 구축)
- **VM(Virtual Machine)** 자체가 너무 무거움
- **Hypervisor** 설치 필요
- 1 service 1 VM 한정 → 확장성 ↓

→ 해결을 위해 container 기반의 배포 방식 등장


### 왜? VM(Virtual Machine)은 더 무거운 걸까?
- **하드웨어 가상화(Hardware Virtualization)** 기반함
- Virtual Machine은 자신만의(Guest OS)를 포함 → 각 OS가 따로 오버헤드가 큼

|  | **VM(Virtual Machine)** | **Container** |
| --- | --- | --- |
| 가상화 방법 | **하드웨어 가상화(Hardware Virtualization)** | **OS Virtualization** 기반→ 어플리케이션 단위 실행 |
| OS 포함 여부 | 포함 | 미포함 |
| 자원 관리 | **Hypervisor** | **Docker Engine**가 Host OS 커널 공유 |


## Container 기반 배포
하나의 OS에 여러 개의 container가 올라가는 구조
- 오버헤드가 적음

### 장점

1. 각각의 서비스르 격리된 환경에서 배포
2. VM에서 더 가볍게 사용이 가능
3. 유연한 확장이 가능


### Container, 컨테이너의 **유연한 확장**이 가능한 이유?
⇒ 
1. **Immutable (불변)한 이미지 구조**

- 컨테이너는 **Docker 이미지**를 기반으로 실행되므로, 언제든지 삭제 후 다시 생성 가능.
- 실행 중인 컨테이너를 삭제해도 **원본 이미지에는 영향 없음**.
1. **OS가 아닌 애플리케이션 단위로 관리**
    - 컨테이너는 **Host OS의 커널을 공유**하므로, VM처럼 OS를 다시 설치할 필요 없음.
    - 따라서 컨테이너 생성/삭제가 훨씬 빠름.
2. **Stateless (무상태성) 디자인**
    - 컨테이너 자체에 상태(데이터)를 저장하지 않음.
    - 모든 데이터는 **볼륨(Volume)** 을 사용하여 별도로 관리.
3. **Docker는 간편한 컨테이너 관리 명령어 제공**
    - 중지: `docker stop 컨테이너_ID`
    - 삭제: `docker rm 컨테이너_ID`


### Kubernetes, 쿠버네티스
- **컨테이너 애플리케이션을 자동으로 배포하고 관리하는 오픈소스 플랫폼**
- **리소스를 절약하고 장애에 대응 가능**
- **온프레미스(On-Premise) 환경**에서도 실행 → 즉, 클라우드뿐만 아니라 자체 서버에서도 운영 가능

**주요 기능**

- **Auto Scaling (자동 확장)**
    - 트래픽이 많아지면 **자동으로 컨테이너 개수를 늘리고**, 줄어들면 다시 감소.
    - **자원 낭비 없이 최적화된 운영 가능**.
- **Auto Healing (자동 복구)**
    - 컨테이너(Pod) 장애 발생 시 **자동으로 새로운 컨테이너를 생성**하여 복구.
    - 하지만 **서버 자체(노드) 문제**라면 직접 교체(replace) 필요.
    - 예: 전체적으로 12개의 Pod가 필요하다면, 장애가 발생해도 12개를 유지하도록 자동 복구됨.

**역할**

1. container는 유연한 확장을 가능하게 한다.
2. 쿠버네티스는 container를 활용하는 다양한 배포 및 운영 기법을 자동화

→ container의 유연한 특징을 최대한 활용해서 resource를 절약하고 서비스를 안정적으로 운영 가능

**기본 개념**

| 개념 | 설명 | 비고 |
| --- | --- | --- |
| **클러스터(Cluster)** | 여러 개의 노드를 묶어 하나처럼 관리하는 구조 |  |
| **노드(Node)** | 컨테이너를 실행하는 개별 서버 (물리/가상 서버) |  |
| **네임스페이스(Namespace)** | 여러 개의 환경을 분리할 수 있도록 제공 | 팀별, 프로젝트별 분리 가능 |

| 개념 | 설명 | 비고 |
| --- | --- | --- |
| 파드(Pod) | 쿠버네티스이 가장 작은 컴퓨팅 단위 | pod의 container안에서 실제 서비스 실행 |
| 레플리카셋 (ReplicaSet) | 다수의 Pod를 관리 → AutoScaler와 함께 사용하며 복제본 개수를 자동 조절 | 자원 관리( 트래픽에 따른pod 개수 조정), 장애 대응 |
| 디플로이먼트(Deployment) | 파드를 새로운 버전으로  업데이트, 다양한 배포 방식 지원| Recreate → → 다운타임이 존재 (잘 사용하지 않음) RollingUpdate를 사용 |
- **Recreate** (중단 있음)
    - 기존 Pod를 종료한 후 새로운 Pod 실행.
    - 단점: **중간에 서비스가 다운됨**.
    - 일반적으로 **테스트 환경에서만 사용**.
- **Rolling Update** (기본 배포 방식)
    - 기존 Pod를 하나씩 종료하면서, 새로운 Pod를 점진적으로 배포.
    - **무중단 배포 가능**.
    - **"카나리 배포"** 같은 전략 활용 가능.

readinessProbe - pod의 상태를 보여준다. 이후 커스터마이징 후 값을 잘 넘겨줘야 한다.

Rollout : in / out을 적절히 반복해서 사용

**서비스(Service)**

Pod는 계속 생성되고 삭제되기 때문에 **IP가 계속 바뀜**.

이 문제를 해결하기 위해 **Service가 Pod에 고정된 IP를 제공**해.

📌 **Service 역할**

- **Pod들이 그룹으로 묶여서 하나의 네트워크 서비스처럼 동작**.
- **고정된 IP 제공** (Pod가 삭제/생성되어도 변하지 않음).
- **로드 밸런싱 지원** → 여러 개의 Pod로 트래픽을 분산 가능.

### 전체적인 개략도

**Ingress**

- **외부에서 쿠버네티스 내부 서비스로 접속할 수 있도록 라우팅**하는 역할
- 트래픽을 적절한 서비스 객체에 분배하여 **로드 밸런싱 가능**
- Proxy, 프록시 서버에 연결해서 사용

**IaC, Infrastructure as Code**

- 모든 쿠버네티스이 오브젝트들은 코드(YAML)로 작성된다.
→ 필요하면 코드를 가져오면 돼서 쉽게 구축 가능 / 유지보수 편의성

## Keep
- Create & Setup TIL Repository 좋은 사례 계속 찾아보기
- docker 및 backend 기본 개념 복습
- Colab Pro+ & ChatGPT+ 자료조사

## Problem
- 실습 중 Git push가 안되는 오류 발생 (해결)
->  1. 인증 토근 방급 받아서 추가
    2. 컨테이너 작제 후 다시 생성

## Try
- 실습 진행 후 모르는 사항 다음 특강 Q&A로 질문 list 만들어 둠.

## 참고 자료
- ([krampoline Cloud 활용 링크](https://krampoline-help.goorm.io/ide/kakao-cloud/basic-how-to))
