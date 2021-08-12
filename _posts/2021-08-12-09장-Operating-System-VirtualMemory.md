---
title: "운영체제(OS) 09장 Virtual Memory"
except: "운영체제(OS) 에서 실제 메모리보다 더 많이 이용할 수 있는 Virtual Memory에 대해서 알아보자"
categories:
  - 운영체제
tags:
  - Paging
  - Victim Page Algorithm
  - Belady's Proof
  - Copy on Write
  - Memory mapped files
toc: true
toc_sticky: true
---

# 9장 Virtual Memory
## <span style = "color:powderblue"> ※ 가상 메모리
### <span style = "color:powderblue"> ※ 가상 메모리가 나온 이유
- 프로세스들은 가상(논리적) 주소를 사용한다
- 가상 주소는 하드웨어에 의해 물리적 주소로 translate 된다.
- 기본 기능 ; partitioning, paging, segmentation
- 하드웨어와 OS support; MMU, TLB , page table
- 이를 실제 메모리보다 더 크게 이용하기 위함 (착시현상)
<br><br><br>

### <span style = "color:powderblue"> ※ VM 기술
- 컨셉; 논리 메모리를 물리 메모리로부터 분리!
- 장점; VM은 프로세스간에 독립성을 부여한다.
- VM은 프로그램이 전체 주소 공간을 요구하지 않고도 실행 할 수 있도록 한다 ...?
- 물리 메모리 크기의 한계를 극복하기 위해 나온 기술
- 필요한 부분만 메모리에 적재
<br><br><br>

### <span style = "color:powderblue"> ※  Demand Paging(현재 필요한 부분만 메모리에 올림)
- 페이징 기법인데, page level swapping을 통해서 일어남
- valid bit를 이용한(있는지 없는지 표현)
- 만약에 페이지가 없으면(이용할 때,) page fault가 일어남
- 퓨어하게 하는거면 근데 좀 프로세스 부를때마다 힘듬 => 주변에 있는것들도 예측해서 불러들임
1. valid 비트 확인
2. 0이라면 인터럽트 신호를 보내서 해당 ISR로 점프
3. 디스크를 탐색하여 해당 프로세스의 페이지를 찾음
4. 비어있는 프레임에 할당
5. 페이지 테이블 갱신

- 따라서 page replacement를 위해 OS는 잘해야 한다.

<br><br><br>

### <span style = "color:powderblue"> ※ 만약에 free frame이 없으면 어떻게 하냐?
- page replacement를 한다 (우리는 최소의 pagefault 하는 알고리즘을 원한다)
- 이를 통해 physicial memory가 작아도 큰 가상 메모리를 구축 가능하다.

간단한 원칙
- 페이지를 찾는다
- free frame을 찾는다
- free frame이 있으면 사용, 없으면 replace 알고리즘을 통해 victim frame을 찾는다
- page를 frame을 통해 읽는다
- update한다

<br><br><br>

### <span style = "color:powderblue"> ※ victim page를 잘 골라서 page fault를 줄이는 것
- 쉽게 말해 또 사용할거같은거는 냅두고 사용 안할거 같은거를 victim page로 만드는 것
<br><br><br>

### <span style = "color:powderblue"> ※ belady’s proof
- 가장 오래동안 이용이 안될 페이지를 victim으로 만드는 것이 page fault minimize한다.
<br><br><br>


### <span style = "color:lime"> ※ victim page고르는 알고리즘이 무엇인가?
- 최적 알고리즘 ; 구현 못함! (결과를 알아야 하는건데 말도 안됨)
- FIFO; 먼저 온게 지워짐(벨라디의 anomaly, 프레임이 많다고 fault수가 줄어드는건 아님)
- LRU; 가장 최근에 이용안된거 삭제 (가장 옛날에 이용한거 victim)
구현하기 위해서 counter 혹은 stack을 이용함
- 하드웨어가 replacement에 support 함으로서 reference bi를 모든 page에 제공하면서 다양한 victim 알고리즘 이용가능(LRU approximation)
- Sampled LRU: time interval마다 referencebit를 모든 페이지에 읽고 reference byte에 적용 가장 작은거 찾기!
- clock algorithm: 한번의 기회를 더 준다는 알고리즘 (페이지를 한바퀴 순회하면서 reference bit를 1이면 0으로 바꾸고 다음 순회때도 0이면 take 함)
- two handed algorithm: 한번의 기회를 더 주되, 한바퀴 다 안돌고 뒤에 오는 clock이 다시 check
- LFU (가장 참조 횟수가 적은)
- MFU (가장 참조횟수가 많은)
<br><br><br>

