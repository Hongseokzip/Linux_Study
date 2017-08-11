---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

가상 메모리 훑어보기

의문1
    text영역에 들어갈 instruction들의 덩어리가 너무 큰 경우(4G를 넘는다든지) kernel은 이놈을 어떻게 처리할까?

의문2
    arch_get_unmapped_area() 함수의 return type이 뭘까?
    -> 주소값!!
    arch_get_unmapped_area() -> 인자가 여러개 있는데 이게 각각 무엇을 의미할까???
    file 인자는 멀까? NULL를 받을 때도 있는데, 이건 뭘 의미하노??


vm_area_struct 몇개가 가상주소를 관리하고 있을때, 인접한 메모리의 속성이 같다면 인접한 vm_area_struct들은 병합되어 관리될 수 있다.
    -> 그림 4-13 참조


페이지 프레임 : 물리메모리의 최소단위
페이지 : 가상메모리의 최소할당단위

가상메모리주소 -> 물리메모리주소로 변환을 위한 page table의 주소는 CR3 레지스터에 들어있다. (x86의 경우)
https://en.wikipedia.org/wiki/Control_register#CR3
https://en.wikipedia.org/wiki/Translation_lookaside_buffer#TLB_miss_handling

의문 : 가상메모리 주소 매핑을 위한 page table을 여러 개 두는 이유는??
