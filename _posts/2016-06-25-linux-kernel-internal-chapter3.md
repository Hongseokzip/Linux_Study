---
layout: post
title: "리눅스커널 내부구조 3장"
description: "리눅스커널 내부구조 3장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

task
    - Linux에서의 기본 자원 관리의 단위가 되는 구성 요소
    - process를 구성하는 단위
    - tid (task id), tgid(task group id) = pid(process id); process id는 POSIX표준때문에 생김

thread
    - thread를 생성할 경우 task_struct가 하나 생상됨
    - 생성된 task_struct는 process를 구성하는 하나의 단위로 들어감, thread를 생성한 다른 task_struct와 connectivity가 생성 (task_struct tree)
    - 결국은 'thread는 task이다' 라고 말할 수 있음 (thread는 곧 task_struct이니까)

thread를 생성하는 함수가 clone인데 clone이라고 부르는것이 맞는건지
    - thread를 생성하는것도 task_struct를 생성하는것이기때문에 그냥 clone이라고 부르는것이 오히려 자연스러움

fork를 했을때 대상 프로세스의 모든 task_struct가 그대로 복사되는건지
    - do_fork안에서 fork를 하는 대상 프로세스에 있는 모든 task_struct의 연관된 모든거를 그대로 가져와서 init을 하니까 가능함 (확인은 안해봄)
    - printk로 직접 do_fork안에서 로그 찍어보면서 확인해보는것도 괜찮을듯

task -> 생성되는 memory segment안에 구분되어있는 stack, heap, data, text등의 세그먼트의 가상메모리 주소를 task_struct안에서 각각 별도로 저장하는것으로 보임

임의의 thread를 생성 -> 생성된 thread의 주소공간에 해당 thread를 생성한 task에서 접근할 수 있는지? (permission의 문제) ; 노해결
    - pthread를 이용하여 테스트 (포인터 하나 만들어서 파라미터로 포인터 넘겨서 접근 가능한지 테스트)

vfork // fork
    - fork의 경우 생성된 프로세스에서 exec를 실행하게되면 앞서 생성한 프로세스가 필요없게됨
    - 이러한 문제를 해결하기위해 나온것이 vfork -> vfork를 사용하여 이러한 일을 방지
    - 근데 vfork를 실행한 이후 어느 타이밍에 새로운 가상주소공간을 사용하게되는지?

참고 : http://pinocc.tistory.com/m/post/entry/fork-와-vfork-의-차이점, http://unius.tistory.com/entry/fork-vs-vfork#footnote_link_309_1
=> 최근 시스템에서는 fork함수를 copy on write (COW)를 이용하여 구현됨, fork를 사용하든 vfork를 사용하든 동일하게 처음에는 생성한 task와 동일한 주소공간을 사용하고있지만 fork를 사용할 경우 값을 write하려 할 경우(변수 선언 등)새로운 주소 공간을 할당받아 분리됨, vfork를 사용할 경우 exec나 exit과 같은 프로세스를 새로 생성하는 함수를 사용할 경우 새로운 주소공간을 할당받아 분리됨. -> vfork를 사용할경우 exec나 exit이 호출되기 이전에는 문제가 발생할 수 있기 때문에 조심해서 써야함


multics // unix
1:N, N:N, M:N -> https://en.wikipedia.org/wiki/Thread_(computing)#1:1_.28kernel-level_threading.29
    - 앞부분: thread 개수, 뒷부분: task
    - 1:1: 하나의 task, 하나의 thread;OS에서 생성되는 모든 thread를 알 수 있음
    - N:1: user-level-threading, 라이브러리를 통해 구현되는 추상적인 thread;실제 동일한 주소공간을 사용하면서 추상적으로 thread를 만들어서 사용, OS에서는 thread를 관리를 못함, 얼마나 만들어지는지 관리할 수 없음
    - M:N: 중간단계에 위치하는 thread의 중간 개체가 있고 그 상위로 user-level에서 생성되는 thread들이 연결됨. 최근 시스템에서는 M:N모델을 사용

tgid // pid
    - task_struct구조체 내부에서: tgid, pid = tid
    - getpid: return tgid, NR_gettid: return pid

uid // euid // suid // fsuid
https://en.wikipedia.org/wiki/User_identifier#Process_attributes
    - uid: 진짜 사용자의 그냥 id
    - euid: 실제 권한 (ex: root)과 관련한 uid
    - suid: root권한에서 잠깐 전성윤권한으로 내려오려고하는데 전성윤권한으로 갔다가 망하면안되니까 다시 root권한으로 돌아오려면 root권한을 저장해둬야함. 이게 suid

start_time // real_start_time
    - test

cpu_time
    - cpu_time의 계산방식 (미해결)
        1. cpu_time을 계산할 때 task가 running상태로 들어갈때의 시간과 running상태가 끝날때의 시간과의 차이를 이용하여 계산하는지?
        2. cpu_time을 실시간으로 running상태에 들어가면 계속해서 갱신을하는지?
        -> 효율성의 측면에서보면 1번일 가능성이 크지만, 과연 멀티코어 시스템이라면; 첫번째 코어에서 running상태로 들어갔는데 다른 cpu에서 해당 task의 cpu_time을 접근한다면?
