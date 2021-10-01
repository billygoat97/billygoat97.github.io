---
title: "운영체제(OS) 10장 File System"
except: "운영체제(OS) 에서 실제 File을 저장하는 File System에 대해서 알아보자"
categories:
  - 운영체제
tags:
  - File
  - Inode
  - Dentry
  - Open() System Call
  - Allocation Methods
  - VFS
  - Ext2
toc: true
toc_sticky: true
---

# 10장 File System
## <span style = "color:powderblue"> ※ 파일 시스템
### <span style = "color:powderblue"> ※ 파일 시스템의 필요성
- 장기 정보 보관 -> 파일 시스템 (메인메모리는 매우 단기임)
- File; 이차 stoarge에 저장된 collection of related information
<br><br><br>

### <span style = "color:powderblue"> ※ os가 파일 타입 아는 방법?
- 윈도우; extension
- unix; extension도 있지만, 8bit byte로 define
<br><br><br>

### <span style = "color:powderblue"> ※ 파일 접근
- sequential access (순차적)
- direct access (랜덤 엑세스)
- record access (기억된 number)
- index access ( 인덱스 db로 접근)

<br><br><br>

### <span style = "color:powderblue"> ※ 파일 operation
- open syscall
- directories (경로)
- 경로는 파일 정렬하는 것에 방법 제공
- 계층적 경로 시스템
경로의 내부;

- ![image](/images/OS-10-01.png)

경로에 관해서;
- tree structured directories: 트리 구조로 경로 만듬
- 혹은 acyclic graph directories(트리가 있긴 한데 각각의 접근이 다른 노드에서도 가능)

- 파일 시스템은 protection system을 가지고 있어야 한다. 
- 즉, 허용이 되냐 안되냐를 define
- read, write execute하는데, RWX 비트로 표시 (chmod 777)
<br><br><br>

### <span style = "color:powderblue"> ※ 파일 시스템을 implement하는 법;
- 경로에 파일이 어떻게 보관되어 있는지?
- 디스크 공간이 관리되는지 (어떻게)
- 효율적으로 이용되게 하는지?

- boot control block (os 부팅 정보 포함)
- partition control block (파티션을 컨트롤 하는 블록)
- directory structure (file name, inode number), 
- file control block; 파일에 대한 detail
- individual files; inode
계층적 구조

메모리 정보는 파일 시스템 관리와 성능 향상을 캐싱을 통해서 이용된다.
<br><br><br>

### <span style = "color:powderblue"> ※ in memory structures
메모리 안에 정보는 다음과 같이 접근이 된다
- in memory partition table (마운트된 파티션 정보 포함)
- in memory directory structure (최근에 접근한 경로에 대한 정보)
- system wide open file table (열려있는 파일에 대한 fcb의 복사 저장)
- per process open file table; 열려 있는 파일에 대한 포인터 제공
<br><br><br>


### <span style = "color:lime"> ※ open 시스템콜
1. open은 file name을 file system에게 전달
2. directory 구조는 file 이름에 대응하는 것을 위해 search 됨
3. 찾아지면 FCB를 복사하여 system wide open file table에 저장
4. per process open file table에 entry가 생성이 되며, system wide open file table에 대한 pointer이  그것이다.
5. open call은 per process file system table에서 pointer을 받아온다.
<br><br><br>

## <span style = "color:powderblue">※ Allocation Methods
### <span style = "color:powderblue">※ ※ Contiguous allocation: 연속적 배치
장점;
- 파일을 디스크에 연속적 블록에 자리한다.
- 시작 지점과 길이만 필요하다
- 효율적인 random access가 필요하다
단점;
- 파일 크기를 키울 수 없다
- 필요 공간을 과대평가한다
특징;
- CD롬이 보통 이용된다.

<br><br><br>

### <span style = "color:powderblue">※ Linked allocation
- 파일이 각각 linked list로 이용된다.
장점;
- 시작 지점만 필요하다
- 공간 낭비 없음
단점; 
- 비효율적인 random access이다.
- 포인터의 자리가 필요하다.
- 포인터에 의존 (하나라도 유실되면 다 유실됨)
<br><br><br>


### <span style = "color:powderblue">※ Indexed allocation
- 모든 포인터를 인덱스 블록으로 가져오는 구조
- 인덱스 테이블이 필요하다
장점;
- 효율적인 random access가 필요하며, 외부 단편화가 일어나지 않는다
<br><br><br>

