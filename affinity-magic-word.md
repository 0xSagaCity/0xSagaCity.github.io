Affinity Magic Word Writeup {.page-title}
===========================

So a quick look at the binary using gdb shows ...

``` {#6b38b695-9ba9-48b7-ab61-be33ede54302 .code}
gef➤  info functions
All defined functions:

Non-debugging symbols:
..
..
..
//Long list of used functions
..
..
..
0x00000000000039d0  __libc_csu_init
0x0000000000003a30  __libc_csu_fini
0x0000000000003a34  _fini
```

Taking a quick look at all the functions you will find one very
interesting function.

``` {#06c6e88d-e13f-471e-a2b1-5e345661b2a6 .code}
0x0000000000002c8c returnFlagabi:cxx11
```

After looking at the disassembly of the main function, I found a call to
that juicy function `returnFlag()`.

``` {#58830463-49e4-4bcc-839d-4f9c0548daad .code}
gef➤ 0x0000000000002f2f <+427>:  mov    rdi,rax
     0x0000000000002f32 <+430>:   call   0x2c8c <_Z10returnFlagB5cxx11v>
     0x0000000000002f37 <+435>:   lea    rax,[rbp-0x60]
     0x0000000000002f3b <+439>:   lea    rcx,[rbp-0x40]
     0x0000000000002f3f <+443>:   lea    rdx,[rip+0x1160]        # 0x40a6
```

Well, I have the call so I added a break point in main and jumped to
that call easy peasy.

``` {#85c08572-a702-410d-89f9-2c1785b184cc .code}
gef➤  b main
Breakpoint 1 at 0x2d88

gef➤  run
#After hitting the breakpoint time for the jump

gef➤ jump returnFlag()
```

Taking a look at values at this frame will give you a base64 value which
is the flag (after decoding though)

``` {#83cee3aa-6108-4249-927a-0101b4926bf5 .code}
# Current Stuff
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x11
$rbx   : 0x0
$rcx   : 0x34
$rdx   : 0x00007fffffffdaa0  →  0x000055555556cf60  →  "QUZGQ1RGe2g0djNBRzAwZEQ0eX0="
$rsp   : 0x00007fffffffda08  →  0x000055555555641e  →  <base64_decode(std::__cxx11::basic_string<char,+0> lea rax, [rbp-0x29]
$rbp   : 0x00007fffffffda70  →  0x00007fffffffdaf0  →  0x00007fffffffdb70  →  0x00005555555579d0  →  <__libc_csu_init+0> push r15
$rsi   : 0x00007fffffffdaa0  →  0x000055555556cf60  →  "QUZGQ1RGe2g0djNBRzAwZEQ0eX0="
$rdi   : 0x1
$rip   : 0x00007ffff7f07fe8  →  <std::__cxx11::basic_string<char,+0> mov QWORD PTR [rdi+0x8], 0x0
$r8    : 0x000055555556cf60  →  "QUZGQ1RGe2g0djNBRzAwZEQ0eX0="
$r9    : 0x000055555556cf60  →  "QUZGQ1RGe2g0djNBRzAwZEQ0eX0="
$r10   : 0xffffffffffffff0c
$r11   : 0x00007ffff7f07fe0  →  <std::__cxx11::basic_string<char,+0> endbr64
$r12   : 0x00005555555561d0  →  <_start+0> xor ebp, ebp
$r13   : 0x0
$r14   : 0x0
$r15   : 0x0
$eflags: [zero carry parity adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x0033 $ss: 0x002b $ds: 0x0000 $es: 0x0000 $fs: 0x0000 $gs: 0x0000
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
```

Well the underlined and highlighted value is our base64 flag .

``` {#65d17ac2-16a7-48f2-8aae-80a2b10c3917 .code}
❯ echo QUZGQ1RGe2g0djNBRzAwZEQ0eX0= | base64 -d
    AFFCTF{h4v3AG00dD4y}
```

Decode and be happy.
