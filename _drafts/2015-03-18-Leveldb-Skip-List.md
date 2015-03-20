---
layout: 	post
title:  	"Skip List"
date:   	2015-03-18 00:00:00-0000
modified:	2015-03-18 00:00:00-0000
categories: 
- Leveldb
tags:		[Leveldb]
---

###Skip List

![SkipList][SkipList]

> Skip lists are a probabilistic data structure that seem likely to supplant balanced trees as the implementation method of choice for many applications. Skip list algorithms have the same asymptotic expected time bounds as balanced trees and are simpler, faster and use less space.
> --*William Pugh*

Leveldb中，KV数据在内存中都是由Memtable管理的。 为了保证Memtable有序性的同时，兼顾高效的插入删除以及查找速度，Leveldb采用了Skip List作为memtable的底层实现。

Skip List由[William Pugh][William Pugh]于1989年发明。Average Case下，搜索、插入、删除的时间代价为$$ O(logn) $$, 空间代价为$$ O(n) $$。与红黑树相比，Skip List最明显的特色就是简单好写，同时也保证了Average Case下的性能。

Skip List采用多层链表结构，最底层(level 1)为原始有序链表。对于每层链表，每个节点有一定概率被抽取，作为索引被复制到上层链表。查询时，逐层与索引比较，直至底层。插入新节点时，首先执行查询操作，然后将新节点插入底层链表，再以一定的概率分布不断flip coin，head则抽取作为上层新节点，直至tail结束。删除时，删除各层该节点的副本即可。具体操作上面的gif应该能很直观的告诉你。

###Leveldb中的实现

Leveldb中，Skip List实现在<db/skiplist.h>中，其中Arena部分是Leveldb的内存管理部分，为Skip List中的节点分配内存。

{% highlight cpp %}
template<typename Key, class Comparator>
SkipList<Key,Comparator>::SkipList(Comparator cmp, Arena* arena)
    : compare_(cmp),
      arena_(arena),
      head_(NewNode(0 /* any key will do */, kMaxHeight)),
      max_height_(reinterpret_cast<void*>(1)),
      rnd_(0xdeadbeef) {
  for (int i = 0; i < kMaxHeight; i++) {
    head_->SetNext(i, NULL);
  }
}
{% endhighlight %}

构造函数接受用户自定义的比较器Comparator和一个用于内存管理的Arena，并且新建一个空的头节点，初始化最大高度为1,初始化随机数。



[William Pugh]:http://en.wikipedia.org/wiki/William_Pugh
[SkipList]:/images/Skip_list.gif