### <span style = "color:powderblue">※ 프레임 배치
- 모든 프로세스는 최소 숫자의 페이지를 요구한다
- local replacement (fixed allocation): 프로세스는 자시자신의 프로세스 페이지에 대해서만 교체 작업을 한다
- 전역 교체 (priority allocation): 모든 프로세스에 대해서 교체함

<br><br><br>

### <span style = "color:powderblue">※ 스레싱(thrashing)
- 메모리에 올라가는 프로세스의 개수가 증가하면 효율이 오를 것 같지만, 일정 이상 증가하면 이용률이 하락하는 현상
- 프로세스 증가, 메인메모리 빈 프레임 개수 감소 그래서 page in/out하는데 문제가 생길 수 있음
- 해결책; local replacement, 혹은 프로세스별 적절한 프레임 할당 
- 정적할당 = > bad
- 동적 할당 => good working set model 
- 현재 시간 기준 일정 시간 이전동안 이용했던 페이지의 집합
- 프로세스에 할당된 프레임의 개수에 반비례해 page fault 변동
<br><br><br>


### <span style = "color:powderblue">※ prepaging
- 여러 페이지를 한번에 load 함
페이지 크기에 따라서 결과가 다름
- 작은 페이지 크기 ;
장점; 내부 단편화 감소, 메모리 효율(실제 쓰이는 것만 쓰임)
단점; 큰 page table, page fault handling 높음
- 큰 페이지 크기;
장점; 작은 page talbe, 작은 page fault handling
단점; 내부 단편화 증가, 메모리 효율성 낮음
<br><br><br>

### <span style = "color:powderblue">※ TLB reach
- tlb에서 접근 가능한 메모리 
- tlb size * page size
- page size가 항상 큰게 요구되지는 않으므로 내부 단편화가 증가되지 않도록 page size 다양화
<br><br><br>

### <span style = "color:powderblue">※ io interlock
- io 쓰는거 replacement 되면 안됨 (io는 누락되면 안되기 때문에)
해결책: 
- 입출력을 사용자 메모리에서 애초에 실행 x
- 입출력에 lock
- 입출력을 시스템 메모리와 입출력 기기에서만 발생
<br><br><br>


### <span style = "color:powderblue">※ 가상 메모리 장점
- copy on write
- memory mapped files
<br><br><br>

### <span style = "color:powderblue">※ Copy on Write
- 부모와 자식이 동일한 메모리의 페이지를 공유할 때, 읽기만 할때는 공유 해도 되지만, 만약에 write를 할때만 copy해서 복사본을 write하게 한다. (효율적)
<br><br><br>

### <span style = "color:powderblue">※ Memory mapped files
- disk block을 mapping해서, 파일 입출력이 메모리 근처라고 여겨지게 함
- 파일 입출력을 메모리를 통해 처리하므로 파일 접근 단순화
- 메모리 내 페이지 공유 허용, 같은 파일 매핑 가능
<br><br><br>
<br><br><br>

