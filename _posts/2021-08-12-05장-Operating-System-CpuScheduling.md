---
title: "운영체제(OS) 05장 CPUScheduling"
except: "운영체제(OS) 에서 Process를 어떤 순서로 실행시킬지 생각해보자."
categories:
  - 운영체제
tags:
  - Scheduling
  - Preemptible
  - FCFS
  - RR
  - SJF
  - MLFQ
toc: true
toc_sticky: true
---

# 5장 CPU 스케줄링
## <span style = "color:powderblue"> ※ CPU 스케줄링
### <span style = "color:powderblue"> ※ 스케줄링의 기본적인 컨셉
- Queue 꼴이다
- Preemptible VS Non Preemptible
- Queue에 있는거 순서를 바꿀수 있는가? -> preemptible

- CPU 스케줄링을 Short term scheduling이라고 하는데, 어떤 프로세스를 다음번에 실행시킬지, 그리고 얼마나 오래 실행시킬지에 대한 스케줄링을 한다!
- short term scheduler을 전통적으로 dispatcher이라고 부르는데 다른 이벤트에 의해 프로세스 변경 ex) Intterupts, Traps(exceptions), System calls
- 디스패처는 프로세스 스위칭을 통해 CPU에게 컨트롤 권한을 준다. (근데 리눅스에는 이런거 없고 그냥 커널 코드에 스케줄러가 구현되어있음)
- 디스패처가 막 프로세스 바꾸고 하면 시간 소요됨

그렇다면 CPU 스케줄링 criteria(기준) 는 무엇일까?
<br><br><br>

### <span style = "color:powderblue"> ※ CPU oriented(시스템);
- utilization(활용): CPU 최대한 바쁘게 하기 (효율적으로 일 처리하기)
- throughput(처리량): 시간마다 실행이 끝나는 프로세스 개수
<br><br><br>

### <span style = "color:powderblue"> ※ Process oriented(유저):
- Turnaround Time: 특정 프로세스를 실행하는데 드는 시간
- waiting time; ready queue에 기다린 시간 (프로세스가)
- response time; request가 제출되는 순간부터 첫 response가 발생할때까지의 시간
<br><br><br>

### <span style = "color:powderblue"> ※ 최적 기준;
- cpu 활용 최대화, 처리량 최대화
- turnaround time, waiting time, response time 최소화

<br><br><br>

### <span style = "color:powderblue">※ 프로세스; CPU Bound / I/O Bound
- CPU – IO 사이클
- CPU bound 프로세스는 IO bound 프로세스에 비해 더 긴 CPU bursts를 가지고 있다.
<br><br><br>

### <span style = "color:powderblue"> ※ Priority
- Request들은 Priority에 의해 serviced 된다.
- 우선순위에 따라 처리됨 (-> starvation problem 생길 수 있음; 우선순위 낮은 사람들)
<br><br><br>

### <span style = "color:lime"> ※ 스케줄링 Policies;
- selection function(which); 다음번에 어떤 것이 queue에서 선별되는가
- decision mode(when); 언제 이게 바뀌는가 -->
- 여기서 non preemptive; 중간에 임의로 변경 x => 끝나거나, I/O에 의해 block되거나
preemptive; 중간에 임의로 변경 가능 => time quantum이 expire되던지... (독점을 막을 수 있음)
<br><br><br>

### <span style = "color:powderblue">※ Decision Mode

![image](/images/OS-05-01.png)
<br><br><br>

### <span style = "color:powderblue">※ FCFS(선입선출) 스케줄링
- 그냥 다 끝날때까지 기다려~ (non preemptive)
<br><br><br>

### <span style = "color:powderblue">※ Round Robin(일정 시간 주는 방식) 스케줄링
- time burst 있음.. 시간 다되면 맨 뒤로 돌아가기 (시간에 preeemptive)
<br><br><br>

### <span style = "color:lime"> ** ※ Shortest Job First; SJF 스케줄링 ** </span>
- 가장 CPU burst time이 적은 것을 고른다
- Non preemptive이면; 골라져서 실행되는 순간부터는 노 터치 (그래도 끝나고 다음꺼 고를때는 가장 작은 CPU burst time 고름)
- preemptive면; 골라져서 실행되는 순간에도 비교를 해서 가장 짧게 끝나는 것을 한다; 근데 starvation 가능
![image](/images/OS-05-02.png)
![image](/images/OS-05-03.png)
- 차이점 유의!
<br><br><br>

### <span style = "color:powderblue">※ CPU Burst 계산하는 방법

- 단순 평균;
T[i] = I번째 cpu burst
s[n+1] = (1/n)Sum{i=1~N}T[i] (그냥 평균 때리는 것)

- exponential averaging
가중치를 곱하는 것
최근 instance에 더 가중치를 둠
a*(1-a)^i * t(n-i)
<br><br><br>

### <span style = "color:powderblue">※ Multilevel Queue 스케줄링
- 각각 다른 queue들은 각각 다른 스케줄링 알고리즘이 있다.
- 프로세스는 다양한 queue 사이를 이용할 수 있다. (preemptive 스케줄링 & 동적 우선순위)
- I/O 바운드 프로세스는 더 높은 우선순위의 큐에 있을 것
<br><br><br>

### <span style = "color:powderblue">※ Fair share scheduling (FSS)
- 모두 공평하게 이용하는 것
<br><br><br>

### <span style = "color:powderblue">※ multiple processor scheduling( 다중 프로세서 스케줄링) 프로세서가 많으면 더 복잡함
- 프로세스가 queue들 사이 이동 가능
- 동적 우선순위를 통한 선점 방식
- 여러 ready 상태의 queue가 순위 내림차순 순서로 있음
- 자기보다 높은 순위의 queue 없을 때만 선택
- 마지막 queue는 round robin이므로 계속 waiting

