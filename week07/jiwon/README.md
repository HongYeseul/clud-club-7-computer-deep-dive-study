# 1. CPU

- 논리곱, 논리합, 논리 부정 게이트를 이용하여 어떤 논리 함수도 구현할 수 있음
- CPU에는 전문적으로 계산을 담당하는 ALU라는 산술 논리 장치 모듈이 있음

## 하드웨어의 기본 기술 : 기계 명령

- 기계 명령어를 통해 CPU에 명령을 내릴 수 있음
  - 기계 명령어는 조합 회로를 이용하여 실행됨
  - CPU는 연산을 하며, 피 연산자는 사용자가 제공함

### 소프트웨어와 하드웨어 간 인터페이스 : 명령어 집합

- 명렁어 집합 : CPU가 실행할 수 있는 명령어 + 각 명령어에 필요한 피연산자
- 서로 다른 CPU는 서로 다른 명령어 집합을 지님
  - 하지만 사람이 직접 기계 명령어를 프로그래밍 하기는 어렵기에, 고급 프로그래밍 언어를 사용할 수 있도록 **컴파일러**가 등장함
- 클럭 신호는 Cpu의 전체 부붐이 동작할 수 있도록 명령한다.
- 클럭 주파수(1초에 몇 번 동안 지휘를 하는 가)가 높을수록 CPU는 더 많은 작업을 하게 되지만, 이로 인해 발열 등 성능 저하가 발생할 수 있다.

## 2. CPU의 유휴 상태

- 프로그램이 메모리에서 실행되면 프로세스의 형태로 존재하고, 이를 운영체제가 관리함
- 운영체제는 프로세스에 우선순위를 할당하고, 우선순위에 따라 스케줄러가 스케줄링 할 수 있도록 대기열에 프로세스를 넣음

### 대기열 상태

- 준비 완료 대기열이 비어 있음 → 현재 운영체제가 스케줄링해야 하는 프로세스가 없고, CPU가 유휴상태임
- 커널에서 스케줄링을 관리할 때는 이미 복잡하기 때문에 조건문으로 처리하기에는 번잡함 → 스케줄러 대기열에서 항상 실행할 수 있는 프로세스를 찾도록 함
  - 그리고 이것이 유휴 작업이라는 프로세스임
    - 항상 준비 완료 상태이며 우선 순위가 가장 낮음
  - null 판단 로직을 제거하여 코드 오류 가능성을 줄임
- halt 명령어는 모든 모듈을 절전 상태로 전환하여 전력 소비를 크게 줄임
  - 시스템 내 더 이상 실행 준비가 완료된 프로세스가 없다는 것
  - 특권 명령어라 커널 상태에서 CPU로만 실행할 수 있음
  - 프로그래머가 작성한 응용 프로그램은 사용자 상태에서 실행되므로 불가함
- sleep은 해당 함수를 호출한 프로세스만 일시 중지함
  - 다른 프로세스는 여전히 실행 상태라면 cpu는 유휴 상태로 진입할 수 없음
- 어떤 유형의 시스템 유휴 시간을 예측하여 다양한 수면 상태를 지닐 수 있음

### 무한 순환 탈출 : 인터럽트

- 운영 체제는 일정 시간마다 타이머 인터럽트를 생성하고, cpu는 인터럽트 신호를 감지하여 운영 체제 내부의 인터럽트 처리 프로그램을 실행함

## 유휴상태를 사용하는 이유

## 3. CPU의 숫자 인식 방법

- 컴퓨터가 2진법인 이유는 컴퓨터 저수준 계층이 끄고 켜는 스위치인 트랜지스터로 구성되어 있기 때문
- 양수와 음수는 최상위 비트로 구분(0 - 양수, 1 - 음수)
- 0의 보수는 2진수 숫자 앞에 최상위 비트를 붙여서 양,음수를 구분함
  - -0이 존재함
- 1의 보수는 2진수를 완전히 반전시킴(0000 → 1111)
  - 여전히 -0이 존재
- **0의 보수와, 1의 보수는 연산 과정에서 문제가 발생함**
  - 따라서 정확한 연산을 보장하는 조합 회로의 추가가 불가피함

### 컴퓨터 친화적 표현 방식 : 2의 보수

- 2의 보수에서 해당 숫자의 양수와 음수의 자릿수를 합하면 0의 2진수 값이 됨
- 2의 보수는 1의 보수를 한 값에 1을 더하면 구할 수 있음
- 2의 보수는 사람에게는 직관적이지 않지만 **회로 설계를 단순화할 수 있음**

