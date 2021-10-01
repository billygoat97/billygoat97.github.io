---
title: "운영체제(OS) 11장 IO System"
except: "운영체제(OS) 에서 Input Output을 다루는 IO System에 대해서 알아보자"
categories:
  - 운영체제
tags:
  - IO Port
  - IO Interface
  - Device Controller
  - Polling
  - Spooling
  - DMA
  - Burst Mode
  - Cycle Stealing
toc: true
toc_sticky: true
---

# 11장 IO System
## <span style = "color:powderblue"> ※ IO System
### <span style = "color:powderblue"> ※ I/O 시스템 구성
- cpu 기준에서는 io bus, io address 두가지가 있음
- 디바이스 기준에는 하드웨어 컨트롤러
<br><br><br>

### <span style = "color:powderblue"> ※ 하드웨어 디바이스와 interact 하는 법
- 폴링, interrupt driven, DMA (3가지)
- 데이터 통신을 위해서는 cpu 메모리 iocontroller을 서로 연결해야 함.
<br><br><br>

### <span style = "color:powderblue"> ※ I/O 구조
### <span style = "color:powderblue"> ※ io port;
- io bus에 연결되어 있는 모든 디바이스는 io 주소가 있다(ioport)
- 각 디바이스의 io port들은 특정 레지스터로 이루어져 있다.
- cpu와 ioport 간에 데이터 전송은 in, ins, out, outs들로 진행된다

<br><br><br>

### <span style = "color:powderblue"> ※ io 인터페이스
- io port와 디바이스 컨트롤러 사이에 삽입되어있는 하드웨어 서킷으로 interpreter의 역할을 수행하는데 io port와 device 간에 instruction을 변환시켜줌 (키보드, 마우스..)
<br><br><br>

### <span style = "color:powderblue"> ※ 디바이스 컨트롤
- io 인터페이스에게서 받은 high level command를 interpret하고, 디바이스를 작동하도록 전기 신호 보냄
<br><br><br>

### <span style = "color:powderblue"> ※ 폴링
- cpu는 디바이스에게 attention을 달리고 함(polling);
- 장점; 소프트웨어는 control 하에 있음
- 단점; 높은 cpu 활용도 있으면 그렇게 좋지는 않음.
<br><br><br>


### <span style = "color:powderblue"> ※ interrupt driven
- io device는 attention이 필요하면 인터럽트 건다
- 인터럽트는 다양한 디바이스간에 공유 가능
- 장점; cpu는 꼭 필요할때만 device에 attend함. 더 효율적
- 단점; 인터럽트는 프로그램 실행을 천천히 하게 함, 오버로드

## <span style = "color:powderblue">※ DMA direct Memory Access
- 바로 메모리에 접근; cpu를 제끼고 바로 io랑 메모리 접근
- 매우 큰 데이터 movement를 제끼기 위해 (대신 DMA 장치가 있음) -> cpu 효율 증진


- 고속 io 장치의 경우 빈번한 인터럽트 발생 => DMA를 이용해 최소화
- cpu 명령 – 버스 request – bus grant – data tarnsfer – interrupt
- 동작모드
- 사이클 스틸링; cpu가 DMA에 우선순위 양보(1사이클 루팡, 인터럽트는 필요한 만큼 정지)
- 버스트 모드; DMA 가 버스 사용권 획득시 데이터 전송 완료까지 버스 사이클 독점

<br><br><br>

### <span style = "color:powderblue"> ※ IO 하드웨어 접근
- 직접 io instruction
- 특별 io instruction 이용하여 io port address 넣음.
<br><br><br>


### <span style = "color:powderblue">※ 메모리 mapped io
- 디바이스 컨트롤 레지스터는 프로세스 주소 공간에 저장됨.
- cpu는 io requests를 data transfer instruction을 이용해 실행
<br><br><br>

### <span style = "color:powderblue">※ I/O 소프트웨어
Application IO interface;
- 디바이스가 너무 다양하면, os가 어떻게 편리한 io interface를 주는가?
목표:
- 기기 독립성(어느 기기나 접근 가능)
- 동일한 이름 형식
- 효과적인 오류 처리 (마치 하드웨어)
- 퍼포먼스
<br><br><br>

## <span style = "color:powderblue">※※ 디바이스 드라이버
- 마우스 연결하며 나오는 그런 드라이버 (디바이스를 os에 적합하게 연결 해주는 드라이버)

<br><br><br>