### <span style = "color:powderblue">※ inode(유닉스 기반 indexed allocation)
- 모든 파일에 존재하며, 고정된 크기를 갖고 메인 메모리에 오래 있을 수 있다.
- 작은 크기의 파일은 indirection이 적거나 없으며, 파일의 최대 크기는 모든 applicaiton을 커버 가능
<br><br><br>

## <span style = "color:powderblue">※ 남는 공간 활용 (free 공간을 어떻게 알려주냐)
### <span style = "color:powderblue">※ ※ bit vector, bitmap
- 연속적인 파일을 갖기 쉬움
- bitmap은 추가 공간을 요구함
- block number을 계산을 필요함

<br><br><br>


### <span style = "color:powderblue">※ linked list(free list)
- free disk block을 linked list 구조로 함
- 연속적 space를 쉽게 가지지는 못함
- 자원낭비 x
<br><br><br>

### <span style = "color:powderblue">※ grouping
- 첫 번째 freeblock에 뒤의 n free block들의 주소 갖고 있으며, 끝나는 애가 또 n free blocks들을 갖고 있음
<br><br><br>

### <span style = "color:powderblue">※ counting
- n개의 free address를 갖기보다는 첫 번째 free block주소를 갖고 n개의 연속적인 주소 공간을 갖고 있다는 정보를 갖는다

<br><br><br>

### <span style = "color:powderblue">※ VFS (가상 파일 시스템)
- systemcall을 처리하는 커널 소프트웨어
- 유저 모드 프로세스에 같은 인터페이스 제공
- 다른 파일 시스템 implementation
<br><br><br>

### <span style = "color:powderblue">※ 파일 접근 하기 위한 개념
- superblock (파일 세스템에 대한 정보)
- inode (파일 정보)
- file (열려있는 open file)
- dentry (주소 entry, 주소에 열려있는 entry)

super block =>dentry ->inode
file이 dentry로 슝슝
<br><br><br>

### <span style = "color:powderblue">※ PCB
- 태스크 구조의 접근
- task struct ->fs_struct -> dentry
<br><br><br>

### <span style = "color:powderblue">※ 디스크 캐싱
- 버퍼 캐시; 파일시스템을 통한 I/o 연산은 buffer cache 이용, file 사용의 locality 활용, 모든 프로세스가 공용으로 사용, 매우 작지만 매우 효율적이라는 장점이 있다. 그렇기 때문에 replacement algorithm에따른 block을 배치해야한다.
- page cache; page frame을 캐싱의 관점에서 설명하는 거승로 memory mapped I.o를 쓰는 경우에는 page cache 사용
- 요즘은 page, buffer 가리지 않고 함께 쓰는 unified buffer cache 이용한다.
<br><br><br>

### <span style = "color:powderblue">※ Ext2 파일 시스템
- 리눅스에서 Ext2 파일 시스템이란 확장 파일 시스템으로, 리눅스의 파일 시스템이다.
- 디렉토리 구조는 테이블이며, 모든 파일이나 디렉토리는 아이노드로 표현한다. 
- ext2 파티션의 첫 블록은 boot sector이며, 나머지는 모두 block group이다. 
- 블록 그룹들은 내부에 수퍼 블록, 그룹 descripter들을 각각 갖고 있고, 이는 파일 단편화를 줄이게 된다. 
<br><br><br>
<br><br><br>

# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . FCB가 무엇인지?** </span>
FCB는 파일 제어 블록으로 파일에 대한 정보를 구성하는 저장구조로 파티션에 위치한다. fcb는 파일을 open할 때 주기억장치로 이동이 되며, read할 때 이를 이용하여 datablock들을 활용한다.

## <span style = "color:Orange"> **Q2 . open(), 일어나는 과정 서술** </span>
open 시스템콜
1. open은 file name을 file system에게 전달
2. directory 구조는 file 이름에 대응하는 것을 위해 search 됨
3. 찾아지면 FCB를 복사하여 system wide open file table에 저장
4. per process open file table에 entry가 생성이 되며, system wide open file table에 대한 pointer이  그것이다.
5. open call은 per process file system table에서 pointer을 받아온다.

