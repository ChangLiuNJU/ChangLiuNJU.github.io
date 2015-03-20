---
layout: 	post
title:  	"Atomic Pointer"
date:   	2015-03-20 00:00:00-0000
modified:	2015-03-20 00:00:00-0000
categories: 
- Leveldb
tags:		[Leveldb]
---

###Atomic Pointer

Leveldb为了支持对于变量的原子性操作，提供了原子性指针Atomic Pointer，并且根据不同的platform进行了相应实现。

###Base on MemoryBarrier()

{% highlight cpp %}

// AtomicPointer built using platform-specific MemoryBarrier()
#if defined(LEVELDB_HAVE_MEMORY_BARRIER)
class AtomicPointer {
 private:
  void* rep_;
 public:
  AtomicPointer() { }
  explicit AtomicPointer(void* p) : rep_(p) {}
  inline void* NoBarrier_Load() const { return rep_; }
  inline void NoBarrier_Store(void* v) { rep_ = v; }
  inline void* Acquire_Load() const {
    void* result = rep_;
    MemoryBarrier();
    return result;
  }
  inline void Release_Store(void* v) {
    MemoryBarrier();
    rep_ = v;
  }
};

{% endhighlight  %}

在Windows on x86, maxos, gcc on x86, arm linux等环境下，leveldb通过直接调用现有的MemoryBarrier()或者通过汇编代码`("":::"memory")`实现内存屏障([内存屏障详见孔神blog][MemoryBarrier])。

+ 在Acquire_Load函数返回前，MemoryBarrier确保对rep_的写操作已经全部完成，写入内存。
+ 在Release_Store中，MemoryBarrier保证对rep_的更改“按照顺序”执行。

此外，AtomicPointer也提供了非原子性的访问操作NoBarrier_Load和NoBarrier_Store。

###Base on \<cstdatomic\>

{% highlight cpp %}

// AtomicPointer based on <cstdatomic>
#elif defined(LEVELDB_ATOMIC_PRESENT)
class AtomicPointer {
 private:
  std::atomic<void*> rep_;
 public:
  AtomicPointer() { }
  explicit AtomicPointer(void* v) : rep_(v) { }
  inline void* Acquire_Load() const {
    return rep_.load(std::memory_order_acquire);
  }
  inline void Release_Store(void* v) {
    rep_.store(v, std::memory_order_release);
  }
  inline void* NoBarrier_Load() const {
    return rep_.load(std::memory_order_relaxed);
  }
  inline void NoBarrier_Store(void* v) {
    rep_.store(v, std::memory_order_relaxed);
  }
};

{% endhighlight  %}

C++11提供了\<atomic\>包，并且提供了对于数据加载时的同步模型控制，分别为：

+ **std::memory_order_relaxed**——对同步导致的副作用relax
+ **std::memory_order_consume**——用于Load。确保当前线程中，此次Load之后用到相关变量的指令，不会被重排到Load之前。值得注意的是， consume与acquire不同，只保证了consume操作变量的按序执行，并不保证其他数据不被乱序。
+ **std::memory_order_acquire**——用于Load。acquire操作之后的内存操作不许乱序到acquire之前。确保一旦acquire,那么其他线程release操作之前的所有内存操作对本线程可见。
+ **std::memory_order_release**——用于Store。release操作之前的所有内存操作不许乱序到release之后。确保本线程release之前的所有内存操作，对于其他acquire的线程有效。
+ **std::memory_order_acq_rel**——acquire and release.
+ **std::memory_order_seq_cst**——确保内存操作严格按序执行！

可以看到，AtomicPoint在此只是进行了简单的封装。

###Base on sparc memory barriers && ia64 acq/rel

{% highlight cpp %}

// Atomic pointer based on sparc memory barriers
#elif defined(__sparcv9) && defined(__GNUC__)
class AtomicPointer {
 private:
  void* rep_;
 public:
  AtomicPointer() { }
  explicit AtomicPointer(void* v) : rep_(v) { }
  inline void* Acquire_Load() const {
    void* val;
    __asm__ __volatile__ (
        "ldx [%[rep_]], %[val] \n\t"
         "membar #LoadLoad|#LoadStore \n\t"
        : [val] "=r" (val)
        : [rep_] "r" (&rep_)
        : "memory");
    return val;
  }
  inline void Release_Store(void* v) {
    __asm__ __volatile__ (
        "membar #LoadStore|#StoreStore \n\t"
        "stx %[v], [%[rep_]] \n\t"
        :
        : [rep_] "r" (&rep_), [v] "r" (v)
        : "memory");
  }
  inline void* NoBarrier_Load() const { return rep_; }
  inline void NoBarrier_Store(void* v) { rep_ = v; }
};

// Atomic pointer based on ia64 acq/rel
#elif defined(__ia64) && defined(__GNUC__)
class AtomicPointer {
 private:
  void* rep_;
 public:
  AtomicPointer() { }
  explicit AtomicPointer(void* v) : rep_(v) { }
  inline void* Acquire_Load() const {
    void* val    ;
    __asm__ __volatile__ (
        "ld8.acq %[val] = [%[rep_]] \n\t"
        : [val] "=r" (val)
        : [rep_] "r" (&rep_)
        : "memory"
        );
    return val;
  }
  inline void Release_Store(void* v) {
    __asm__ __volatile__ (
        "st8.rel [%[rep_]] = %[v]  \n\t"
        :
        : [rep_] "r" (&rep_), [v] "r" (v)
        : "memory"
        );
  }
  inline void* NoBarrier_Load() const { return rep_; }
  inline void NoBarrier_Store(void* v) { rep_ = v; }
};

{% endhighlight  %}

这里只是基于不同的体系结构，进行了相似的封装，不(完)再(全)详(看)述(不)了(懂)。

###Base on 其他

{% highlight cpp %}

// We have neither MemoryBarrier(), nor <atomic>
#else
#error Please implement AtomicPointer for this platform.

{% endhighlight  %}

请自己实现一下咯～

[MemoryBarrier]:http://blog.kongfy.com/2014/11/linux%E5%86%85%E6%A0%B8%E5%90%8C%E6%AD%A5/

