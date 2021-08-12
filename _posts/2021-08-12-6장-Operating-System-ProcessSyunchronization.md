---
title: "운영체제(OS) 6장 ProcessSynchronization"
except: "운영체제(OS) 에서 Thread로 인해 프로세스의 동기화 이슈가 있는 경우를 알아보자"
categories:
  - 운영체제
tags:
  - Synchronization
  - Semaphore
  - Mutex
  - Monitor
  - Lock
  - Mutual Exclusion
  - Critical Section
toc: true
toc_sticky: true
---

# 6장 Process Synchronization
## <span style = "color:powderblue"> ※ 프로세스 동기화
### <span style = "color:powderblue"> ※ 동기화 이슈가 생기는 이유?
- 생기는 이유는? 오직 멀티스레딩 때문에
- 스레드는 자원을 공유한다는 점이 있기 때문에 동기화 이슈가 있다~
- 즉! 두 개 이상의 프로세스(스레드)는 공유 자원을 동기화 없이 이용하는 경우에 “Race condition”이 생성된다. (결과는 접근 timing에 의해 결정된다) =>따라서 동기화 기법이 필요하다
<br><br><br>

### <span style = "color:powderblue"> ※ 공유 자원
- 프로세스간 ; shared memory objects, files
- 스레드 간; 지역변수 x(자기만의 스택 有), 전역변수(static data segment), 동적 객체(힙)
<br><br><br>

### <span style = "color:powderblue"> ※  Critical section; 임계 구역
- 여러 프로세스들이 공유 데이터를 이용하는 것!
- 즉 한 프로세스가 임계 구역을 executing 하는 중이면 다른 프로세스의 접근 불가
<br><br><br>

### <span style = "color:lime"> ※ 임계구역의 solution을 위한 requirements;
1. mutual exclusion(상호 배제)
만약에 임계구역에 프로세스가 execute하고 있으면 다른 프로세스들은 executing하면 안됨!

2. Progress
만약에 임계구역에 아무 프로세스가 없고, 몇몇 프로세스가 그 임계 구역에 접근하고 싶다면 임계 구역에 접근하는 프로세스 선별을 계속 미루면 안된다!= >쉽게 말해 cs 안에 프로세스가 없으면 진입 가능해야함

3. Bounded waiting/ no starvation
프로세스가 임계 구역 접근을 요청한 후 해당 요청이 승인되기 전에 다른 프로세스가 임계구역에 들어갈 수 있는 횟수에 대한 bound가 존재해야 한다. => 프로세스 cs 진입이 유한 시간 내에 허용되어야 한다.

<br><br><br>

### <span style = "color:powderblue"> ※ 솔루션 분석 프레임워크
Assumption
- 프로세스 실행 속도는 0은 아니지만, 상대적인 속도가 동일하다고 생각
- cpu가 여러개 있어도 메모리 하드웨어는 동일한 메모리 위치에 동시 접근 x
- interleaving 실행 순서 가정 x
- 우리는 entry, exit 섹션을 specify해야한다.
- 임계구역에 대한 해결책; classification
<br><br><br>

### <span style = "color:powderblue"> ※ 낮은 레벨의 동기화 ; Locks
- 매우 원시적인 것.
- 소프트웨어 용도의 솔루션 ; spinlock
- (데커 알고리즘, 피터슨 알고리즘)
- 하드웨어 기본 용도의 솔루션; spinlock
- (test and set, swap instruction)
- 인터럽트 무효화 (context switch 방지)
<br><br><br>

### <span style = "color:powderblue"> ※ 고레벨의 동기화;
- 운영체제 해결책
- function과 data structure 제공 (세마포어, 모니터)
<br><br><br>

### <span style = "color:powderblue">※ Locks
- locks는 낮은 단계의 동기화 기법이다
2가지 방법이 있는데 (같은거임)
- acquire()/lock(): lock이 풀릴때까지 기다리고 grab한다
- release()/unlock(): unlock하고, acquire하는 프로세스(thread)를 깨운다
<br><br><br>

