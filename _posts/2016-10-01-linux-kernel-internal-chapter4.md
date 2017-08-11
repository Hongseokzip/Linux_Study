---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

NUMA 가 구체적으로 뭔가?
- http://www.dawnofthered.net/?p=51 에 나온 모식도와
- http://bladesmadesimple.com/2010/03/details-on-intels-nehalem-ex-xeon-7500-and-xeon-6500/ 에 나온 그림 참조
- 한 CPU 는 로컬 메모리를 갖는다. CPU 가 여러개일 때, 다른 CPU 의 로컬 메모리(원격 메모리)를 참조할 수 있는 구조라면, 일반적으로는 원격 메모리 참조 속도가 현저히 느릴것이다. 이러한 속도차이로 Bank 를 구분하며, Node 로 구현된다.
- 인텔의 QPI 같은 기술은 원격 메모리 참조 속도를 부스트업해줘서 속도차이 구분이 굳이 필요없지도록 해줬고, 따라서, Bank가 한개 CPU의 로컬 메모리군으로만 구성된다고는 할 수 없다. 예를 들면, 2개의 CPU 의 로컬메모리들 전체가 한 Bank 가 될 수 있는 것이다.
- 속도차이가 있더라도 임의로 Bank 를 구성할 수 있으나, 일반적으로는 그렇게 하지 않을 것이다. 즉, 마더보드가 CPU 여러개를 어떻게 연결했고, 커널이 마더보드에 설정을 쿼리했을 때 어떻게 값이 넘겨 줄 지는 하드웨어 설계자 역량이다.