---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

// linux version 4.10

create 라는 system call 에서 do_sys_open 까지 도달함.

위 함수 안에 filp_open 함수는 '인자로 전달된 파일 이름과 디렉터리 구조를 이용해 그 파일에 대응되는 아이노드 객체를 찾아내 리턴한다. 이때 파일 구조의 f_op 변수도 초기화되는데, f_op 에는 각 파일의 유형에 따라 적합한 파일 연산으로 등록된다'

[책] f_op 의 open 함수(포인터)는 NULL 이다?
 => 옛날 버전에서 그렇다. 최신 커널에서는 그렇지 않다. 
  근데 왜 옛날 버전에서는 open 이 NULL 일 수 있었지?
  open 은 딱히 초기화 안해도 filp_open 함수가 미리 할거 다 해주니깐..

*filp_open 에 대해 알아보자
-> do_filp_open : (http://lxr.free-electrons.com/source/fs/namei.c#L3484)
 -> set_nameidata : 현재 nameidata 가 유지된다 (http://lxr.free-electrons.com/source/fs/namei.c#L529)
 -> path_openat (http://lxr.free-electrons.com/source/fs/namei.c#L3426)
  -> link_path_walk : 그 link 의 path 를 '/' 를 기준 토큰나이즈하여 하나씩 walk 해본다. nameidata 에 구조화시킨다. (http://lxr.free-electrons.com/source/fs/namei.c#L2035)
   -> walk_component 
    -> lookup_fast 함수 참조! dentry 를 가지고 뭔가 한다. (http://lxr.free-electrons.com/source/fs/namei.c#L1533)
 -> restore_nameidata

lookup_slow 에서 inode->i_op->lookup 함수를 통해서 그 파일시스템의 고유 함수(lookup)를 호출하게끔한다. 

우리의 이 모든 고민은 http://egloos.zum.com/studyfoss/v/5469672 여기에 담겨있다!!