### <span style = "color:powderblue">※ lock을 쓰느법
- lock은 처음에는 free이다.
- 임계구역에 접근하기 전에 acquire을하고, 떠난 후에 release 해주어야 한다
- release와 acquire 사이에는 프로세스가 lock을 hold한다
- 한번에 하나씩만 lock 가능
- locks들은 spin한다 (spinlock)
<br><br><br>

### <span style = "color:lime">※ 소프트웨어 해결책
-두 개의 프로세스가 있는 경우(1);
  - turn이라는 variable을 이용하고
  - turn = I 일 때 Pi가 임계 섹션에 접근 가능
  - 상호 배제 만족하지만, progress는 만족하지 않음
  - 왜냐? 만약에 0이 임계영역으로 들어가기 전에 죽어버리면 turn은 여전히 0이기 때문에 임계영역이 비어있어도 turn(1)이 아니기 때문에 사용 x

- 두 개의 프로세스가 있는 경우(2);
  - flag 변수를 쓴다. flag[i] = true ; Pi는 임계 섹션 입장 가능
  - 상호 배제 만족하지만 여전히 progress는 만족 하지 않음
  - 무슨 뜻이냐면 Pi가 쓰고 싶으면 flag[i]==true로 바꾸고, flag[j] == true이면(쓰고있는애가 있으면 안씀), flag[j] == false로 바뀌면 내가 쓰고, 다 쓰면 flag[i] = false로 바꿈! 근데 이것도 여전히 flag를 false로 안바꾸면 안된다..


- 두 프로세스가 있는 경우(3); 피터슨 알고리즘
  (1) (2)를 동시에 이용해야 함
  - flag[i] := true; // 누가 임계영역에 진입할 것인지
  - turn := j; // 누가 임계 영역에 들어갈 차례인지
  - flag[0]=true로 설정하면 0번 스레드가 나 cs 들어가고 싶어! 의사 표시
  - turn = 1이라고 하면 1번보고 먼저 들어가라고 양보
  - 1번 프로세스가 작업 마치면 flag[1] = false => 0번 스레드 일 가능
그러니까
  - turn을 while문을 통해 돌아가면서 남턴 남의flag가 true일 때 제외하곤 사용 가능
<br><br><br>

### <span style = "color:powderblue"> ※ 하드웨어 솔루션(원자성)
- 메모리 접근은 동시 접근 불가능
- Test and Set
- 명령어 수행 중에 선점을 막음
- lock을 걸어 다른 프로세스가 cs접근 못하게 함 while문을 통해
- 단점은 3개 이상 있으면 프로세스가 cs에 접근 못하는 경우가 있음 (bounded waiting 조건 위배)
<br><br><br>

### <span style = "color:powderblue"> ※ spinlock의 문제점; 
- 자원의 낭비
<br><br><br>

### <span style = "color:powderblue"> ※ interrupt 무효화;
- context switching 방지
- 커널에서만 가능
- cs가 길면 인터럽트 같은 중요한거 miss할수도?
- 멀티 프로세스에서 유효 x
- 더 높은 단계의 동기화가 필요

- spinlock은 조금 기다리면 나도 임계구역 쓸수 있다는 마인드에서 출발
<br><br><br>

### <span style = "color:powderblue">※ 높은 수준의 동기화
동기; 스핀록과 인터럽트 무효화는 작은 임계구역에 한함 (낭비), 상호 배제 이외엔 안함)
더 높은 동기화 필요
1) 블록 기다리는 프로세스
2) 임계 구역에 interrupt가 허용되도록 해야 함
- Atomic locks들이 높은 수준을 허용케 함

<br><br><br>

### <span style = "color:lime">※  세마포어 (Semaphore)
- (다익스트라가 고안 <- 또 너야?)
- busy waiting이 없는 synchronization
- wait(s); 세마포어 s가 열릴 때까지 막는다 => P()
- signal(s); 다른게 들어오도록 해준다 => V()
![image](/images/OS-06-01.png)
- 0보다 작거나 같으면 누가 쓰는중임
- 세마포어가 0보다 크면 해당 자원에 접근 가능. 0이면 접근 불가능
- 즉 자원에 접근하기 전에 세마포어 값을 검사해서 값이 0이면 자원을 사용할 수 있을 때까지 기다리고, 0보다 더 크면 자원에 접근해서 세마포어값을 0으로 감소하여 다른 프로세스들이 접근 못하도록 해야 한다. 자원 사용 끝나면 다시 세마포어를 증가시켜서 다른 프로세스가 사용할 수 있도록 한다.
<br><br><br>

