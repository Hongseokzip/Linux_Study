---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

1) NUMA 에서 pg_data_t 의 pgdat_list 가 왜 이중포인터로 되어있을까? 
 - 우리가 착각했다. 실제로는 2차원 배열이 아니라, 그냥 리스트 하나에 대한 포인터 배열이다. (extern pg_data_t *pgdat_list[MAX_NUMNODES];)
 - 그렇다면 왜 포인터 값이 들어갔는가? 동적으로 사용을 하기 위해서 포인터로 들어갔는데, CPU 갯수가 바뀌는 것(정확히는 뱅크의 갯수가 바뀌는것)에 대해 대응이 가능하기 때문에 이렇게 고려하지 않았나.
 - MAX_NUMNODES : (1<<CONFIG_NODES_SHIFT) 이거는 시스템 상에서 최대로 설정한 값이다. 그런데 왜 이렇게 쉬프트 연산을 해뒀을까?
 - 아마 지금까지 헷갈린 이유는 pgdat_list 의 네이밍이 pgdat_array 가 아니라서 ㅋㅋㅋ
 - 자문이 필요한 부분인거 같다.
  [ Question. NUMA 에서 CPU 와 뱅크..노드와의 관계에 대해 공학적인 설명이 필요 ]

2) ZONE_DMA 가 있는 이유? http://hooneyo.tistory.com/entry/%EC%98%81%EC%97%ADZone
 - 반면 x86아키텍쳐에서 ISA 디바이스는 모든 32비트 주소 공간으로 DMA를 할 수가 없는데(할 수 있다면 4GB), 그 이유는 ISA 장치(24비트까지만 접근가능) 가 물리적 메모리의 처음 16MB만을 접근할 수 있기 때문이다. 따라서 x86에서는 ZONE_DMA는 0~16MB 범위의 모든 메모리로 구성된다. 즉, 24비트까지만 접근가능하니깐 16M 까지 커버되는거라

3) pglist_data 에 왜 node_zones는 struct zone node_zones[MAX_NR_ZONES]; 로 고정되어있을까?
 - linux/mmzone.h 에 enum zonetype 으로 같이 정의 되어있음. ( http://lxr.free-electrons.com/source/include/linux/mmzone.h#L267 )
 - MAX_NR_ZONES 개의 칸을 만들어두고 여기서 ZONE_* 중에 선택해서 접근하려고 하는듯.
  * 참고 : MAX_NR_ZONES 가 2.6버전 이하에서는 3으로 고정되어있었는데, 3.x 부터는 enum 으로 접근

4) 각 노드마다 사용하는 ZONE 이 다를 수 있을까?
 - 아직 모르겠다. 자문각.

***
 - 책에 98페이지에 struct pglist_data * pgdat_list[MAX_NUMNODES]; 라고 적혀있는데 이게 아니고 nodes_list 임.


http://lxr.free-electrons.com/source/mm/bootmem.c?v=2.4.37#L52 를 참조하면 arch 에 상관없이 pgdat_list 를 set 한다.


***
 - 32bit 와 64bit 에서 zone 사이즈 설명
 https://utcc.utoronto.ca/~cks/space/blog/linux/KernelMemoryZones
  ( 64bit 에서는 DMA32 가 4GB 까지 이고, 그 이상부터 NORMAL 시작 )

