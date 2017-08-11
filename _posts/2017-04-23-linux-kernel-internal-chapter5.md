---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

# linux kernel study

*2017 04 23, linux version 4.10*

## 인터럽트와 트랩 그리고 시스템 호출

### p.172

- syscall table 의 경로가 바뀜(../entry/..). Linux/arch/x86/entry/syscalls/syscall_32.tbl
- ~/include/linux/syscalls.h 에 정의한 내용 중에 예를 들어, sys_close 같이 커널에서 이미 구현해둔 함수를 새로운 모듈이 다른 파일에서 덮어씌워 EXPORT_SYMBOLS 하면 어떻게 될까?
- API 와 ABI 에 대한 차이?
 [Wiki] In computer software, an application binary interface (ABI) is the interface between two program modules, one of which is often a library and/or operating system and the other one is usually an application created by a regular programmer.
