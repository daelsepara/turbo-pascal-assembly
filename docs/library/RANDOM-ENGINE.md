# Random Number Generator Engine

```nasm
SYS05DD: MOV AX,[RandSeed.Low]
SYS05E0: MOV BX,[RandSeed.High]
```

Load **[RandomSeed](../DATA.md)** (*LongInt*) into **BX**:**AX**.

```nasm
SYS05E4: MOV CX,AX
SYS05E6: CS:
SYS05E7: MUL WORD PTR [0613]
SYS05EB: SHL CX,1
SYS05ED: SHL CX,1
SYS05EF: SHL CX,1
SYS05F1: ADD CH,CL
SYS05F3: ADD DX,CX
SYS05F5: ADD DX,BX
SYS05F7: SHL BX,1
SYS05F9: SHL BX,1
SYS05FB: ADD DX,BX
SYS05FD: ADD DH,BL
SYS05FF: MOV CL,05
SYS0601: SHL BX,CL
SYS0603: ADD DH,BL
SYS0605: ADD AX,0001
SYS0608: ADC DX,+00
```

```nasm
SYS060B: MOV [RandSeed.Low],AX
SYS060E: MOV [RandSeed.High],DX
```

Update **[RandSeed](../DATA.md)** (*LongInt*) with **DX**:**AX**.

```nasm
SYS0612: RET
```

Return to calling **Random** function (*[Real](RANDOM-REAL.md)*/*[Int](RANDOM-INT.md)*). Because it returns with a **NEAR RET**, it can only be called from within the system library.

```nasm
SYS0613: 05 84
```

Magic Number **8405h**/**33797**. Used internally.

See also: [RandSeed](../DATA.md), or go [back](../../README.md)