### <span style = "color:powderblue">※ 세마포어 종류
- 이진 세마포어 counter = 1;
- counting 세마포어 counter = N; (이만큼은 접근 가능하다..)

- 세마포어는 =>>> 데드락을 유발할수 있다
- 데드락은 교착상태를 의미 (7단원에서 리뷰할  것)
- 세마포어는 전역 변수
-쓰기 힘들고 버그에 약함

그렇기 때문에 세마포어를 잘못 쓰면 동기화 에러를 유발할 수 있다
<br><br><br>

### <span style = "color:powderblue">※ 모니터
- 뮤텍스는 프로세스간에 동기화할 때 사용 가능하지만, 모니터는 프로세스 내의 다른 스레드 동기화에 이용한다는 것
- 뮤텍스는 os 커널, 프레임워크, 라이브러리에 의해서 제공, 모니터는 프레임워크나 라이브러리 그 자체에서 제공
- 모니터는 공유 자료구조를, 공유 데이터에 실행하는 procedure을, encapsulate한다
- pthread 동기화; 컨디션 변수, mutex
<br><br><br>

### <span style = "color:powderblue">※ 리눅스 커널 동기화
- 커널; request 응답 서버
- race condition 방지
- 커널 데이터구조 보호하는 것이 목표
- 원자 operation
- 인터럽트 무력화
- locking => 스핀 락, 커널 세마포어
<br><br><br>
<br><br><br>


# <span style = "color:Yellow">**※ 예상 질문**
## <span style = "color:Orange"> **Q1 . 세마포어, 뮤텍스, 모니터의 개념과 차이점을 간단히 서술** </span>
세마포어, 뮤텍스, 모니터는 모두 프로세스 동기화에 이용한다는 공통점이 있다. 모니터와 뮤텍스는 상호배제를 하여 임계구역에 단독으로 이용한다는 점이 있으나, 세마포어는 한 개 혹은 그 이상의 개수도 접근이 가능하게 한다.


세마포어는 wait과 signal을 이용하여 critical section을 이용한다. 보통 counting 세마포어를 이용하는데, 최대 접근 가능한 수의 프로세스를 셀때마다 –1씩 하여 0이하가 될 경우, 접근 금지, 프로세스가 완료되면 +1을 함으로서 다른 프로세스들에게 기회를 주는 형식을 하고 있다. 세마포어를 조작하는 함수들이 여러 프로세스에 분산이 되며, 독점이 아닌, 전역 변수를 이용하는 것이기 때문에 하나의 문제점이 다른 문제점까지 영향을 끼치는 Deadlock의 문제점이 있을 수 있다. 세마포어는 counting 세마포어가 있으며, binary 세마포어 또한 존재하는데 ,이를 뮤텍스라고 지칭한다.

이는 MUTual EXclusion으로 상호배제라고도 하는데, 이는 임계구역을 가진 스레드들의 실행 시간을 서로 겹치지 않게 단독 실행하게 한다. 

세마포어의 문제점을 해결하기 위해 나오는 기법이 모니터로, 뮤텍스는 프로세스간에 동기화할 때 이용하지만, 모니터는 하나의 프로세서 내의 다른 스레드들을 동기화할 때 이용한다.(다른 프로세스 접근 block) 또한, 뮤텍스는 커널, 라이브러리등에 의해 제공되지만, 모니터는 프레임워크 혹은 라이브러리 그 자체에서 제공이 된다. Condition 변수라는 것이 존재하는데, 만일 프로세스가 자신이 continue 할수 없다는 것을 안다면(deadlock과 같은 상황), 자기 자신이 sleep하거나, 다른 프로세스가 대신 이용하도록 하는 기능을 가지고 있다