<br><br><br>

### <span style = "color:powderblue">※ 유닉스 스케줄러
- cpu 바운드 프로세스보다 interactive process 상
- 응답시간 최소화
- 3~4개 mlfq 사용
- queue 내에선 rr queue 간에는 priority
<br><br><br>

### <span style = "color:powderblue">※ real time scheduling
- hard rt (최악의 시간 analysis 예측)
- 보장된 시간 내에 중요한 작업 완료
- 특수 목적 소프트웨어 구성
<br><br><br>

### <span style = "color:powderblue">※  soft rt 
- 중요 프로세스가 그 외보다 더 높은 우선순위를 할당받게 됨
- 우선순위 스케줄링 필요
- 커널 전체가 preemptible
- priority inversion problem 우선순위 inversion 문제 (높은 우선순위가 낮은 우선순위에 의해 막힐수 있음 = how? mutex(공유자원 동기화 때문))
<br><br><br>

### <span style = "color:powderblue">※ 그래서 어떤 알고리즘 쓰는게 좋을까?
- 시스템 worklord에 우선
- performance 기준에 따라
- deteministic modeling(미리 결정된workload ->빠르지만, 구현 hard)
- queueing 모델
- 시뮬레이션 분석(시뮬레이션에 의해 결정)
<br><br><br>

### <span style = "color:powderblue">※ 솔라리스 스케줄링
(우선순위, 시간 sharing, preemptive scheduling 모두 반영)
4가지 우선순위 class (다 다른 우선순위와 스케줄링 알고리즘 가짐 & real time class, system class, time sharing / interactive classes)
<br><br><br>


### <span style = "color:powderblue">※  윈도우 스케줄링
- 우선순위, time sharing, preemptive 모두
- 32 level 우선순위 클래스 (16 –31)
- variable priority class (1-15) // 이건 자주 바뀔 수 있음 dynamic함
<br><br><br>


### <span style = "color:powderblue">2.6 스케줄링은 옛날 꺼니까 pass
<br><br><br>

<br><br><br>


# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . 3가지 스케줄링 방법에 대해서 설명하고 그 비교 분석** </span>
스케줄링은 FCFS, RR, SJF 3가지가 존재하는데, FCFS는 선입선출로 처음 waiting queue에 들어온 프로세스 순으로 실행을 한다는 점이 있다. 들어온 순서대로 하기 때문에 초기에 너무 긴 execution time이 있는 프로세스가 있다면 다른 프로세스는 waiting 시간이 평균적으로 길어진다는 점이 있다. 두 번째로는 RR인데, FCFS와 같은 선입선출의 방식을 따르지만, 일정 시간 (CPU burst)를 정해 해당 시간만큼 execute 할 수 있도록 공평하게 시간을 할당해 준다. 이를 통해서 시간이 만료됐지만 완료가 안된 프로세스는 queue의 마지막에 다시 위치하게 된다. 마지막으로는 SJF로, 가장 빠른 cpu burst를 가진 것을 우선순위로 먼저 선택한다는 점이 있다. SJF에서는 2가지 방식이 있는데 preemptive와 non preemptive이다. non preemptive는 현재 실행되고 있는 프로세스는 대기 queue에서 우선순위를 누가 가지고 있는지와 별개로 완료될때까지 프로세스 변경을 하지 않는다는 점이며, preemptive의 경우, 실행되고 있는 프로세스도 다른 queue의 우선순위와 비교하여 변경이 수시로 가능하다는 점이 있다. 이러한 우선순위를 도입하는 것에 있어서 우선순위가 낮은 프로세스는 starvation problem이 존재할 수 있다는 단점이 있다.

## <span style = "color:Orange"> **Q2 . MLFQ에 대해서 간단히 설명 할 것** </span>
Multilevel Feedback Queue Scheduling은 다단계 큐의 단점을 개선한 방식인데, 다단계 큐는 각각 프로세스의 중요도에 따라 큐로 나누고 각 큐에서 다른 알고리즘을 이용해 효율을 높이는 장점이 있지만, 한 queue에 들어가게 되면 다른 queue로는 변경하지 못한다는 점이 있다. 이를 개선해 queue간에 변경을 할 수 있는 개선점을 가진 것이 다단계 피드백 queue이다. 예를들어 RR = 8, RR = 16, FCFS 의 3개의 queue가 있다면 RR8에서 시간 완료후 실행 완료가 되지 않는다면 RR16으로 이동하며, 최후에는 FCFS로 이동하는 방식과 같다. 그렇기 때문에 I/O bound 프로세스가 cpu bound 프로세스보다 높은 순위를 배정받게 된다. 이러한 방식을 택한 것은 CPU burst가 큰 프로세스는 우선순위가 낮다고 판별하기 때문이다. 이 경우, cpu burst가 너무 커서 낮은 순위의 queue에만 머무는 것을 방지하기 위해 aging을 도입하면 이를 완화할 수 있다. Ex. 이를 통해 게임을 다운로드(CPU burst 큼) 하면서 웹서핑(작음)을 하는 것이 가능

## <span style = "color:Orange"> **Q3 . soft/hard real time scheduling 특징, 차이점?** </span>
hard real time은 중요한 task를 주어진 시간 내에 완료해야 한다는 점이 있다. 프로세스는 리소스 요구사항과 함께 등장하며, 최악의 경우를 계산해야 한다. 그에 반해 soft real time은 프로세스들이 우선순위를 가지며 우선순위대로 실행이 가능하게 된다. 이는 starvation을 유발하게 되기도 한다.