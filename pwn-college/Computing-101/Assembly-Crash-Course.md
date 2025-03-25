# Assembly Crash Course

## set-register

Challenge instructs us to `rdi = 0x1337`, I got confused after just putting the instruction and trying to assemble the file but I got the error that the input is not an assembled binary


I assembled the following assembly code:-

```asm

.intel_syntax noprefix
.global _start
_start:

mov rdi, 0x1337

```

flag: `pwn.college{UGMU5woKCgX7NmQAjX-y6dIhFa-.0FN5EDL2MDO0czW}`


## set-multiple-registers

same shit, different code

```asm

.intel_syntax noprefix
.global _start
._start:

mov rax, 0x1337
mov r12, 0xCAFED00D1337BEEF
mov rsp, 0x31337

```

flag: `pwn.college{QHqSksYY3oYzAi3Bd7eyN6kRse6.dBTM4MDL2MDO0czW}`


## add-to-register

We have to add `0x331337` to the `rdi` register

```asm

.intel_syntax noprefix
.global _start
._start:

add rdi, 0x331337

```

flag: `pwn.college{YJ4-f_FJEk_BEfKzbCkCm7YmmZ7.0VN5EDL2MDO0czW}`


## linear-equation-registers

We have to compute a linear equation, `f(x) = mx + b` where `m` is in `rdi`, `x` is in `rsi` and `b` is in `rdx`

We have to place the result in `rax`:-

NOTE: `mul` -> unsigned multiplication, `imul` -> signed multiply


```asm
.intel_syntax noprefix
.global _start
._start:


mov rax, 0
add rax, rsi
imul rax, rdi
add rax, rdx

```

flag: `pwn.college{YIagHF19G5yU2JZngOSavLJCJu7.0lN5EDL2MDO0czW}`


## integer-division

A few things to note, `div` always works on `rax`, it can divide a 128-bit dividend by a 64-bit divisor:-

It does integer division and stores the value in `rax` and the remainder in `rdi`

```asm

mov rax, reg1
div reg2        ; reg1/reg2  

```

does:-

```asm

rax = (rdx:rax) / reg     ; rdx:rax used to store a 128-bit operand
rdx = remainder

```

we have to compute `speed = distance / time`

- distance = `rdi`
- time = `rsi`
- speed = `rax`


code:-

```asm
.intel_syntax noprefix
.global _start
._start:


mov rdx, 0;       ; rdx must be zeroed
mov rax, rdi
div rsi

```

flag: `pwn.college{wawtBQDmPk2SVfqQJHF2EalgYsg.01N5EDL2MDO0czW}`

