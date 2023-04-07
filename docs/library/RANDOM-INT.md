# Random(*Int*): *Int* Function

Returns a random number (***Int***/***Word***, 2 bytes). It takes one ***Int*** parameter and returns ***X*** in the range: **0** <= ***X*** < ***Int***.

```nasm
SYS0580: CALL 05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

# Stack after SYS:0580

|Index|Contents                |
|-----|------------------------|
|SP   |Return Address (OFFSET) |
|SP+02|Return Address (SEGMENT)|
|SP+04|*Int* parameter         |

```nasm
SYS0583: MOV BX,SP
SYS0585: MOV CX,DX
SYS0587: SS:
SYS0588: MUL WORD PTR [BX+04]
SYS058B: MOV AX,CX
SYS058D: MOV CX,DX
SYS058F: SS:
SYS0590: MUL WORD PTR [BX+04]
SYS0593: ADD AX,CX
SYS0595: ADC DX,+00
SYS0598: MOV AX,DX
SYS059A: RETF 0002
```

See also: [RandSeed](../DATA.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)