---
layout: 	post
title:  	"[Nanos]2. Initialization"
date:   	2014-10-27 16:00:00-0000
modified:   2014-10-27 16:00:00-0000
categories: 
- Operating System
tags:		[OS]
---

As we talked in the last section, code `((void(*)(void))elf->entry)();` jumps to the kernel.

We defined the entry function as follow:

{% highlight cpp %}
void
entry(void) {
	init_timer();
	init_idt();
	init_intr();
	init_serial();
	enable_interrupt();

	while (1) {
		wait_for_interrupt();
	}
	assert(0);
}
{% endhighlight %}

###Initialize Programmable Interval Timer

{% highlight cpp %}
/* 8253输入频率为1.193182MHz */
#define TIMER_PORT 0x40
#define FREQ_8253 1193182

void
init_timer(void) {
	int counter = FREQ_8253 / HZ;
	assert(counter < 65536);
	out_byte(TIMER_PORT + 3, 0x34);
	out_byte(TIMER_PORT + 0, counter % 256);
	out_byte(TIMER_PORT + 0, counter / 256);
}
{% endhighlight %}

The __Programmable Interval Timer (PIT)__ chip (also called an 8253/8254 chip) basically consists of an oscillator, a prescaler and 3 independent frequency dividers. Each frequency divider has an output, which is used to allow the timer to control external circuitry (for example, IRQ 0).

###Initialize IDT

###Initialize Interrupt

###Initailize Serial Port
