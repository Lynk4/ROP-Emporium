# callme  64 bit

---

[Accesss the challenge](https://ropemporium.com/challenge/callme.html)

---

So we got these files in this challenge:

```bash
❯ ls
 callme   encrypted_flag.dat   key1.dat   key2.dat   libcallme.so  
╭─ ~/rop/callme
╰─❯
```

---

Let's analyze the binary in gdb.............
```bash
❯ sudo gdb callme
[sudo] password for lynk:
GNU gdb (GDB) 14.2
Copyright (C) 2023 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-pc-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
pwndbg: loaded 160 pwndbg commands and 47 shell commands. Type pwndbg [--shell | --all] [filter] for a list.
pwndbg: created $rebase, $base, $ida GDB functions (can be used with print/break)
Reading symbols from callme...
(No debugging symbols found in callme)
------- tip of the day (disable with set show-tips off) -------
GDB and Pwndbg parameters can be shown or set with show <param> and set <param> <value> GDB commands
pwndbg> info functions
All defined functions:

Non-debugging symbols:
0x00000000004006a8  _init
0x00000000004006d0  puts@plt
0x00000000004006e0  printf@plt
0x00000000004006f0  callme_three@plt
0x0000000000400700  memset@plt
0x0000000000400710  read@plt
0x0000000000400720  callme_one@plt
0x0000000000400730  setvbuf@plt
0x0000000000400740  callme_two@plt
0x0000000000400750  exit@plt
0x0000000000400760  _start
0x0000000000400790  _dl_relocate_static_pie
0x00000000004007a0  deregister_tm_clones
0x00000000004007d0  register_tm_clones
0x0000000000400810  __do_global_dtors_aux
0x0000000000400840  frame_dummy
0x0000000000400847  main
0x0000000000400898  pwnme
0x00000000004008f2  usefulFunction
0x000000000040093c  usefulGadgets
0x0000000000400940  __libc_csu_init
0x00000000004009b0  __libc_csu_fini
0x00000000004009b4  _fini
pwndbg> disass main
Dump of assembler code for function main:
   0x0000000000400847 <+0>:	push   rbp
   0x0000000000400848 <+1>:	mov    rbp,rsp
   0x000000000040084b <+4>:	mov    rax,QWORD PTR [rip+0x20081e]        # 0x601070 <stdout@@GLIBC_2.2.5>
   0x0000000000400852 <+11>:	mov    ecx,0x0
   0x0000000000400857 <+16>:	mov    edx,0x2
   0x000000000040085c <+21>:	mov    esi,0x0
   0x0000000000400861 <+26>:	mov    rdi,rax
   0x0000000000400864 <+29>:	call   0x400730 <setvbuf@plt>
   0x0000000000400869 <+34>:	mov    edi,0x4009c8
   0x000000000040086e <+39>:	call   0x4006d0 <puts@plt>
   0x0000000000400873 <+44>:	mov    edi,0x4009df
   0x0000000000400878 <+49>:	call   0x4006d0 <puts@plt>
   0x000000000040087d <+54>:	mov    eax,0x0
   0x0000000000400882 <+59>:	call   0x400898 <pwnme>
   0x0000000000400887 <+64>:	mov    edi,0x4009e7
   0x000000000040088c <+69>:	call   0x4006d0 <puts@plt>
   0x0000000000400891 <+74>:	mov    eax,0x0
   0x0000000000400896 <+79>:	pop    rbp
   0x0000000000400897 <+80>:	ret
End of assembler dump.
pwndbg> disass pwnme
Dump of assembler code for function pwnme:
   0x0000000000400898 <+0>:	push   rbp
   0x0000000000400899 <+1>:	mov    rbp,rsp
   0x000000000040089c <+4>:	sub    rsp,0x20
   0x00000000004008a0 <+8>:	lea    rax,[rbp-0x20]
   0x00000000004008a4 <+12>:	mov    edx,0x20
   0x00000000004008a9 <+17>:	mov    esi,0x0
   0x00000000004008ae <+22>:	mov    rdi,rax
   0x00000000004008b1 <+25>:	call   0x400700 <memset@plt>
   0x00000000004008b6 <+30>:	mov    edi,0x4009f0
   0x00000000004008bb <+35>:	call   0x4006d0 <puts@plt>
   0x00000000004008c0 <+40>:	mov    edi,0x400a13
   0x00000000004008c5 <+45>:	mov    eax,0x0
   0x00000000004008ca <+50>:	call   0x4006e0 <printf@plt>
   0x00000000004008cf <+55>:	lea    rax,[rbp-0x20]
   0x00000000004008d3 <+59>:	mov    edx,0x200
   0x00000000004008d8 <+64>:	mov    rsi,rax
   0x00000000004008db <+67>:	mov    edi,0x0
   0x00000000004008e0 <+72>:	call   0x400710 <read@plt>
   0x00000000004008e5 <+77>:	mov    edi,0x400a16
   0x00000000004008ea <+82>:	call   0x4006d0 <puts@plt>
   0x00000000004008ef <+87>:	nop
   0x00000000004008f0 <+88>:	leave
   0x00000000004008f1 <+89>:	ret
End of assembler dump.
pwndbg> disass usefulFunction
Dump of assembler code for function usefulFunction:
   0x00000000004008f2 <+0>:	push   rbp
   0x00000000004008f3 <+1>:	mov    rbp,rsp
   0x00000000004008f6 <+4>:	mov    edx,0x6
   0x00000000004008fb <+9>:	mov    esi,0x5
   0x0000000000400900 <+14>:	mov    edi,0x4
   0x0000000000400905 <+19>:	call   0x4006f0 <callme_three@plt>
   0x000000000040090a <+24>:	mov    edx,0x6
   0x000000000040090f <+29>:	mov    esi,0x5
   0x0000000000400914 <+34>:	mov    edi,0x4
   0x0000000000400919 <+39>:	call   0x400740 <callme_two@plt>
   0x000000000040091e <+44>:	mov    edx,0x6
   0x0000000000400923 <+49>:	mov    esi,0x5
   0x0000000000400928 <+54>:	mov    edi,0x4
   0x000000000040092d <+59>:	call   0x400720 <callme_one@plt>
   0x0000000000400932 <+64>:	mov    edi,0x1
   0x0000000000400937 <+69>:	call   0x400750 <exit@plt>
End of assembler dump.
pwndbg> disass usefulGadgets
Dump of assembler code for function usefulGadgets:
   0x000000000040093c <+0>:	pop    rdi
   0x000000000040093d <+1>:	pop    rsi
   0x000000000040093e <+2>:	pop    rdx
   0x000000000040093f <+3>:	ret
End of assembler dump.
pwndbg> cyclic 100
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa
pwndbg> run
Starting program: /home/lynk/rop/callme/callme
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".
callme by ROP Emporium
x86_64

Hope you read the instructions...

> aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa
Thank you!

Program received signal SIGSEGV, Segmentation fault.
0x00000000004008f1 in pwnme ()
LEGEND: STACK | HEAP | CODE | DATA | WX | RODATA
──────────────────────────────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]───────────────────────────────────────────────────
 RAX  0xb
 RBX  0x7fffffffe238 —▸ 0x7fffffffe4ff ◂— '/home/lynk/rop/callme/callme'
 RCX  0x7ffff7eb5504 (write+20) ◂— cmp rax, -0x1000 /* 'H=' */
 RDX  0
 RDI  0x7ffff7f90710 ◂— 0
 RSI  0x7ffff7f8f643 (_IO_2_1_stdout_+131) ◂— 0xf90710000000000a /* '\n' */
 R8   0
 R9   0x7ffff7fcdf40 ◂— endbr64
 R10  0
 R11  0x202
 R12  1
 R13  0
 R14  0x7ffff7ffd000 (_rtld_global) —▸ 0x7ffff7ffe2e0 ◂— 0
 R15  0
 RBP  0x6161616161616165 ('eaaaaaaa')
 RSP  0x7fffffffe108 ◂— 0x6161616161616166 ('faaaaaaa')
 RIP  0x4008f1 (pwnme+89) ◂— ret
───────────────────────────────────────────────────────────[ DISASM / x86-64 / set emulate on ]────────────────────────────────────────────────────────────
 ► 0x4008f1 <pwnme+89>    ret                                <0x6161616161616166>
    ↓









─────────────────────────────────────────────────────────────────────────[ STACK ]─────────────────────────────────────────────────────────────────────────
00:0000│ rsp 0x7fffffffe108 ◂— 0x6161616161616166 ('faaaaaaa')
01:0008│     0x7fffffffe110 ◂— 0x6161616161616167 ('gaaaaaaa')
02:0010│     0x7fffffffe118 ◂— 0x6161616161616168 ('haaaaaaa')
03:0018│     0x7fffffffe120 ◂— 0x6161616161616169 ('iaaaaaaa')
04:0020│     0x7fffffffe128 ◂— 0x616161616161616a ('jaaaaaaa')
05:0028│     0x7fffffffe130 ◂— 0x616161616161616b ('kaaaaaaa')
06:0030│     0x7fffffffe138 ◂— 0x616161616161616c ('laaaaaaa')
07:0038│     0x7fffffffe140 ◂— 0x7f0a6161616d
───────────────────────────────────────────────────────────────────────[ BACKTRACE ]───────────────────────────────────────────────────────────────────────
 ► 0         0x4008f1 pwnme+89
   1 0x6161616161616166
   2 0x6161616161616167
   3 0x6161616161616168
   4 0x6161616161616169
   5 0x616161616161616a
   6 0x616161616161616b
   7 0x616161616161616c
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> cyclic -l faaaaaaa
Finding cyclic pattern of 8 bytes: b'faaaaaaa' (hex: 0x6661616161616161)
Found at offset 40
pwndbg>
```

