---
layout: 	post
title:  	"[Nanos]1. Bootloader"
date:   	2014-06-06 03:00:00-0000
modified:   2015-03-09 00:00:00-0000
categories: 
- Operating System
tags:		[OS]
---

Bootloader contents:

{% highlight Bash shell scripts %}
.
├── asm.h
├── boot.h
├── genboot.pl
├── main.c
├── Makefile
└── start.S
{% endhighlight %}

###1.1 MakeFile

{% highlight Makefile %}
bootblock: start.S main.c asm.h boot.h
	gcc -c -m32 start.S
	gcc -c -m32 -O1 main.c
	ld -melf_i386 -e start -Ttext 0x7C00 start.o main.o -o bootblock.o
	@objcopy -S -O binary -j .text bootblock.o bootblock
	@./genboot.pl bootblock

clean:
	rm -rf bootblock *.o
{% endhighlight %}

`Makefile` complie `tart.S` and `main.c` under 32-bit environment, and load the entry of Bootloader - `start` which realiszed in `start.S` into 0x7C00([Why 0x7c00](http://www.glamenv-septzen.net/en/view/6)) after linking. Then it call the perl script `genboot.pl` to transform the obj file into 512 bytes bootblock. 

###1.2 start.S - The entry of the world

{% highlight gas %}
# 系统启动时，BIOS会完成初始化，然后将磁盘第一个512字节保存到物理内存0x00007C00
# 位置，然后设置%CS=0000h, %IP=7C00h

#include "asm.h"
# 从此开始是16位代码
.code16
.globl start
{% endhighlight%}

Now when the computer finish it's hardware routine, it comes to the `start.S`. Note that at the time, the system is run under the [Real Mode][Real Mode], so the first line `.code 16` make the code under 16-bits environment. And next line extern the `start` which was mentioned in the `Makefile`.

{% highlight gas %}
start:
	cli                       # 关闭外部中断

	# 设置正确的段寄存器
	xorw    %ax, %ax          # %AX = 0
	movw    %ax, %ds          # %DS = AX (data segment)
	movw    %ax, %es          # %ES = AX (extra segment)
	movw    %ax, %ss          # %SS = AX (stack segment)

	# 打开A20地址线
	movw    $0x2401, %ax
	int     $0x15

	lgdt    gdtdesc           # 设置GDT(段描述符表地址为$gdt)
	movl    %cr0, %eax        # %CR0 |= PROTECT_ENABLE_BIT
	orl     $0x1, %eax
	movl    %eax, %cr0        # 设置PE bit
{% endhighlight %}

Then we turn off the hardware [interrupt][Interrupt], setting the segment register DS, ES, and SS to zero, openning the [A20 line][A20], setting the [GDT][GDT] and [control register CR0](http://en.wikipedia.org/wiki/Control_register).

###1.3 A20 Line

The A20 Address Line is the physical representation of the 21st bit (number 20, counting from 0) of any memory access. When the IBM-AT (Intel 286) was introduced, it was able to access up to sixteen megabytes of memory (instead of the 1 MByte of the 8086). But to remain compatible with the 8086, a quirk in the 8086 architecture (memory wraparound) had to be duplicated in the AT. To achieve this, the A20 line on the address bus was disabled by default.

Acctually, there are 3 ways to open A20, and the morden computer use a fast way which read/write 0x92 port, but we use BIOS 0x15 instead. 

###1.4 GDT, Protected Mode, and A BIG JUMP

Befor [Protected Mode][Protected Mode] introduced by 80286, the 8086 computer run in [Real Mode][Real Mode], which giving exactly 1MiB of addressable memory. To access a memory unit, we caculate the physical address by "base << 4 + offset". So with the 16bit Segment Register, the physical address can reach 20bits, giving 1MiB addressing space.

But actually, using two 16-bit register which's max value is 0xFFFF, the address could only reach (0xFFFF) << 4 + 0xFFFF = 0x10FFEF. That is why we need another bit - A20.

When it comes to Protected Mode, the base:offset addressing mode has a little change. For extending the addressing space with downward compatible with the Intes-x86 family before 80286, the segment registers are used as index of [GDT][GDT], which defined the base, limit and properties of a segment.

![GDT](/images/GDT.png "GDT")

The GDT structure may be a little strange at first view. For history reasons, the base and offset are splitted in the middle. The GDT also provides some protect procedures where the name "Portected Mode" comes.

Okey, now we come back to `start.S`. The "lgdt" command do the such two jobs:

GDTR(Limit) <- Operand[0:15]; GDTR(Base) <- Operand[16:47]

{% highlight gas %}
# GDT
.p2align 2                         # 对齐
gdt:
	SEG_NULLASM                    # GDT第一项必须为空
	SEG_ASM(0xA, 0x0, 0xffffffff)  # 代码段描述符
	SEG_ASM(0x2, 0x0, 0xffffffff)  # 数据段描述符

gdtdesc:                           # GDT描述符
	.word   (gdtdesc - gdt - 1)    # GDT长度，留意地址运算
	.long   gdt                    # GDT地址
{% endhighlight %}

And at the `gdt:` section, we defined three segments NULL, Code Segment and Data Segment with macro SEG\_NULLASM and SEG\_ASM, which are implemented in `asm.h`.

Then the `ljmp` command jump to the `start32` label where the Protected Mode begins. Note that, here the GDT\_ENTRY is the segment selector which point to the code segment, the 1st item of GDT.

{% highlight gas %}
.code32
start32:
	# 设置数据访问所用的段寄存器(%DS, %ES, %SS)
	movw    $GDT_ENTRY(2), %ax
	movw    %ax, %ds          # %DS = %AX
	movw    %ax, %es          # %ES = %AX
	movw    %ax, %ss          # %SS = %AX

	# 设置栈位置。栈从此没有切换过，请注意栈的大小！
	movl    $0x8000, %esp     # %ESP = $0x8000
	call    bootmain          # 跳转到C代码执行，此处不会返回
{% endhighlight %} 

When we come to Protected Mode, we set the DS, ES, SS as the Data Segment, the second item of GDT. And set the stack address as 0x8000. Then we jump to `main.c` (bootmain).

###1.5 main.c

{% highlight cpp %}
void
bootmain(void) {
	struct ELFHeader *elf;
	struct ProgramHeader *ph, *eph;
	unsigned char *pa, *i;

	/* 因为引导扇区只有512字节，我们设置了堆栈从0x8000向下生长。
	 * 我们需要一块连续的空间来容纳ELF文件头，因此选定了0x8000。 */
	elf = (struct ELFHeader*)0x8000;

	/* 读入ELF文件头 */
	readseg((unsigned char*)elf, 4096, 0);

	/* 把每个program segement依次读入内存 */
	ph = (struct ProgramHeader*)((char *)elf + elf->phoff);
	eph = ph + elf->phnum;
	for(; ph < eph; ph ++) {
		pa = (unsigned char*)ph->paddr; /* 获取物理地址 */
		readseg(pa, ph->filesz, ph->off); /* 读入数据 */
		for (i = pa + ph->filesz; i < pa + ph->memsz; *i ++ = 0);
	}

	((void(*)(void))elf->entry)();
}
{% endhighlight %}

After we setting the system environments with `start.S`, C code loads the kernel from disk to memory with [ELF][ELF] format. Then the bootloader`s job finished, code `((void(*)(void))elf->entry)();` jumps to the kernel.

[Interrupt]:http://en.wikipedia.org/wiki/Interrupt
[Protected Mode]:http://en.wikipedia.org/wiki/Protected_mode
[Real Mode]:http://en.wikipedia.org/wiki/Real_mode
[GDT]:http://wiki.osdev.org/GDT
[A20]:http://en.wikipedia.org/wiki/A20_line
[ELF]:http://en.wikipedia.org/wiki/Executable_and_Linkable_Format