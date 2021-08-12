---
title: "운영체제(OS) 08장 Memory Manangement"
except: "운영체제(OS) 에서 Memory 관리 기법에 대해서 알아보자"
categories:
  - 운영체제
tags:
  - Partitioning
  - Buddy System
  - Paging
  - Segmentation
toc: true
toc_sticky: true
---

# 8장 Memory Management
## <span style = "color:powderblue"> ※ 메모리 관리 기법
### <span style = "color:powderblue"> ※ 메모리 관리 기법의 등장 이유
- 멀티프로그래밍으로 인해 메모리에 여러 프로세스들이 접근함
- 안정성과 메모리 하드웨어 업데이트를 빨리 해야함
<br><br><br>

### <span style = "color:powderblue"> ※ 주소 공간!
주소공간은 2가지로 나뉘는데
물리적 주소 공간과 논리적 주소 공간을 의미한다
- 물리적 주소 공간은 하드웨어에 의해  support 되며, 절대주소라고도 불린다. 주소는 0부터 MAXsys까지 올라간다.
- 논리적 주소 공간은 프로세스 기준의 메모리를 바라보는 것이다. 0에서 시작하며, 0부터 MAXprog까지 간다.
- 정리하면, 프로세스는 논리적 주소 공간을 이용하는데 메모리 관리 기법으로 이것을 분할하여 물리적 주소에 저장한다
<br><br><br>

### <span style = "color:powderblue"> ※ 주소 공간 Translation
- 상대적인 주소에서 물리적인 주소로 변경하는 것은 하드웨어에 의해 일어나야 한다. 프로그램은 동적으로 재배열되어야 한다. (논리적 주소 이전에 상대적인 주소)

<br><br><br>

### <span style = "color:powderblue"> ※ 스와핑
프로세스는 실행되려면 메모리에 올라가야 하는데, 프로세스는 메모리에서 저장공간으로 빠졌다가 다시 데려왔다가 하는 식으로 스와핑이 일어날 수 있다!

<br><br><br>

### <span style = "color:powderblue"> ※  단순 메모리 관리기법 – virtual memory가 없다는 가정 하에서
- 실행 프로세스는 메인 메모리에 로드 무조건!
방법;

### <span style = "color:powderblue"> ※ fixed partitioning
- 그냥 파티셔닝 할 때, 모두 크기 동일하게 자르기 (구현은 쉬워도, 낭비가 크다, internal fragmentation 문제 발생)

### <span style = "color:powderblue"> ※ 동적 partitioning
- 파티션들이 동적으로 생성해서 자신의 크기와 일치하는 파티션에 들어감. internal fragmentation이 없다는 장점이 있고, 메인 메모리를 효율적으로 이요하지만, external fragmentation 문제가 있어 메모리 집약이 필요(외부에서도 메모리 효율적으로 이용하지 못함 -> compaction을 함)
<br><br><br>

### <span style = "color:powderblue">※  버디 시스템
- 동적/fixed 파티셔닝 문제를 해결하는 방법;
- 사이즈가 2^k이고 L <= K <= U 이면 가능하다는 것

- ![image](/images/OS-08-01.png)

<br><br><br>

### <span style = "color:powderblue">※ 단순 페이징
- 물리 메모리 주소는 연속적이지 않을 수 있다. 그렇기 때문에 물리 메모리를 여러개의 프레임으로 분리한다. 2의 제곱수. 논리적 메모리를 blocks로 분리하는데 이를 페이지라고 한다. 즉 쪼개서 저장한다는 것! 페이징은 pair(p,o)으로 하는데, p는 페이지 숫자, o는 페이지 offset이다. 즉 3,6이면, 3번째 페이지에서 o번째 offset이다.) 이러한 페이지를 프레임(물리 주소)에 mapping 된다.
- 이러한 가상 주소 공간을 물리 주소 공간으로 변경시키는 방법; page table을 이용함
- 각 프로세스별로 1개의 table을 가짐
- page table entry ; page frame number, bitflags로 구성됨
- page sharing; 페이지가 공유된다는 것
<br><br><br>


### <span style = "color:powderblue">※ 페이지 테이블
- PTBR(page table base register => 페이지 테이블 시작 위치), PTLR(page table length register => 페이지 테이블 크기)로 구성되어있다.
- 모든 데이터와 명령 access는 두 개의 메모리 accesses를 요구한다. 이는 페이지 테이블과, data/instruction이다.
- 이렇게 두 개의 메모리 접근하는 문제는 TLB 라는 것을 이용해서 해결할수 있다(Translation look aside buffers)
<br><br><br>

### <span style = "color:powderblue">※ Associative Memory(연관 기억장치)
- 자료를 찾을 때 주소를 직접 찾지 않고 기억된 내용의 일부를 이용하여 access할수 있는 기억장치(CAM)
- 장점; 주소에 의해서만 접근 가능한 기억장치보다 정보 검색이 신속하다.
- 캐시 메모리나 가상 메모리에서 이용하는 mapping table에 이용

