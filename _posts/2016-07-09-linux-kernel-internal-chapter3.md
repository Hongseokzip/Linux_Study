---
layout: post
title: "리눅스커널 내부구조 3장"
description: "리눅스커널 내부구조 3장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

CFS (일반 테스크 스케줄링)
    - 테스크를 스케줄링을 할때 cpu에서 단위시간을 기준으로해서 런큐에 존재하는 테스크의 개수만큼 N빵
    - 근데 여기서 우리가 원하는거는 N빵이 아니라 절대적인 시간만큼 할애할 수 있는지. (의문점 1)
        - 일단은 안되는것같음: 근데 어쩌면 구현해서 가능하게 할수도 있을것같음 (?) -> vruntime으로 구현?
    - 컨텍스트 스위칭을 하는 오버헤드가 발생할 때의 지연되는 시간이 고려되는지 알고리즘상에서. (의문점 2)
        - 우리의 추측: 만약에 이런 지연시간을 고려를 한다면 오버헤드에 대한 지연시간을 정확히 알고리즘상으로 만들기 너무 어려울 것 같음. -> 단순히 테스크를 실행하는 시간만을 고려하여 (오버헤드로 인한 지연시간은 고려하지 않음) 스케줄링을 진행할 것으로 추측
    - 스케줄러는 누가 스케줄링을 하는지?
        - 스케줄러가 하는 역할 자체는 그냥 단순히 런큐에 넣을때 뺄때 그때만 작용을 하는것이고 단순히 실행자체는 그냥 런큐에 있는 테스크를 순서대로 쭉쭉 실행하는 것뿐.

왜 RBTree을썼을까? -> minheap도 있는뎅? ㅎㅎㅎㅎ

시간단위를 동적으로 정하는 함수 (__sched_period)
    - nr_running: 실행중인 테스크 개수, sched_nr_latency: 8 
    - sched_nr_latency는 상수인데, 8이 과연 어떻게 정해진것일까? -> 맘대로 실험적으로 정함
    - https://lkml.org/lkml/2010/9/11/123

그룹스케줄링에도 사용자간의 우선순위가 가능할까?
    - limits.conf에서 user/group에 대한 셋팅이 가능한것처럼 보여서 -> Linux-PAM이라는 모듈이 있어서 여기서 뭔가 해주는것처럼 보임
        - https://fossies.org/dox/Linux-PAM-1.3.0/pam__limits_8c_source.html
        - http://bencane.com/2013/09/30/changing-the-default-nice-value-for-a-user-or-group/
    - nice시스템콜 찾아서 group에 대한 셋팅이 별도로 있는지를 찾아보려함: sys_setpriority, sys_nice
        - http://lxr.free-electrons.com/source/kernel/sys.c#L175
