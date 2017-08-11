---
layout: post
title: "리눅스커널 내부구조 5장"
description: "리눅스커널 내부구조 5장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

sticky bit
tmp directory같은 거는 권한이 777이라서 누구나 읽고 쓸수 있는 곳인데, 이 directory에 sticky bit를 설정해놓으면 파일의 소유자만 파일 삭제가 가능(그 외에는 rx만 가능)

sticky bit와 setuid bit
http://ehclub.co.kr/2756
http://soul0.tistory.com/246




# 찾아보고 싶은것.
파일 크기가 48kb미만인 경우 인덱스 블록을 디스크에서 읽어올 필요가 없다. 48kb = 4kb * 12개
"12개"라는 숫자를 최초로 커밋한 순간은 ??

파일시스템의 boot block의 역할?
MBR과 같이 OS 부팅을 위한 기본적인 코드가 들어잇는 블록인 것 같음!!

디스크에서의 물리적으로 빠른 접근을 위해 인접한 실린더를 "블록그룹"으로 정의 (ext2의 경우)



p.145 그림 5.14
파일마다 고유한 inode값을 갖고 있고 inode를 알고 있으면 inode table의 갯수가 고정되어 있으므로 inode가 속한 block group을 계산하여 알아낼 수 있다.





$ sudo dumpe2fs -h dev/sda1
Inodes per gruop, Inode blocks per group, Blocks per group 등의 정보를 출력해줌



다음주에 p.145 다시 토론 ㄱㄱㄱㄱ
data block도 global하게 관리되어야 하지 않나???
file size가 data block size보다 커지면 file은 실제로 어떻게 저장되고, 탐색은 어떻게 되는가??