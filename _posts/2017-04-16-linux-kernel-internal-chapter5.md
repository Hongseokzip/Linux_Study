---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

# linux kernel study

*2017 04 16, linux version 4.10*

## 인터럽트와 트랩 그리고 시스템 호출

### p.168

- 그럼 PIC는 수신한 펄스를 적절한 번호로 변환하고 -> 적절한 번호 (IDT 의 번호)
- PIC (Programmable Interrupt Controller)
- 굳이 I/O포트를 이용해야하는지? (중간과정이 왜 필요한지)

### p.169

- [do_IRQ](http://lxr.free-electrons.com/source/arch/x86/kernel/irq.c#L212 "do_IRQ") -> [handle_irq](http://lxr.free-electrons.com/source/arch/x86/kernel/irq_64.c#L68) -> [generic_handle_irq_desc](http://lxr.free-electrons.com/source/include/linux/irqdesc.h#L148)
- [irq_desc](http://lxr.free-electrons.com/source/include/linux/irqdesc.h#L51)
- [irqaction](http://lxr.free-electrons.com/source/include/linux/interrupt.h#L110) 


### p.170
- [SAVE_ALL](http://lxr.free-electrons.com/source/include/asm-i386/hw_irq.h?v=2.4.37#L98)
- do_IRQ() 안에서 old_regs로 SAVE_ALL을 대신하는 것 같음 (옛날 버젼에서는 SAVE_ALL을 그대로 사용)
    - [do_IRQ](http://lxr.free-electrons.com/source/arch/x86/kernel/irq.c#L212 "do_IRQ") -> [set_irq_regs](http://lxr.free-electrons.com/source/include/asm-generic/irq_regs.h#L28) -> [__this_cpu_read](http://lxr.free-electrons.com/source/include/linux/percpu-defs.h#L430) -> [raw_cpu_read](http://lxr.free-electrons.com/source/include/linux/percpu-defs.h#L407) -> [this_cpu_read](http://lxr.free-electrons.com/source/include/linux/percpu-defs.h#L494) -> this_cpu_read_1 ... 어쨋든 SAVE_ALL을 하는 것 같음
    
### p.171
- 그림 6.4에서 그림에 1번째 레벨에 있는 트랩과 2번째 레벨에 있는 트랩은 같은 트랩 (첫번째 레벨의 트랩은 좀 더 큰 범위의 트랩..)
 
