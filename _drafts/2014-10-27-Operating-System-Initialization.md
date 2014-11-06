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

{% highlight cpp linenos %}
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

--excerpt--

###Initialize Programmable Interval Timer

The PIT chip uses the following I/O ports:
{% highlight cpp linenos %}
I/O port     Usage
0x40         Channel 0 data port (read/write)
0x41         Channel 1 data port (read/write)
0x42         Channel 2 data port (read/write)
0x43         Mode/Command register (write only, a read is ignored)
{% endhighlight %}

The __Programmable Interval Timer (PIT)__ chip (also called an 8253/8254 chip) basically consists of an oscillator, a prescaler and 3 independent frequency dividers. Each frequency divider has an output, which is used to allow the timer to control external circuitry (for example, Channel 0 for IRQ 0).

{% highlight cpp linenos %}
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

This code use Mode 2 - Rate Generator to gain frequency accuracy(frequency = 1193182 / reload_value Hz). 

Firstly, we write command register 0x43 which is 0011 0100 in binary. The fist 00 select channel 0, then 11 set the access mode is lobyte/hibyte respective. 010 set the operating mode as rate generator. The last 0 set it as 16-bit binary.

After mode set, we set the rate by write counter into Channel 0 data port 0x40 hibyte after lobyte.


###Initialize IDT

{% highlight cpp linenos %}
void init_idt() {
	int i;
	/* 为了防止系统异常终止，所有irq都有处理函数(irq_empty)。 */
	for (i = 0; i < NR_IRQ; i ++) {
		set_trap(idt + i, SEG_KERNEL_CODE, (uint32_t)irq_empty, DPL_KERNEL);
	}

	/* 设置异常的中断处理 */
	set_trap(idt + 0, SEG_KERNEL_CODE, (uint32_t)vec0, DPL_KERNEL);
	set_trap(idt + 1, SEG_KERNEL_CODE, (uint32_t)vec1, DPL_KERNEL);
	set_trap(idt + 2, SEG_KERNEL_CODE, (uint32_t)vec2, DPL_KERNEL);

	...
	
	set_trap(idt + 13, SEG_KERNEL_CODE, (uint32_t)vec13, DPL_KERNEL);
	/* the system call 0x80 */
	set_trap(idt + 0x80, SEG_KERNEL_CODE, (uint32_t)vecsys, DPL_USER);
	/* 设置外部中断的处理 */
	set_intr(idt + 32, SEG_KERNEL_CODE, (uint32_t)irq0, DPL_KERNEL);
	set_intr(idt + 33, SEG_KERNEL_CODE, (uint32_t)irq1, DPL_KERNEL);

	/* 写入IDT */
	save_idt(idt, sizeof(idt));
}
{% endhighlight %}

###Initialize Interrupt

###Initailize Serial Port
