---
layout: post
title: "리눅스커널 내부구조 1장"
description: "리눅스커널 내부구조 1장"
comments: true
keywords: "linux kernel"
---

NFS
    - reliable operation in the presence of failure
    - 파일을 보낼때 꼭 가도록 보장 (신뢰성이 보장)
    - 실패하면 재전송을 할 수도 있음
RFS
    - file system의 semantic을 유지시키는게 목적
    - 통채로 보내버린다.

    -> unix/posix파일 시스템의 semantic을 완벽히 지원 (file locking)
    -> 원격으로 디바이스같은거를 마운팅 가능하다
    -> transparent access to files (사용자가 정확히 파일의 경로를 몰라도 접근이 가능)

micro kernel // monolithic
    - micro kernel: 커널 구조에서 꼭 필요한 기능들만 골라서 만들어낸 것 (인터럽트 처리, 페이지 폴트, 스케줄링, 컨텍스트 스위칭, IPC 등)
    - monolithic: 커널 구조에서 꼭 필요한 기능들 이외에 여러가지 더 필요한 것들도 다 들어가있음 (커널의 크기가 커짐)

module
    - example (driver)
    - 만약에 어떤 인터페이스가 있으면 이것을 사용자가 디바이스를 사용하기위해서는 단순히 컴퓨터에 연결하는것만으로는 디바이스가 인식될뿐 유저레벨에서는 접근이 불가
    - 디바이스 드라이버를 통해 인터페이스에 접근할 수 있음 -> 유저레벨에서 시스템레벨에 있는 디바이스에 접근하려면 system call interface를 거쳐야만하는데 이것을 가능하게 해주는것이 디바이스 드라이버이고 이것이 module의 일종

resource manager
    - physical resource / abstraction manager

CPU - Task
Memory - page/segment
Network - packet/protocol

fig 2.1
    - network manager랑 hardware interface랑 왜 연결되어있을까? (pass)

linux에서 어떻게 단순히 리부팅만 한다고 해서 커널이 업데이트가 가능한지?
    - 윈도우에서는 불가능한데 왜 linux는 가능한지.
    - 윈도우랑 linux를 비교하는것이 말이 안됨. (오히려 windows NT커널과 linux kernel을 비교하는것이 맞음;ubuntu와 windows의 비교가 올바른 비교)
    - 재부팅하는것만으로 단순히 업데이트가 가능한지? (그냥 단순히 커널 빌드된 이미지를 교체하는것만으로도 linux kernel을 업데이트할 수 있음)

y, n, m
    - kernel구성요소를 모듈로 뺀다. (모듈로 빼면 필요할때만 사용할수있음;커널로 들어가면 항상 실행되있는것)

build
    - bzImage -> gzip
    - boot -> gzip 압축해제 -> lds에 써있는 명세대로 매모리에 매핑됨. 

autoconf
    - .config로 만들어진 파일 (menuconfig를 통해 만든 파일)은 실제 컴파일단계에서는 사용되지않음 (autoconf파일안에 define으로 들어가있음)
    - m, y, n등의 파일은 makefile에서 obj-(.config의 변수)로 치환되어 들어가짐

