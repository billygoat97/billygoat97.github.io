---
title: "운영체제(OS) 07장 DeadLock"
except: "운영체제(OS) 에서 Resource 활용으로 일어 날 수 있는 Deadlock 에 대해 알아보자"
categories:
  - 운영체제
tags:
  - Deadlock
  - Banker's Algorithm
  - Safe state
toc: true
toc_sticky: true
---

# 7장 DeadLock
## <span style = "color:powderblue"> ※ DeadLock
### <span style = "color:powderblue"> ※ 데드락의 정의
- 자원을 들고 있는 프로세스들이 다른 프로세스들이 들고 있는 자원을 acquire하기 위해 멈춰 있는 교착상태를 의미한다.
- 프로세스가 교착상태가 아니면 그래프는 사이클이 아니다.
- 만약에 자원별로 한가지의 instance만 있다면 프로세스들은 교착상태에 빠진다(프로세스와 자원이 사이클을 만족할 때)
<br><br><br>

### <span style = "color:powderblue"> ※ 필요조건(4가지 모두 만족해야 교착상태에 빠질 수 있음)
- 상호배제 ; 한번에 한 프로세스만이 자원을 이용 가능
- hold and wait; 최소 하나의 자원을 들고 있는 프로세스가 다른 프로세스가 들고 있는 자원을 기다리는 중
- no preemption; 다른 프로세스보고 갖고 있는 자원을 내놓으라고 말 못할 때,
- 원형 대기; 그래프에서 사이클
<br><br><br>

### <span style = "color:powderblue"> ※ 데드락이 없는 2가지 방법;
- 데드락 예방 / 회피
- 4가지 필요조건이 일어나지 않도록
- 상호배제; 비공유 자원에 대해서 할수 있는게 딱히 없음
- hold and wait; 다른 자원을 필요로 할 때 자원을 들고 있지 않도록 한다. 
- no preemption; 만약에 내가 다른 자원 요구하는데 다 할당되어 있으면 모두 풀어주는 것
- 원형 대기

<br><br><br>

### <span style = "color:powderblue"> ※ 데드락 회피;
- 런 타임 체크하고 자원 할당 체크
- 자원 할당 상태를 동적으로 변화시켜 원형대기 상태를 방지한다.

<br><br><br>

### <span style = "color:powderblue"> ※ safe state
- 데드락에 걸리지 않도록 (만약에 자원을 요구할 때 그거 할당하면 safe state에서 벗어나는지 확인함)
<br><br><br>

### <span style = "color:powderblue"> ※ 뱅커 알고리즘;
- 모든 순서로 요구하는 자원을 할당하는 안전 순서열이 존재한다면 문제가 없지만 그렇지 않다면 최소한 한명에게 대출해줄 금액은 항상 은행이 보유하고 있어야 한다는 개념.

MAX
Allcoated
Availabe
3가지를 가지고 생각
- ![image](/images/OS-07-01.png)

이 상태에서 남는게 3개일 때, 문제 해결 가능 (p1,p0p2)그러나, p2에게 하나 더 할당하면 p1은 끝내도 p0를 못해서 교착상태 => 즉 데드락이 걸리는 불완전 상태로 변함

- 단점; 할당할 수 있는 자원의 수가 일정해야 함
- 사용자 수가 일정해야 함
- 불안전 상태를 방지해야 함으로 자원 이용도가 낮음
- 최대 자원 요구량을 알아야 함
- 시간 내에 자원 반납해야 함
<br><br><br>

### <span style = "color:powderblue"> ※ 데드락 탐지/ 회복
- 탐지알고리즘의 남용은 상당한 오버헤드 가능
1. work := 가능한 것
 allocation[i] != 0 => finish[i] = false
2. finish[i]  = false, request[i] <= wo가 인것을 찾아보자 
3. 만약에 존재하면
몇몇가지는 데드록이다
4. 아니면, work := work + allocation[i]
finish[i] = true 로 바꾸고 2로 돌아기
<br><br><br>

### <span style = "color:powderblue">※ 데드락 복귀
- 순환 대기 ROrl 위해 한 개 이상을 중지
- 교착상태에 있는 프로세스들로부터 자원 선점
- 순서
- 우선순위
- 연산시간 + 남은 시간
- 자원  양
- 프로세스 수(종료되어야 하는)
<br><br><br>
<br><br><br>


# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . 데드락을 유발하는 4가지 필요조건에 대해 설명하고, 왜 충분조건이 아닌지에 대해서 설명하시오** </span>
데드락의 4가지 필요조건은 상호배제, hold and wait, no preemption, circular wait 4가지로, 상호배제는 한번에 한 프로세스만이 자원을 이용가능하며, hold and wait는 자원을 들고 있으면서 자원을 요청하는 프로세스가 다른 프로세스가 들고 있는 자원을 기다리는 것, no preemption은 다른 프로세스보고 자원을 요구하라고 말 못할 때, 원형 대기는 그래프에서 사이클을 구축할때를 의미한다. 다음 4가지를 모두 만족할 때에만 교착상태가 일어날 수 있는 것이며, 그중 하나만 아니더라도 데드락이 되지 않는 모습이 존재하기 때문이다.

## <span style = "color:Orange"> **Q2 . 뱅커의 알고리즘을 간단하게 설명하시오** </span>
뱅커의 알고리즘은 회피 알고리즘으로 프로세스가 자원을 요청했을 때, 해당 요청을 수락한다면 안전 상태를 유지하는지,(즉 안전 순서가 있는지 확인)을 하고 대기시키는 것이다. 안전 상태를 확인하는 것은, 현재 할당한 후에, 남은 자원을 이용하여 순차적으로 할당된 것을 회수할수 있는지 확인 후에 모든 프로세스가 완료될 수 있는 방법이 있는지를 state로 표시할 수 있다.