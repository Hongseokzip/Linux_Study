---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

의문 : 가상메모리 주소 매핑을 위한 page table을 여러 개 두는 이유는?? page table을 여러개 두면 메모리가 절약되는 이유는?

http://stackoverflow.com/questions/29467510/how-does-multi-level-page-table-save-memory-space
책 p.122의 그림4.19

해답:
    32bit cpu, 4G만큼의 메모리 환경을 가정.
    32bit짜리 가상주소를 10bit, 10bit, 12bit로 나누어 해석한다.
    첫번째 10bit는 첫번쨰 page table의 offset
    두번째 10bit는 두번째 page table의 offset
    세번째 12bit는, 4K짜리 page에서의 offset

    첫번째 page table의 entry는 두번째 page table의 주소를 가리킨다.
    두번째 page table의 entry는 page의 주소를 가리킨다.

    첫번째 page table은 메모리에 항상 상주하고 있어야 하고
    두번째 page table은 메모리에 항상 있지 않아도 되고, page table 자체도 paging될 수 있다(table의 크기가 4K)

    두번째 page table은 필요할 때 새로 할당된다. -> 따라서 page table을 한개만 둘 때보다 대체적으로 메모리를 절약할 수 있다.

좋은 문서
page table의 크기를 줄이기 위한 고민들
http://pages.cs.wisc.edu/~remzi/OSTEP/vm-smalltables.pdf




cpu에서
L1 cache에 접근하는 데에는 5clock 이하 필요
memory에 접근하는 데에는 50clock 정도가 필요
-> 메모리에 직접 접근하는 건 최대한 피해야 한다

cpu cache의 크기가 작은 이유
    cache에 쓰이는 sdram 가격이 창렬

cache algorithm은 하드웨어적으로 구현되어 있어 수클럭만에 수행 가능