### <span style = "color:powderblue">※ 디바이스 독립 io 소프트웨어
- 유닉스에서는 디바이스도 특정 파일로 취급
- 버퍼링이 이래서 일어나는 것; 줄서서 대기하는 현상
<br><br><br>

### <span style = "color:powderblue">※ 스풀링
- io devices들을 멀티프로그래밍 시스템에서 어떻게 하느냐
- 프린터기가 한 예인데, cpu가 프린터 출력을 직접 제어한다면 프린터의 인쇄 작업이 끝날때까지 다른 일을 못하게 하게 됨. 그래서, 프린터로 전송될 데이터를 하드디스크에 잠시 저장하고 cpu는 다른 일을 처리하면서 필요할 때마다 조금씩 프린터로 보내주는 방식으로 cpu 사용효율 높임
<br><br><br>

### <span style = "color:powderblue">※ I/O로 인한 병목구간 성능 향상

모드 스위치 횟수 줄이는 방법
- 데이터 복사 감소
- large transfer, smart controller 이용해 interrupt 감소
- DMA 사용
- CPU와 기기 컨트롤로 동시 operation
- CPU, BUS I/O performance간에 balance를 얻어 처리량 극대화

<br><br><br>
<br><br><br>

# <span style = "color:Yellow">**※ 핵심 내용**
## <span style = "color:Orange"> **Q1 . io port io interface device controller 특징, 관계?** </span>
io bus에 연결되어 있는 모든 디바이스는 io 주소가 있다(ioport). 각 디바이스의 io port들은 특정 레지스터로 이루어져 있으며, cpu와 ioport 간에 데이터 전송은 in, ins, out, outs들로 진행된다. 그런데 이것이 바로 cpu에 바로 전달되기 힘든데, 이를 위해서 io 인터페이스를 이용한다. 인터페이스는 io port와 디바이스 컨트롤러 사이에 삽입되어있는 하드웨어 서킷으로 interpreter의 역할을 수행하는데 io port와 device 간에 instruction을 변환시켜줌으로서 디바이스에게 적합하게 만들어 준다. 이를 작동하도록 전기 신호를 보내는 것이 디바이스 컨트롤이다.

## <span style = "color:Orange"> **Q2 . 폴링 인터럽트, dma 특징,비교** </span>
폴링은 cpu는 디바이스에게 attention을 달리고 요구하는 것으로 강제하지는 않는다. 장점은 소프트웨어가 control 하에 유지된다는 점이며, 단점은 cpu가 활용도가 높으면 자신의 차례가 쉬이 오지 않는다는 점이 있다. 인터럽트는 io device가 attention이 필요하면 인터럽트를 거는 것을 말하며, 다양한 디바이스 간에 공유가 가능하다. 장점은 cpu가 필요할때만 효율적으로 device attend한다는 점이 있지만, 프로그램 실행을 천천히 하게 되는 단점이 있다. DMA는 이러한 점을 절충한 내용으로 바로 직접적으로 메모리에 접근하는 것을 의미한다. 여기서 사이클 스틸링과 버스트 모드가 등장하는데 사이클 스틸링은 한번의 사이클만 이용하여 cpu 활용도를 덜 낮추는 점이 있으며, 버스트 모드는 다 실행완료할때까지 사이클을 뺏는 것이다.


## <span style = "color:Orange"> **Q3. interrupt handler에 대해 설명** </span>
인터럽트 핸들러는 인터럽트를 처리하기 위해 커널이 실행하는 함수로, 인터럽트마다 핸들러가 존재한다. 방식은 인터럽트가 발생했을 때 커널이 호출을 하며, 인터럽트 컨텍스트라는 특수 상황에서 실행이 된다. 인터럽트는 언제든지 발생가능하므로 중단된 프로세스를 다시 재개하기 위해 핸들러는 짧고 간단하게 구성되어야 한다.

## <span style = "color:Orange"> **Q4. spooling이란 무엇인가** </span>
io devices들을 멀티프로그래밍 시스템에서 어떻게 하느냐는 관점에서 등장하는 것이 스풀링이다. 프린터기가 한 예인데, cpu가 프린터 출력을 직접 제어한다면 프린터의 인쇄 작업이 끝날때까지 다른 일을 못하게 하게 된다. 그래서, 프린터로 전송될 데이터를 하드디스크에 잠시 저장하고 cpu는 다른 일을 처리하면서 필요할 때마다 조금씩 프린터로 보내주는 방식으로 cpu 사용효율 높이는 방식이다.