## 4. CPU가 if 문을 만났을 때

### 파이프라인

- CPU가 기계 명령어를 실행하기 위해서는 여러 과정이 필요함
  - 명령어 인출 → 명령어 해동 → 실행 → 다시 쓰기
  - 각 단계는 별도의 하드웨어로 처리됨
- 프로그래머가 작성한 if문은 일반적으로 컴파일러가 조건부 점프 명령어로 변환하며, 이 명령어는 분기 역할을 함
  - 참이면 점프, 아니면 순차적 실행
- 분기 점프 명령어 실행 전에 다음 명령어가 위치해 있어야 함
  - 근데 점프 할 수도 있고 순차적 실행을 할 수도 있음
  - 미리 위치해 있기 위해 **예측**이 필요함
- 추측이 틀리면 잘못된 분기 명령어 전부를 무효화 하기에 성능 손실이 발생함
- 그래서 정렬되어있는 배열에서의 if 작업의 추측이 더 잘 맞게 되는 것이고 프로그램 성능이 높아지게 됨
  - 하지만 최신 성능에서는 불규칙 배열의 예측 실패율이 훨씬 낮아지긴 했음

## 5. CPU 코어 수와 스레드 수 사이의 관계

- cpu의 코어 수는 작업자의 수를 의미한다.
- 스레드 수는 일정 시간동안 실행할 수 있는 일의 수를 의미한다.
- cpu 코어 수와 스레드 수 사이에는 어떤 필연 관계도 없다.(cpu는 하드웨어, 스레드는 sw)
  - 단일 코어 시스템에서도 여러 스레드 생성 가능
- cpu는 자신이 실행하는 명령어가 어느 스레드에 속하는지 이해하지 못하며 이해할 필요도 없음
  - 이는 운영 체제게 이해해야 함
- cpu는 레지스터 주소에 따라 메모리에서 기계 명령어를 꺼내 실행함

### 작업 분할과 블로킹 입출력

- 단일 코어 시스템에서 cpu는 일정 시간 동안 단 하나의 스레드만 실행할 수 있기에 다중 스레드 환경이어도 진정한 병렬 처리는 아님
- 하지만 의미 없는 것은 아니고 작업을 분류한다는 것에 의미가 있음
  - 작업을 전환할 때 기다리지 않고 바로 실행할 수 있음

### 다중 코어와 다중 스레드

- 다중 프로세스도 다중 코어를 활용할 수 있지만 프로세스간 통신 방식이나, 프로세스 간 전환에 드는 비용 문제가 있음
- 다중 코어를 최대한 활용하려면 시스템에 코어가 몇 개 있는지 알고, 일반적으로 스레드 수를 코어 수와 일정한 선형 관계를 유지하는 것이 좋음
  - 스레드가 많다고 좋은 것은 아님
- 스레드가 단순히 계산을 위한 것이고 입출력, 동기화 작업이 없음 → 1개가 적정
- 일정한 입출력과 동기화가 필요할수록 스레드를 적당히 늘리는 것이 시스템 성능에 좋음
  - “적당”한 것은 수치화하기 어렵고, 실제로 프로그램을 사용하며 상황에 따라 지속적으로 테스트해야 할 수 있음
- 하지만 스레드 수가 과도해지면 스레드 전환 부담으로 운영 체제 성능이 저하됨
- 개발하는 어플리케이션의 동작이 순차적으로 실행돼야만 하는 특징을 가지거나 잘게 쪼개서 동시에 실행하기에 매우 어려운 성격의 어플리케이션이라면 오히려 멀티 스레드 구성은 별 이점이 없다

<예시>

- CPU 연산 능력에 의존하는 작업
  - 오라클에서 아키텍트로 일하고 있는 Goetz가 2002, 2006년에 발표한 자바 병렬 프로그래밍에 관한 책과 논문에 따르면 CPU 바운드 작업에서 적절한 스레드 수는 코어 수 + 1 이라고 함
- I/O 장치의 응답에 의존하는 작업
  - I/O 작업이 많은 상황에서는 대기 시간을 줄이기 위해CPU 코어 수보다 2배 3배 혹은 그 이상으로 스레드 수를 늘려주는 것이 코어들을 더 효율적으로 쓸 수 있음

[참고 자료](https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-Is-more-threads-always-better)