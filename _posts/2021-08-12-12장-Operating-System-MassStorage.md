---
title: "운영체제(OS) 12장 Mass Storage"
except: "운영체제(OS) 에서 대용량의 데이터를 처리하는 기법에 대해서 알아보자"
categories:
  - 운영체제
tags:
  - Disk Addressing
  - Cylinder
  - Disk Scheduling
  - RAID
  - DAS
  - NAS
  - SAN
toc: true
toc_sticky: true
---

# 12장 MassStorage
## <span style = "color:powderblue"> ※ 대용량 저장장치
### <span style = "color:powderblue"> ※ 디스크를 사용하는 이유
- 우리는 충분한 메모리가 없음! -> 보조저장장치가 더 쌈
- 메모리는 휘발성임!
<br><br><br>

### <span style = "color:powderblue"> ※ Read write operation to a disk
어떻게 읽고 저장하냐
1. 디스크를 섹터 주소로 표현
2. 타겟 트렉으로 head 이동
3. 적절한 head가 유효하게 된다
4. 타겟 섹터가 헤드 밑에 있을때까지 대기
5. read write한다
<br><br><br>

### <span style = "color:powderblue"> ※ 디스크 addressing
- 사실 디스크는 3d 구조지만~ 1D 주소로 바꾼다
- 실린더 베이스 매핑
- 실린더0 = 트랙 0 surface 0~ p-1

<br><br><br>

### <span style = "color:lime"> ※ 디스크(헤드) 스케줄링
- 디스크 큐에 한 개 이상의 request 있을 수 있음
- 그러면 당연히 스케줄링이 등판함

그것은 바로.. page replacement와는 물론 다르지만
fcfs, sstf, scan c-scan, look clook 스케줄

- fcfs 들어가 순서대로 찾음
- sstf 내가 있는 자리에서 움직임 최소화
- scan scheduling ; 한방향으로 쭉 가기 (엘레베이터 알고리즘)
- c scan – 한방향으로 쭉 가되 끝에가면 반대편에서부터 찾기
- look 스케줄링; 한방향으로 쭉 가되, 맨끝까지가 아니라 request가 있는 곳까지 가기
- clook 마찬가지로 request 끝까지 가고 처음부터 다시 시작
<br><br><br>

### <span style = "color:powderblue"> ※ 디스크 관리
- 디스크 파티셔닝;
- 하나의 디스크가 여러개의 파티션 가능;
- 파티션은 실린더의 모음
- 파티션은 논리적으로 독립적인 디스크라고 보면 됨
<br><br><br>

### <span style = "color:powderblue"> ※ swap space 관리 기법
- 일반 메모리 시스템에 최고의 처리량 제공
- file system 분할 혹은 별도 파티션 배치
swap file 접근법
- file system 루틴 이용 공간 생성/이름/할당
- file system structure 이용해서 비효율적
swap 파티션 접근법
- 디스크 파티셔닝 중에 fixed swap space 생성
- swap storage 관리자 이용 block 할당 및 해제
- 속도 > 효율
예시로는
유닉스가 있는데, 스와핑과 페이징을 같이 씀
<br><br><br>


### <span style = "color:lime"> ※ Redundant array of Inexpensive Disks ; RAID
storage를 이용해 정보의 안정성(redundancy 보장 위해 생김)
RAID 0,1,5가 기본 (3은 잘 안쓰임)

## <span style = "color:powderblue">※ RAID 0
- 전체 내용을 정확히 분할해서 저장. 중복 x 속도 빠름, 데이터 보장 x
## <span style = "color:powderblue">
- 전체 복사, 좀 느리고, 자원 낭비 심하지만, 정보의 redundancy 확실히 보장
- ![image](/images/OS-12-01.png)

<br><br><br>

### <span style = "color:powderblue"> ※ RAID 3 
- parity disk 생성함으로서 다른 디스크들에게 문제가 생겼는지 detect 가능, but, parity dist가 문제가 생기면 복구하지 못함
- ![image](/images/OS-12-02.png)

<br><br><br>


### <span style = "color:powderblue">※ RAID 5
- parity disk가 모든 디스크에 분할되어 저장. 하나의 디스크가 문제가 생겨도 다른 disk에서 복구할 수 있을 정도의 redundancy 있음
- ![image](/images/OS-12-03.png)
### <span style = "color:powderblue">※ Small write problems
- 이걸 physically 쓰려면, 1회당 2번의 논리적 read, 2번의 논리적 write해야 함.
<br><br><br>

## <span style = "color:powderblue">※ DAS(Direct Attached Storage)
-전통적인 기법
- 서버 기계 이용 복사 데이터 저장/전달

<br><br><br>


### <span style = "color:powderblue">※ NAS(network attached storage)
- 네트워크 연결하여 storage 기능 제공
<br><br><br>

### <span style = "color:powderblue">※ SAN(Storage Area Network)
- 허브 스위치와 같은 네트워킹 이용해 서로 연결
- interconnected computer system storage 장치
<br><br><br>

### <span style = "color:powderblue">※ NAS, SAN의 장점
- 가용성
- 확장성
- 부하 분산
- 중앙 집중식

<br><br><br>
<br><br><br>

# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . 디스크 스케줄링 종류, 설명** </span>
디스크 스케줄링은 크게 4가지로 분류할 수 있다. FCFS는 말그대로 queue에 들어온 순서대로 디스크를 검색하는 것으로, 구현은 제일 간단하지만, 너무 낭비가 심하다는 점이 있다. SSTF는 현재 존재하는 주소에서 가장 근접한 위치를 queue에서 찾아서 탐색하는 방식을 의미한다. 다음에 등장하는 것이 SCAN, C-SCAN인데, 한가지 방향이 정해지면 그 방향으로 끝까지 진행하는 것이 SCAN 스케줄링이고, 만약에 각 주소의 끝까지 도달하게 되면 방향을 바꿔 역방향으로 scanning을 하는 것이다. c 스캔은 circular이라는 이름에 맞게 해당 인덱스의 끝까지 도착하면 반대 방향의 시작을 새로운 인덱스로 잡아서 다음에 실행될 디스크를 스케줄링한다. 이를 조금 더 발전 시킨 것이 look 스케줄링으로, look, clook은 스캔 알고리즘과 방법은 같으나, 인덱스를 끝까지 가는 것이 아닌, 한 방향의 스캔해야 할 대상이 더 이상 없으면 방향을 바꾸거나, clook의 경우 반대방향의 시작에 가장 가까운 queue의 타겟을 찾게 되는 방법이다. 디스크 스케줄링은 request의 요구 개수와 타입에 따라서 성능이 달라지게 된다. 기본적으로는 SSTF, LOOK을 사용하나, 필요에 따라서 알고리즘을 변경시키게 되고 heavy load를 가지는 디스크에는 scan이나 cscan이 더 높은 효율을 보인다.

## <span style = "color:Orange"> **Q2 . 디스크 파티션 실린더 관계** </span>
실린더는 디스크의 같은 트랙의 부분을 의미하며, 실린더들의 집합을 파티션, 여러 파티션들의 집합을 디스크라고 한다.


## <span style = "color:Orange"> **Q3. RAID 종류 특징** </span>
RAID는 Redundant Array of Inexpensive Disks의 준말로, 높은 수준의 스토리지(보관)을 얻기 위해 저렴한 가격의 pc 클래스 디스크 드라이브 요소들을 이용하는 것이다. 쉽게 말해 같은 정보를 중복하는 방식으로 저장하여 정보에 문제가 생기는 요소들에 대응하기 위해 설계되었다. RAID는 0 1 3 5가 있고, 보통 0 1 5를 이용한다. RAID 0는 여러 개의 디스크에 striped 형식으로 중복되는 정보 없이 각각이 하나의 정보를 분할해서 저장하는 형식이다. 이는 빠른 속도는 있어도 정보의 오류에 대한 보장은 하지 못한다. RAID 1 은 모든 디스크에 같은 정보를 저장하는 방식으로 오류에 대한 확실한 보장을 해주지만, 모든 디스크에 같은 내용의 정보를 저장하는 것은 비효율적이다. RAID 3 는 이를 Parity Disk라는 개념을 이용하여 data recovery를 보장하는 것으로, 나머지 디스크들은 하나의 정보를 분할 저장하며, parity disk는 나머지 디스크들의 문제 여부를 체크하는 역할을 맡는다. 그러나 이러한 과정은 small write problem과 병목현상을 야기할 수 있다. write problem은 새로운 데이터를 작성할 때, 기존의 데이터와 XOR + parity와 XOR을 함으로서 parity도 변경해야 한다는 점이 있어 한번의 logical writing이 2번의 physical read, 2 physical write를 한다는 점이 있다. 또한, parity disk failure이 있다면 이를 대체할 수 없다는 단점이 있다. 이를 발전시킨 것이 바로 RAID 5로 각각의 parity check block들을 각각의 디스크에 분산하여 저장하게 하여, 하나의 디스크가 disabled 되어도 전체 array가 destroy 되지 않는다는 장점이 있다.

## <span style = "color:Orange"> **Q4. SAN NAS, DAS(전통적인 방식) 특징 차이** </span>
NAS는 Network Attached Storage란 뜻으로 통합 보관 시스템이 메시지 네트워크와 결합된 구조로, TCP/IP를 이용하게 된다. 이를 위해서 클라이언트 – 서버 구조의 서버를 이용하게 된다. NAS는 파일시스템이자, 보관 시스템이 된다.

SAN은 Storage Area Network로 저장 디바이스를 공유 접근 네트워크에 접근할 수 있도록 하는 기술로, 보관을 위한 private한 네트워크를 통해 접근이 가능하다. TCP/IP와 같은 기술이 아닌 SCSI아니면 FIbre 채널(허브, 스위치)을 이용한다. SAN은 파일 시스템은 제공하지 않고 보관 기능만 제공한다. 

기존의 전통적인 구조(DAS)는 서버와 저장 장치를 직접 연결하는 구조이다. 서버 머신을 통해서 저장과 forwarding을 하였지만, 서버에 직접 외장장치를 추가하여 병목 현상을 유발할 뿐만 아니라, 대역폭이 제한된다는 점과, 서버 기계의 slot이 제한되므로 확장성이 없다는 단점이 있다.

이에 반해 NAS SAN은 가용성, 확장성, 로드 밸런싱과, 저장 관리 기법의 중앙화라는 장점을 가지고 있다.
- ![image](/images/OS-12-04.png)