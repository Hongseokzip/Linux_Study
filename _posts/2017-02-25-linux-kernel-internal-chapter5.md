---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

dentry
    - 파일시스템안에서 파일로서의 객체
    - 프로세스 위에서는 파일 파일시스템안에서의 파일로서는 dentry

i_nlink
    - http://www.tldp.org/LDP/lki/lki-3.html
    - 하드 링크의 수
    - http://lxr.free-electrons.com/source/fs/inode.c#L314
    - if (inode->i_nlink == 0) -> 0일 경우는 어떤것인지?
    - https://lkml.org/lkml/2011/10/12/213


// 갓갓코드
// https://lkml.org/lkml/2011/10/12/214
582         union {
583                 const unsigned int i_nlink;
584                 unsigned int __i_nlink;
585         };
