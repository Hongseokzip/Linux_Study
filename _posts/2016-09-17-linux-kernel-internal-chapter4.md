---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

watermark (min, low, high)
    lazy buddy가 작동하기 시작하는 시점과 작동을 멈추는 시점을 정의하는 값
    min < low < high
    메모리 해제(병합) 작업에 오버헤드가 심함 -> 해제 작업을 가급적 뒤로 미루는 것이 lazy buddy -> 해제작업을 도대체 언제 할 것인가?
    watermark min/low/high 값을 기준으로 kswapd(pageout deamon)이 작업을 시작하거나 쉰다

슬랩 할당자 (slab allocator) - p.110 그림4.10 참조
    cache : page frame을 나누고 object를 할당하는 정책
    slab : 2의 승수 byte로 나뉘어진 page frame
    object : 나뉘어진 slab의 한 조각

    의문
    어차피 2의 승수로 나뉘어있는데 왜 리스트로 관리해서 비효율적인 linear search를 하는가?
     => 갱남 왈 : 2^5 ~ 2^17(2^22) 의 12~17 개의 정렬된 리스트를 바이너리 서치하는건 오히려 리니어서치보다 오버헤드가 더 걸릴 수도 있지 않을까?

    slab 일 때, 
    kmem_cache : http://lxr.free-electrons.com/source/include/linux/slab_def.h#L10
        node 변수가 kmem_cache_node 포인터 배열 http://lxr.free-electrons.com/source/include/linux/slab_def.h#L87
    kmem_cache_node : http://lxr.free-electrons.com/source/mm/slab.h#L417