- 각 가상 메모리 reference는 두 개의 메모리 reference를 요구함 => TLB이 해결책!
<br><br><br>

### <span style = "color:powderblue">※ EAT (Effective Access time) 기대 접근 시간 
(tlb access time + memorry access time)*A + (TLB access time + 2*memory accesstime)(1-A)
A = hit ratio 
A가 크면 EAT 줄어듬
<br><br><br>


### <span style = "color:powderblue">※ 큰 page table을 다루는 방법?
예시)
- n비트 논리 주소
- 2^k 바이트  페이지 주소
- 4바이트 페이지 테이블 엔트리
- 2^n/2^k * 4바이트
- 쓰고 있는 주소만 이용함 (부담됨)
<br><br><br>

### <span style = "color:powderblue">※ 계층적 구조;
- 다중 페이지 테이블로 분리
- 해쉬 페이지 테이블
- inverted page table
<br><br><br>

### <span style = "color:powderblue">※ 다중 페이지 테이블
- 2 level page table
outer PT => PT(페이지의 페이지테이블) => 메모리
논리적 주소 예시
32비트 => 10(페이지 주소) / 10(페이지 주소) / 12 페이지 offset 
- ![image](/images/OS-08-02.png)
<br><br><br>

### <span style = "color:powderblue">※ 해쉬 페이지 테이블;
- 32비트 주소;
- 가상 페이지 숫자가 page table로 해싱됨 (중복해서 들어옴 linked list)
<br><br><br>

### <span style = "color:powderblue">※ inverted 페이지 테이블 (역페이지)
- 시스템에 단 하나의 페이지 테이블
- 메모리(프레임)별 하나의 entry -> 페이지 테이블이 너무 크면 search가 오래 걸림
<br><br><br>

### <span style = "color:powderblue">※ Segmentation
- 프로그램은 세그먼트들의 집함
- segmentation은 사용자가 보게 되는 메모리 관리 개념을 그대로 지원 메모리 관리 기법
- 세그먼트라는 덩어리로 나눠서 배치
- 세그멘트는 (코드 데이터 스택)으로 덩어리
- segment-number, offset으로 구성
- segment table (base->시작 limit->길이)
- 세그멘트 자체가 동적, 외부 단편화가 일어날 수 있음.
<br><br><br>

### <span style = "color:powderblue">※ 페이징 vs segmentation
- 페이징은 작은 내부 단편화
- segmentation은 외푸 단편화

두 개 같이 쓰면 좋지 않을까?
- 세그멘트로 논리적으로 연결된 unit 관리
- 페이지로 세그먼트 분할(일정 사이즈로)
- 세그먼트 덩어리 => 페이지 크기로 분할
<br><br><br>

### <span style = "color:powderblue">※ 8장 초 간단 정리
- paritioning 하려다가 buddy system (연속적인 메모리 할당)
- 프로세스를 -> segment로 분할(segment table) -> segment를 paging 기법으로 분할해서(page table) 물리 메모리의 frame에 저장 (비연속적인 할당)
<br><br><br>
<br><br><br>

# <span style = "color:Yellow">**※ 예상 질문**
## <span style = "color:Orange"> **Q1 . 물리적 주소와 논리적 주소의 특징을 설명하고 그 차이를 비교하시오** </span>
물리적 주소는 하드웨어에 의해 support되며, 절대 주소라고 불린다. 실제로 배치되는 주소라고 할 수 있다. 그에 반해 논리적 주소는 프로그램이 보는 기준의 주소로, 연속적인 주소이며, 이를 실제 물리적 주소에 배지하려고 할 때, address translate 기법을 이용하여 논리적 주소에서 물리적 주소로 변경을 한다. 물리적 주소는 연속적이지 않아도 되기 때문에 paging기법을 통해 연속적인 논리적 주소에서 비연속적인 물리 주소로 변환이 가능하다.

## <span style = "color:Orange"> **Q2 . Fixed partitioning과, Dynamic partitioning의 장단점** </span>
Fixed partitioning은 메인 메모리에 분할이 된 일정 크기의 구역이며, fixed이기 때문에, 들어오는 프로세스 사이즈와 무관하게 정해져 있는 크기의 파티션을 의미한다. 이는 구현하기 쉽다는 장점이 있지만, 파티션 내부의 분량을 효율적으로 이용하지 못한다는 내부 단편화 문제점이 대두된다. 이와 반대로 동적 partitioning은 프로세스가 들어오는 만큼의 크기를 할당해 주어 내부 단편화를 제거하는 방식이다. 이는 프로세스 크기에 따라 할당하는 만큼 메인 메모리를 효율적으로 이용할 것 같지만, 할당된 프로세스가 제거됨에 따라 남는 사이즈의 파티션이 다른 프로세스에게 적합한 사이즈가 아니기 때문에 외부 단편화가 일어나게 된다. 이는 compaction이라는 메모리 이동 기법을 이용하면 해결이 되지만, compaction자체가 상당히 부담이 많다는 단점이 있다.