## <span style = "color:Orange"> **Q3. Allocation method에 대해 설명** </span>
Allocation method는 3가지 종류가 있는데, 첫 번째는 continous allocation으로, 모든 파일은 연속적인 블록을 디스크에 저장한다는 것입니다. 이는 시작 지점과 길이만 알면 된다는 장점이 있지만, 단편화 문제가 야기될수 있으며, 파일 크기를 키울 때 다른 공간을 찾아 떠나야 한다는 단점이 있습니다. Linked allocation은 각각의 block이 그다음 블록에 대한 정보를 갖고 있어 단편화 문제를 제거하였으며, 시작 주소만 있으면 된다는 장점이 있습니다. 그러나 포인터에 의지하기 때문에, 하나의 포인터만 소실되도 전체적인 정보를 잃는다는 점이 있습니다. 이러한 두 단점을 개선한 것이 index allocation으로, 하나의 block에 모든 연결되어있는 index의 정보를 갖고 있어, index block에서 모두 찾아갈수 있으며, 단편화 없이, 인덱스 블록만 유효하면 정보의 소실이 적다고 할 수 있습니다. 

## <span style = "color:Orange"> **Q4. inode란 무엇인가** </span>
inode란 index node로, 모든 파일에 존재하며, 파일을 기술하는 디스크상의 데이터 구조로, 데이터 블록이 디스크 상의 어느 주소에 위치하고 있는가와 같은 파일의 중요 정보 포함한다. 이는 매우 작기 때문에 메인 메모리에 오랫동안 존재할 수 있다. 모든 파일에 존재하기 떄문에 inode의 숫자는 파일 시스템이  갖고 있을 수 있는 최대 파일 수가 된다.

## <span style = "color:Orange"> **Q5. Free space management에 대해 설명** </span>
비트맵과 비트 벡터의 방법이 있는데, 차례대로 순회하며, 이 자리가 비었는지 확인을 할 수 있다. 이는 연속적인 file을 쉽게 get 가능하지만, bitmap은 추가 공간을 요구하게 된다. linked list(free list)는 빈 disk block을 link하여 다음 포인터를 빈 공간을 가르키게 된다. 이것은 공간 낭비는 적지만, 연속 데이터를 쉽게 가지지는 못한다. grouping은 여러개의 블록(n개)를 한 덩어리로 취급하여 n개의 빈 block들의 주소를 첫 번째의 빈 블록에 위치하게 한다. 마지막으로는 counting이 있는데,  주소 자체를 각각 기억하기보다는 첫 번째 주소를 저장하여 그 주소와 연속되게 몇 개의 free block이 있는지 확인 할 수 있는 방법이 있다.

## <span style = "color:Orange"> **Q6. VFS에 대해 설명** </span>
쉽게 말해 유저 모드 프로세스에 같은 인터페이스를 제공하게 하여 모두 동등하게 이용할 수 있도록 관리하는 시스템이다. (minix, nfs, ext2,... 등을 하나의 vfs로 묶어서 이용)
리눅스에서 VFS는 superblock, inode, file, dentry 4가지로 구성되고, superblock은 마운트된 파일 시스템의 정보를, inode는 특정 파일의 일반 정보를 갖고 있으며, file은 open file과 프로세스의 interaction 정보를 저장, dentry는 그냥 경로이다.
쉽게 말해 superblock이 근간(전체 c,d드라이브), dentry가 경로, inode가 파일, file이 task(process)가 interact하고 있는 내용에 대한 정보를 의미한다

## <span style = "color:Orange"> **Q7. 디스크 캐싱에 대해 설명** </span>
디스크 캐싱에 대해
버퍼 캐시; 파일시스템을 토한 I/o 연산은 buffer cache 이용, file 사용의 locality 활용, 모든 프로세스가 공용으로 사용, 매우 작지만 매우 효율적이라는 장점이 있다. 그렇기 때문에 replacement algorithm에따른 block을 배치해야한다.
page cache; page frame을 캐싱의 관점에서 설명하는 거승로 memory mapped I.o를 쓰는 경우에는 page cache 사용

요즘은 page, buffer 가리지 않고 함께 쓰는 unified buffer cache 이용한다.

## <span style = "color:Orange"> **Q8. Ext2 파일 시스템에 대해 설명** </span>
리눅스에서 Ext2 파일 시스템이란 확장 파일 시스템으로, 리눅스의 파일 시스템이다. (크기고 뭐고 패스.. ) 디렉토리 구조는 테이블이며, 모든 파일이나 디렉토리는 아이노드로 표현한다. ext2 파티션의 첫 블록은 boot sector이며, 나머지는 모두 block group이다. 블록 그룹들은 내부에 수퍼 블록, 그룹 descripter들을 각각 갖고 있고, 이는 파일 단편화를 줄이게 된다. 
