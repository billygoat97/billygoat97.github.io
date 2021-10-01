---
title: "운영체제(OS) 04장 Threads"
except: "운영체제(OS) 에서 Processor의 처리를 획기적으로 빠르게 하는 Thread에 대해 알아보자"
categories:
  - 운영체제
tags:
  - Threads
  - Thread
  - Multithreading
toc: true
toc_sticky: true
---

# 4장 Threads (스레드)
## <span style = "color:powderblue"> ※ 스레드란 무엇인가?
### <span style = "color:powderblue"> ※ 프로세스 모델의 한계
- fork를 해서 복제 in order to 다수의 simultaneous task를 하기 위해
이러한 것은 자원을 공유할 때에는 비효율!
- 멀티 프로세싱;
전통적인 프로세스들은 멀티 프로세서의 장점을 얻지 못한다. 왜냐면 한번에 하나의 프로세서만 이용하기 때문!
<br><br><br>

### <span style = "color:powderblue"> ※ 스레드 모델!
- 프로세스 모델은 무겁다.. so heavy...
- 프로세스는 이미지와 컨텍스트를 포함한다. 그리고 새로 프로세스 생성하는건 너무 비싸다 
<br><br><br>

### <span style = "color:powderblue"> ※ 멀티스레드 모델은: 
- 스레드는 프로세스 instruction을 share하고 데이터도 공유함
- 스레드는 os process state 도 공유함 (5 state) -> 3장 참조
<br><br><br>

### <span style = "color:powderblue"> ※ 멀티 스레드가 있는 프로세스
- 스레드는 각자의 logical control flow가 있다.
- 스레드는 코드 데이터, 커널 컨텍스트를 공유함
- 스레드는 고유 tid 있음.

![image](/images/OS-04-01.png)

![image](/images/OS-04-02.png)

- 그림을 보면, 스레드가 있는 것은 다 같은데 stack이 스레드별로 할당됨!

![image](/images/OS-04-03.png)
<br><br><br>

### <span style = "color:powderblue"> ※ 프로세스와 스레드의 비교
- fork를 써서 request마다 새 프로세스를 만드느냐,
- 아니면 thread_fork를 써서 request마다 스레드 생성을 하냐 의 차이
- 이제 프로세스는 thread들의 container이라고 보는 것이 좋다. 즉, 프로세스 안에 다량의 스레드가 존재한다.
- 즉, 프로세스는 정적, 스레드는 동적으로 변함
<br><br><br>

### <span style = "color:powderblue"> ※ 스레드와 프로세스의 공통점. 
- 각각의 논리 control flow가 있다.
- 각각 스케줄링이 별개로 된다.
<br><br><br>

### <span style = "color:powderblue"> ※ 스레드와 프로세스가 어떻게 다른가
- 스레드는 코드와 데이터까지 공유, 프로세스는 안그럼
- 스레드가 더 생성에 저렴, 스위칭이랑 삭제까지!
<br><br><br>

### <span style = "color:powderblue"> ※ 스레드의 장점:
- 비용이 저렴함. 시간도 적음!
- 자원을 공유하기 때문에 enhanced communication이 가능
- kernel을 야기하기 않아도 됨
- 멀티프로세서에도 잘 이용가능

![image](/images/OS-04-04.png)
<br><br><br>

### <span style = "color:powderblue"> **** 중요한 부분 (kernel level thread와 user level thread의 차이) ****
- 커널 레벨 스레드: 스레드 생성과 관리가 커널에 의해 관리되기 때문, 시스템 콜을 요구함
- 유저 레벨 스레드: 프로그램에 연결되어 있는 라이브러리가 스레드를 관리한다
- 어떻게 유저 레벨 스레드가 관리가  가능한가?
  - 스레드는 같은 주소 공간 이용
  - 스레드는 하드웨어만 differ함.
<br><br><br>

### <span style = "color:powderblue"> ※ 유저 레벨 스레딩(유저 레벨 스레드 라이브러리 이용)
- 커널은 스레드에 딱히 관심 x
- 라이브러리 패키지는 생성, 동기화, 스케줄링, 관리를 커널 필요없이 가능
- 커널 스레드를 지원하지 않는 시스템에서 이용함.
- 유저 레벨 스레드는 다대일 모델
- 즉, 하나의 커널 스레드(프로세스)에 많은 유저 레벨 스레들이 mapped 됨
- 장점: 매우 가벼움
- 단점: I/o thread가 프로세스가 막을 수 있음. 멀티 프로세스에서는 딱히 이득이 없음.

![image](/images/OS-04-05.png)
<br><br><br>