## <span style = "color:Orange"> **Q3 . Buddy system에 대해서 설명하시오** </span>
버디 시스템은 내부/외부 단편화 문제를 해결하기 위해 등장한 것으로, 절충안을 마련하기 위한 방법이다. 파티션의 크기를 동적으로 할당하되, 2의 제곱수로 할당하여, 프로세스가 입력됐을 때 위치를 할당하며, 다시 프로세스가 완료됐을 때 파티션을 원래대로 합치는 기법을 의미한다. 이는 2의 배수이기 때문에 index를 쉽게 접근할수 있으며, 효율적인 메모리 이용이라고 할 수 있다.

## <span style = "color:Orange"> **Q4 . 페이징에 대해서 설명하시오** </span>
페이징 기법은 물리적 주소 공간이 비연속적이라는 점에서 착안하여 시작하였는데, 물리 메모리를 정해진 크기의 블록들로 분리하여 프레임이라고 이름을 붙이고, 논리적 메모리를 페이지라는 정해진 크기의 블록으로 분리를 한다. 이를 통해 페이지는 연속적이되, 페이지 테이블을 통해 각각 다른 프레임에 비연속적으로 존재해도 괜찮도록 만드는 방법이다. 이를 통해 앞서 제시했던 비연속적인 물리 메모리를 이용하여 연속적으로 논리 메모리에서 이용할수 있다는 장점이 있다.


## <span style = "color:Orange"> **Q5 . Page Table과 그 종류에 대해서 설명하시오** </span>
Page Table은 페이징을 하기 위해서 존재하는 논리주소공간에서 물리 주소공간으로 변환시켜주는 표를 의미한다 page table은 PTBR PTLR두가지로 구성되어 있는데 전자는 페이지 테이블 시작 위치를 의미하고, 후자는 길이를 의미한다. 페이지 테이블은 메인 메모리에 저장되기 때문에 프로그램에 의한 모든 메모리 접근은 2차례 요구가 된다. 이를 위해서 TLB를 이용하여 빠르게 접근을 시도하거나(캐쉬 같은 느낌), 실패한다면 페이지 테이블 탐색을 하게 되는 것이다. 페이지 테이블은 multi level, hsash, inverted 3가지로 구성이 되는데, multi level은 여러개의 page table을 이용하여 많은 주소를 표현하는 것이며,  bit수가 줄어들기 때문에 메모리 공간을 효율적으로 사용 가능하다. hash table은 page number을 hash 함수에 넣어 테이블을 만든 것인데, 중복되는 값은 linked list에서 처리하고, 결과를 linked list에서 실제 메모리를 찾게 된다. 마지막은 inverted page table인데, 페이지 테이블의 크기가 너무 크게 되는 상황을 방지하기 위해서 등장하게 되었다. 실제 메모리에 하나의 엔트리만 유지하는 것으로, 논리 페이지마다 항목 가지는 대신, 물리 프레임에만 대응하게 테이블을 설정한다는 것이 특징이다.

## <span style = "color:Orange"> **Q6 . Segmentation에 대해서 설명하고 paging과 비교하고, 세그먼트와 페이징을 같이 이용하는 것에 대해 설명하시오.** </span>
(쉽게 설명하면 paging은 크기 단위(1cm 크기의 돼지고기), segment 개념 단위(목살, 삼겹))
세그먼테이션은 사용자가 바라보는 메모리 관리 개념(코드, 스택, 데이터)을 지원하는 방식으로, 각각 세그먼트라는 덩어리로 배치하는데, segment number, offset 순서쌍으로 위치가 지정이 된다. 세그먼트 테이블은 페이지 테이블과 유사하게 base, limit를 가지고 있다 (STBR, STLR) . 세그멘테이션의 장점은 보호와 공유가 더 효율적이라고 할 수 있다. 페이징은 모두 일정한 크기로 나누므로 코드 스택 데이터와 같은 부분들이 섞이기 쉽다고 할 수 있지만, 세그먼테이션은 구간별로 나누기 때문에 공유도, 분리도 쉽다고 할 수 있다. 그럼에도 불구하고 세그먼테이션 단독으로 이용하지 않는 이유는 외부단편화 때문이다. 이는 논리적인 단위로 나누기 때문이다. 따라서, 두 장점을 모두 이용하는 것이 세그먼트를 페이징 기법으로 나누는 것으로 세그먼테이션은 보호 공유에 효율적이고 페이징은 외부 단편화를 해결할 수 있다. 그러나, 이는 세그먼트 -> 페이징을 하기 때문에 주소 변환을 두 번 해야 한다는 단점이 있다고 할 수 있다. (세그먼트 테이블 -> 페이지 테이블)
