---
layout: post
title: "리눅스커널 내부구조 3장"
description: "리눅스커널 내부구조 3장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

3.24
    - rt_sched_class 2개

sched_class
    - sched_class type으로 만들어진 rt_sched_class, fair_sched_class, dl_sched_class
    - http://lxr.free-electrons.com/source/kernel/sched/sched.h#L1184: sched_class
    - http://lxr.free-electrons.com/source/kernel/sched/rt.c#L2326: rt_sched_class


thread_info vs thread_struct
    - thread_union
    - http://lxr.free-electrons.com/source/init/init_task.c#L25
    - thread_info: http://lxr.free-electrons.com/source/arch/x86/include/asm/thread_info.h#L55
    - thread_struct: http://lxr.free-electrons.com/source/arch/x86/include/asm/processor.h#L374

context_switching
    - http://lxr.free-electrons.com/source/kernel/sched/core.c#L2715
    - switch_to: http://lxr.free-electrons.com/source/arch/x86/include/asm/switch_to.h#L31

task switching 최적화?
    - arm에서 stm, ldm

task switching -> (?)

http://lxr.free-electrons.com/source/arch/x86/include/asm/switch_to.h#L31

signal
    - do_signal: http://lxr.free-electrons.com/source/arch/x86/kernel/signal.c4402031