### <span style = "color:powderblue"> ※ 커널 레벨 스레드
- 커널에 의해 지원됨
- 커널에 의해 생성 삭제됨
- 커널 텍스트와 전역 데이터를 공유함
- 자기만의 커널 스택 있음
- 각각이 별도로 스케줄됨
- 동기화 기법을 이용함
- 일대일 모델임
- 장점: 많은 cpu별로 스레드가 동일하게 run하게 함
- 시스템 콜 때문에 멈춰도 다른 스레드 움직이게 가능
- 단점: 너무 무거움

![image](/images/OS-04-06.png)

- 커널 레벨 스레딩이 다대다 모델인 경우도 있음
  - 장점: 유저 공간에서 스레드 생성 가능
  - 스레드가 다른 cpu에서도 동작 가능
<br><br><br>

### <span style = "color:powderblue"> ※ 스레드 interface:
Pthreads
- Thread 생성 동기화를 위한 POSIX 표준 API
- API는 스레드 라이브러리의 동작 지정
- 유닉스 기반 OS에서 쓰며, 최근 멀티스레딩 APP = Pthread 라이브러리
<br><br><br>

### <span style = "color:powderblue"> ※ 리눅스 스레드;
- 리눅스는 lightweight processes를 제공
- 프로세스와 스레드 간에 차이를 두지 않음
- clone을 이용
- pthread 이용
<br><br><br>

### <span style = "color:powderblue"> ※ 컨텍스트 전환
- 컨텍스트 전환은 다른 프로세스의 수행을 위해 실행되고 있는 현재 상태를 저장하는 절차
- 실행되고 있던 프로세스의 내용을 제어 블러에 저장하고 다른 프로세스의 컨텍스트를 그 프로세스의 제어 블록으로부터 불러와 실행 (이 과정이 일어날때에는 아무 일도 일어나지 않음 = overhead)
<br><br><br>

## <span style = "color:powderblue"> ※ 요약
- 스레드는 가볍다 저렴하다
- 그러나 동기화 문제가 있다. (멀티 프로세스는 시스템 자원을 많이 사용하고 프로세스간 통신이 어려운 반면 하나의 프로세스에 문제가 생겨도 확산 X, 그러나 멀티스레드는 시스템 자원을 적게 소모하고 스레드간 통신은 쉽지만, 공유 메모리의 동기화가 문제가 됨)
- 스레드는 코드 데이터 힙을 공유하나 스택은 스레드별로 할당된다.
- 프로세스; 실행중인 프로그램
- 스레드; 프로세스의 실행 단위
<br><br><br>
<br><br><br>


# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . 프로세스와 스레드의 특징, 장단점 비교?** </span>
프로세스는 실행중인 프로그램이고, 스레드는 프로세스의 실행 단위이다. 프로세스는 여러 작업을 동시에 실행하기 위해 fork()를 통해 복제하는 방식을 거쳐 code, data, 힙 스택 자체를 복제를 하지만, 스레드는 멀티 작업을 하기 위해 스택만을 복제하고 나머지 code data heap을 공유하여 이용한다. 프로세스는 각각이 독립적이기 때문에 IO operation과 같은 문제가 생기더라도 다른 프로세스들이 block되는 현상이 있지 않다. 그러나, 모든 정보를 복제하기 때문에 시간 소요가 다소 많이 된다고 할 수 있다. 스레드는 하나의 프로세스 내부에 존재하기 때문에 IO operation이 발생하여 프로세스가 blocked된다면, 내부 스레드는 모두 멈춘다는 단점이 있지만, 복제하거나 서로 교환을 한다는 점에서 멀티프로세스에 비해 비용이 저렴하다는 장점이 있다.

## <span style = "color:Orange"> **Q2 . 커널 레벨 스레딩과 유저 레벨 스레딩의 특징과 장단점 비교?** </span>
커널 레벨 스레드는 커널이 스레드를 관리하기 때문에 커널에 종속적이다. 커널 레벨 스레드는 스레드들을 몇몇 프로세서에 한번에 디스패치 할 수 있기 때문에 멀티프로세서 환경에서 빠르게 동작하며, 커널이 각 스레드를 개별적으로 관리 가능하다. 그러나 스케줄링의 동기화를 위해 커널을 호출하는데 시간이 오래 걸리며, 모드 변화로 인해 성능 저하가 발생한다. 유저 레벨 스레드는 사용자 영역에서 스레드 연산을 하여 라이브러리 활용한다는 점이 있다. 유저 레벨 스레드는 운영체제에서 지원할 필요가 없으며, 모드 변화가 없기 때문에 성능이 좋다. 그러나 스케줄링 우선순위를 지원하지 않아 어떤 스레드가 선제적으로 동작하는지 모르며, IO operation에 의해 block이 되면 전체 스레드가 정지된다는 단점이 있다.