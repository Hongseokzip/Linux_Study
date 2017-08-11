---
layout: post
title: "리눅스커널 내부구조 4장"
description: "리눅스커널 내부구조 4장"
comments: true
keywords: "linux kernel"
author: 홍석준
---

1. rb_node에 데이터가 없다. 도대체 어디에 있나?
    - Why? (왜 이렇게 관리할까)
        c에서는 상속이 없기 때문에 레드블랙트리와 같은 lib로 사용되는 자료구조들은 다른 소스에서도 많이 사용되기때문에 다른 곳에서도 사용하려면 재사용성을 위해서 이렇게 (i.e. rb_entry) 관리 해야함

    http://lxr.free-electrons.com/source/include/linux/rbtree.h#L51
    rb_node에 데이터를 저장하는 게 아니라, 데이터 구조체 내에 rb_node멤버를 포함시키고
    rb_entry (container_of) 매크로를 통해 "부모" 데이터 구조체를 얻는다
        => 사용 예 : http://lxr.free-electrons.com/source/mm/mmap.c#L245

    [ container_of 매크로 ]
    #define container_of(ptr, type, member) ({          \
    const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
    (type *)( (char *)__mptr - offsetof(type,member) );})

    ptr을 포함하는 struct instance의 pointer를 리턴한다

    typeof 연산자는 변수의 타입을 리턴하는 gcc 확장 키워드

    "ptr" 주소값에서 member의 offset을 빼면 "부모" struct instance의 주소값

http://cinsk.github.io/cfaqs/html/


313 static void __insert_vmap_area(struct vmap_area *va)
314 {
315         struct rb_node **p = &vmap_area_root.rb_node;
316         struct rb_node *parent = NULL;
317         struct rb_node *tmp;
318 //리프노드 찾아가는 루프
319         while (*p) {
320                 struct vmap_area *tmp_va;
321 
322                 parent = *p;
323                 tmp_va = rb_entry(parent, struct vmap_area, rb_node);
//va_start, va_end의 값은 va_start가 아래에 있음 (va_end = va_start + size)
324                 if (va->va_start < tmp_va->va_end)
325                         p = &(*p)->rb_left;
326                 else if (va->va_end > tmp_va->va_start)
327                         p = &(*p)->rb_right;
//unreachable
328                 else
329                         BUG();
330         }
331 
332         rb_link_node(&va->rb_node, parent, p);
// insert_color
// augment_rotate -> rotate는 rb_insert에서 파라미터로 함수 포인터 넘겨서 rotate한 뒤에 커스텀 하고 싶은 내용을 추가할 수 이씅ㅁ
// 실제 rotate는  __rb_rotate_set_parents(gparent, parent, root, RB_RED); 여기서 다 됨
// http://lxr.free-electrons.com/source/lib/rbtree.c#L97
333         rb_insert_color(&va->rb_node, &vmap_area_root);
334 
335         /* address-sort this list */
336         tmp = rb_prev(&va->rb_node);
337         if (tmp) {
338                 struct vmap_area *prev;
339                 prev = rb_entry(tmp, struct vmap_area, rb_node);
340                 list_add_rcu(&va->list, &prev->list);
341         } else
342                 list_add_rcu(&va->list, &vmap_area_list);
343 }

http://lxr.free-electrons.com/source/mm/vmalloc.c#L1222
insert_vmap_area -> vmalloc_init -> mm_init -> start_kernel

