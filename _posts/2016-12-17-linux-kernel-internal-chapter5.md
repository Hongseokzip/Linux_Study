---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

== ext2 ==
그림 5.14에 대한 결론

 $ sudo dumpe2fs /dev/mapper/GangsServer--vg-root   # 파티션 내부 각 block group들의 세부정보
 $ sudo fdisk -l /dev/mapper/GangsServer--vg-root   # 파티션이 차지하는 실린더 갯수 파악
 $ df -h                                            # 파티션별 사이즈를 알기 위해
 ==> block group 들은 연속되어있다

 * http://www.nongnu.org/ext2-doc/ext2.html#DISK-ORGANISATION
 - block group 안에 위 문서에 나온대로, 데이터들 저장
 - (inode number) = (block group number) * s_inodes_per_group + (local inode index)

 Q. inode table 은 Block Group 들에 global 하다?
  A. inode number 는 global 하지만, inode table 은 블록그룹마다 local

 Q. data blocks 는 Block Group 들에 global 하다?
  A. data blocks 은 블록그룹 마다 local

 Q. 만약에 file 이 block group 1 의 끄트머리에서부터 저장된다, 이 file 이 block group 1의 관할을 벗어난 크기로 저장될 때, 어떠한 사건이 일어나는가?
  A. 확인 방법 : dd 같은거로 io 유발해두고, perf 로 tracing 하면서 block group number 가 어떻게 넘어가는지 ( 과제로 남겨둔다 )

SSD 를 위한 전용 파일시스템이 있을까?
 - f2fs (삼성의 김재극씨)
 - ex4 도 빠르다!

== ext3, ext4 ==
journaling 이 뭐지?
 - 경남 : journal 은 git 에서 stage 같은 공간임 commit 해둔곳, push 해서 filesystem 에 최종적으로 씀. 전원이 나가거나 하면 완벽하지 않은 push 나 commit 은 rollback 가능

journal 은 어디에 저장되는 거지?
 - The journal is stored in a separate space of the partition, you cannot access it directly.
  $ sudo debugfs /dev/sda#
  >> logdump

extent 기반 데이터 블록 유지
 - 기본적인 압축 아이디어. 1111111111112222223333 -> 1:12,2:6,3:4 로 압축 하듯이, 

온라인 단편화 제거 ; 디스크 조각모음을 파일 읽고 쓰는 중에도 해주는거

[PENDING] i_block[15]가 있는 것처럼 책에 나와 있는데 struct inode 에 읎당께