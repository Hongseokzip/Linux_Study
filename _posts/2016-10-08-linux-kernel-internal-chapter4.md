---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

시작하는 질문.
Q1. RAM 슬롯마다 ZONE들이 있느냐? 그러니깐, 1번 슬롯에 ZONE_DMA, ZONE_NM, ... 있고, 2번 슬롯에도 ZONE_DMA, ZONE_NM,.. 이렇게 있느냐?
 => A. 노드라는거는 RAM 여러개 합쳐가지고 구성이 될 수 있잖아요? 그 노드 하나 안에서 ZONE_DMA, ZONE_NM, .. 이렇게 나뉘어 지는거다.

node_start_pfn

http://stackoverflow.com/questions/23626165/what-is-meant-by-holes-in-the-memory-linux
hole
    1. 부팅 등을 위해 예약된 메모리공간들. 할당할 수 없는 메모리 공간들
    2. HW메모리와 가상메모리간에 매핑되지 않은 메모리공간. 잘못된 참조를 방지하기 위해 일부러 매핑하지 않음. ex) null pointer exception

의문.
    node에 node_start_pfn 값을 초기화하는 순간??
    mm/bootmem.c : init_bootmem_core() 에서 하는 것 같은데.. 이놈을 누가 호출하는가?


가상메모리
region = segment

region 관리를 위한 vm_area_struct를 red-black tree로 관리함


linux kernel의 red-black tree 원리
/include/linux/rbtree.h
rb_node 구조체의 __rb_parent_color의 의미
    마지막 2bit는 현재 노드의 red-black 구분을 위한 비트
    나머지 비트는 부모 노드의 주소
    aligned(sizeof(long)) 예약어를 이용해, 할당되는 메모리 주소의 맨 뒤 2비트는 항상 0임이 보장됨
