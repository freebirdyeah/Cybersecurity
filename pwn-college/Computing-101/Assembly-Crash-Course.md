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

It does integer division and stores the value in `rax` and the remainder in `rdx`

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

## modulo-operation

Need to calculate `rdi % rsi`, but the `div` operation works on `rax` so I'll set `rdx` to 0 and `mov rax, rdi`

after doing `div rsi` the remainder will be stored in `rdx`


```asm

mov rdx, 0
mov rax, rdi
div rsi
mov rax, rdx

```

need to place the remainder (`rdx`) in `rax`

flag: `pwn.college{kKmBHzoA6B7yQ5n036gMjTf9-Op.0FO5EDL2MDO0czW}`


## set-upper-byte

bruh.

```asm

mov ah, 0x42

```

flag: `pwn.college{4t5VS7TaYDvVTltRTEsPeiTWfkR.dFTM4MDL2MDO0czW}`


## efficient-modulo

apparently using `div` to compute, the modulo is slow as hell.

the trick is if `x % y` and `y = 2^n` then the result will be lower `n` bits of `x`

we need to compute:-

```
rax = rdi % 256
rbx = rsi % 65536

```

256 -> 8th power of 2
65536 -> 16th power of 2

`rax` should take in the lower 8 bits of `rdi`
`rbx` should take in the lower 16 bits of `rsi`

lower 8 bits of `rdi` -> `dil`
lower 16 bits of `rsi` -> `si`

```asm

mov rax, 0
mov rbx, 0

mov al, dil
mov bx, si

```

Incorrect tangent:-


Won't work due to the second and fourth instruction, can't put a smaller register into a bigger one. To do that `movzx`, should be used which clears the higher bits


```asm

mov rax, rdi
mov rax, al

mov rbx, rsi
mov rbx, bx
  
```

## byte-extraction

`shr`, `shl` shifts the values to right and left

need to set `rax` to the 5th LSB of `rdi`

`rdi = | B7 | B6 | B5 | **B4** | B3 | B2 | B1 | B0 |`

OH WE ARE DEALING WITH BYTES NOT BITS!


```asm

mov rax, rdi
shl rax, 24
shr rax, 56

```

flag: `pwn.college{8iUlPuED1l_m50Ar9O9nNhAvkis.0FMwIDL2MDO0czW}`


## bitwise-and

this works (we need to write `rdi & rsi` to `rax` without using `mov`, `xchg`) 

NOTE: `lea` works as `mov`, also it doesn't dereference the register it just loads the value stored

```asm

and rdi, rsi
lea rax, [rdi]

```

## check-even

Have to implement this-

```
if x is even then
  y = 1
else
  y = 0

```

where `x = rdi`, `y = rdx`

code:-

```asm

and rdi, 1
xor rdi, 1
xor rax, rax
or rax, rdi

```
need to `and rdi, 1` first because if we straight up `xor` it then the 0s in 1's 64bit representation will mess up rdi, on being `xor`'ed, we won't be just left with the LSB

- `xor`ing with `1` just flips the bit

flag: `pwn.college{4i0L32WT0rIEHOTQHndKhDKCugB.0lMwIDL2MDO0czW}`


## memory-read

NOTE:-

1. `mov rax, [some_address]` <=> Moves the thing at 'some_address' into `rax`

2. `mov rax, [rdi]` <=> Moves the thing stored at the address of what `rdi` holds to `rax`

3. `mov [rax], rdi` <=> Moves `rdi` to the address of what `rax` holds.


Place the value stored at `0x404000` into `rax`. Make sure the value in `rax` is the original value stored at `0x404000`.

```asm
mov rax, [0x404000]
```

flag: `pwn.college{UD_ZT52atqP2b9bU-IiUMTLBglk.dJTM4MDL2MDO0czW}`

## memory-write

Place the value stored in `rax` to `0x404000`.

code:-

```asm

mov rbx, 0x404000
mov [rbx], rax

```

flag: `pwn.college{Qo35tx9ZR0pdntFUYnGNqu952hV.dNTM4MDL2MDO0czW}`


## memory-increment

- Place the value stored at `0x404000` into `rax`.
- Increment the value stored at the address `0x404000` by `0x1337`.

```asm

mov rax, [0x404000]
mov rbx, rax
add rbx, 0x1337
mov qword ptr [0x404000], rbx

```

flag: `pwn.college{cOH4wL7cJzUzL0qgyHlGTc4x-zx.01MwIDL2MDO0czW}`


