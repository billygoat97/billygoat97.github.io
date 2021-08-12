---
title: "운영체제(OS) Processes"
except: "운영체제(OS) 에서 가장 핵심적인 개념 프로세스에 대해 알아보자."
categories:
  - 운영체제
tags:
  - processes
  - process
  - process state
  - process switching
  - mode switching
toc: true
#toc_sticky: true
---

# 3장 Processes (프로세스)
## ※ 프로세스란 무엇인가?
- instance of a running program으로, 실행중인 프로그램이라고 한다.
- 프로세스는 이미지와 프로세스 컨택스트로 분류된다.

### ※ 이미지(image)는

- code segment: read only (instruction 코드)
- initialized data segment
- uninitialized data segment: 프로그램 시작 전엔 0으로 됨
- stack : 변수, 임시 변수, return 주소값, 등등
- heap : 동적 메모리

으로 구성되어 있다.

### ※ PCB (process control block) & PD(process descriptor)
커널 데이터 구조에서 가장 essential한 부분: 
- 모든 프로세스에 존재하며
- pcb는 상기 서술했던 이미지와 프로세스 컨택스트로 구성되어 있다.
- 프로세스 컨텍스트는 프로그램 컨텍스트와 커널 컨택스트로 구성되어있다.

### Process State