# <span style = "color:Yellow">**※ 예상 질문**
## <span style = "color:Orange"> **Q1 . age fault는 무엇인지 이를 줄이기 위해서 어떤 알고리즘을 이용하는지?** </span>
Page fault는 가상 메모리에서 요구하는 페이지가 존재 하지 않을 때, 일어나는 것으로 page가 없으니 빈 공간에 해당 페이지를 가져오는 것을 요구한다. 만약 해당 가상 메모리에 빈 공간이 존재하지 않는다면 필요한 공간을 생성하기 위해 채워져 있는 페이지들을 스와핑 하게 되는데, 이 스와핑 과정에서 채워져 있는 페이지를 victim page라고 하고 이 victim page를 어떻게 선택하냐에 따라 page fault가 더 나올 확률을 줄여준다고 할 수 있다. 이는 벨라디의 증명에 따르면, 가장 오래동안 이용이 안될 page를 victim으로 만드는 것이 page fault 비율을 줄여주기 때문에 가장 오랫동안 이용이 안되는 victim을 고르는 알고리즘을 찾아야 한다. 최적 알고리즘은, 말그대로 가장 이상적인 알고리즘이기 때문에 미래의 정보를 알지 못하는 이상 구현은 하지 못하나, 비교 대상이 될 수 있다. FIFO는 선입선출로 이용하며, LRU는 가장 최근에 이용하지 않은 것을 victim으로 고른다. 여기서 stack 혹은 counter을 이용한다. 하드웨엉에서 reference bit (참조 비트)를 제공하기 때문에 approximate LRU와 같은 알고리즘도 사용을 하는데, 이는 참조 비트를 바이트에 저장하여 별도의 stack 혹은 counter을 이용하지 않고 크기에 따라 victim을 고를 수 있다. 추가적으로 clock 알고리즘으로, 1번의 기회를 더 주는 방법이 있는데, 만약에 bit가 1인 경우, 0으로 변경 후 다음 time interval이 될 때까지 다시 이용이 되는 경우(1이 되면) 다시 0으로 초기화 시키지만, 0으로 남았을 경우에는 victim으로 이용한다. 이를 발전 시킨 것이 two hand clock으로 첫 침이 0으로 감소, 뒤에 침이 재비교를 하는 과정을 의미한다. 그 외에도 참조 횟수에 따른 MFU LFU가 존재한다.

## <span style = "color:Orange"> **Q2 . thrashing이 무엇인지 + working set/pff이 무엇인지** </span>
물리 메모리에 프로세스가 충분한 페이지가 없으면, 페이지 fault 비율이 높아지며, 이것은 cpu 효율성을 저해시키고, 운영체제 기준에서는 멀티프로그래밍의 degree를 높여야 한다고 생각하기 떄문에,(효율성이 낮으니 프로세스를 추가해서 cpu 효율성을 높여야 한다고 think) 프로세스를 더 추가시킴. 따라서 thrashing은 모든 프로세스가 page를 in out하느라 바쁘다는 것! 이를 위해서는 메인 메모리를 늘려주는 하드웨어적 방식이 있으며, 운영체제가 이를 해결하는 방법은 working set model, page fault frequency이 있다. 워킹 셋 모델은 locality 기반으로 가장 많이 쓰이는 페이지 집합을 메모리 공간에 계속 냅둠으로서 스레싱을 줄인다. page fault frequency 조절을 통해 상한선 하한선 내에서 발생하도록 하여, 상한선을 넘어가면 프레임 할당이 적으니 프레임을 추가하며, 하한선을 내려가면 프레임 할당이 높아 메모리 낭비 중이니 프레임을 회수하는 방법이 있다.

## <span style = "color:Orange"> **Q3. 가상 메모리의 장점 3가지** </span>
- 가상메모리를 이용함으로서 여러 프로그램이 동시에 실행이 되는 것처럼 보이게 할 수 있으며, 필요한 부분만 메인 메모리에 저장하며, 나머지는 보조메모리에 저장함으로서 실제 메모리보다 더 큰 용량을 사용할 수 있도록 해준다.

- Copy On Write: 컴퓨터의 한정된 자원을 서로 다른 두 개의 프로세서가 공유할 때 유용하게 이용한다. 이는, 공유 자원을 이용할 때, modification, 즉 수정이 필요할 경우에는 공유 자원을 copy 한 이후에 write를 하여 modification을 할 수 있다.

- 메모리 맵 파일은 물리 디스크 파일, 장치 공유 메모리 객체와 같이 운영체제에서 파일을 다루는 모든 대상에 사용 가능하며, 프로세스의 가상 메모리 주소 공간에 매핑한 뒤 가상 메모리 주소에 직접 접근하는 것으로 파일 읽기 쓰기를 할 수 있다. 장점으로는 직접적으로 파일 입출력을 수행할 필요 없이 메모리 사용하도록 이용함. 페이징 기법으로 수행 가능, 대용량의 자료 효율적으로 처리할 수 있다. 파일 입출력보다 빠르다는 장점이 있다. 단점으로는 파일의 크기를 변경 불가능하고, 지나치게 큰 파일은 비효율적이다. 쉽게 말해서 file I/o를 read write 이용하기보다는 메모리를 이용해서 접근한다는 것이다!