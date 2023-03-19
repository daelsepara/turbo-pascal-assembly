# 0460 Unknown

```
SYS:0460 8BDC          MOV	BX,SP
SYS:0462 1E            PUSH	DS
SYS:0463 36            SS:
SYS:0464 C47F04        LES	DI,[BX+04]
SYS:0467 26            ES:
SYS:0468 C5550C        LDS	DX,[DI+0C]
SYS:046B 33C9          XOR	CX,CX
SYS:046D 26            ES:
SYS:046E 874D08        XCHG	CX,[DI+08]
SYS:0471 26            ES:
SYS:0472 8B1D          MOV	BX,[DI]
SYS:0474 B440          MOV	AH,40
SYS:0476 CD21          INT	21
SYS:0478 7202          JB	047C
SYS:047A 33C0          XOR	AX,AX
SYS:047C 1F            POP	DS
SYS:047D CA0400        RETF	0004
```

[Go back](../README.md)