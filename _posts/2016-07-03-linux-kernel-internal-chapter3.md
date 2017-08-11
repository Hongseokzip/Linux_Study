---
layout: post
title: "리눅스커널 내부구조 3장"
description: "리눅스커널 내부구조 3장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

n개의 CPU를 갖는 시스템에서는 임의의 시점에 최대 n개의 테스크가 실제 실행상태에 있을 수 있음
    - n코어이면 n개의 테스크가 실행상태에 있을 수 있음
    - 하이퍼스레딩도 포함 (os가 지원해야함)
    - http://jake.dothome.co.kr/category/linux/page/24/
    - http://lxr.free-electrons.com/source/arch/x86/kernel/cpu/common.c#L556 -> http://lxr.free-electrons.com/source/arch/x86/kernel/cpu/intel.c#L440
    - http://sfeg.tistory.com/217

TASK_DEAD 
    - wait() -> EXIT_ZOMBIE -> EXIT_DEAD -> 자원 정리하고 완전히 종료

데몬만드는 방법
    - 부모프로세스 만들고 부모를 죽이면 자식이 init에 붙어서 계속 돌아감
    - 이런식으로 만들 수 있는게 고아가 될 수 있는 자식 프로세스를 init으로 붙기때문에 가능한것

wake_up // wake_up_new_task
    - spin_lock vs raw_spin_lock
    - spin_lock vs mutex
    - spin_lock? (넘어감)
    - http://lxr.free-electrons.com/source/kernel/sched/core.c#L2421
    - http://lxr.free-electrons.com/source/include/linux/spinlock.h#L205
    - http://lxr.free-electrons.com/source/kernel/sched/wait.c#L89
    - http://lxr.free-electrons.com/source/include/linux/spinlock.h#L335
    - http://jake.dothome.co.kr/spinlock/

왜 2.4에서는 tasklist를 이중연결리스트를 사용했을까?

실시간 스케줄링에서 DEADLINE은 확정적으로 이용가능한지를 알 수 있는데 다른 일반 테스크에는 영향이 없을지?

DEADLINE테스크에서 DEADLINE이 지난 이후에 실행하는것이 의미있는지?
    - RBTree에서 앞서 실행에 실패하면 그냥 그 뒤에꺼가 자동으로 실행하는지?
    - HARD Realtime vs SOFT Realtime
        HARD Realtime
        - 테스크 실행에 실패하면 그냥 자체가 죽어버림
        SOFT Realtime
        - 실행에 실패해도상관없음 (ex: 동영상)
    - http://www.drdobbs.com/soft-real-time-programming-with-linux/184402031
