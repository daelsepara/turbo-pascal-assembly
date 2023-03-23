# 043B Write to file with additional error checking

```
SYS:043B 8BDC          MOV	BX,SP
SYS:043D 1E            PUSH	DS
SYS:043E 36            SS:
SYS:043F C47F04        LES	DI,[BX+04]
SYS:0442 26            ES:
SYS:0443 C5550C        LDS	DX,[DI+0C]
SYS:0446 33C9          XOR	CX,CX
SYS:0448 26            ES:
SYS:0449 874D08        XCHG	CX,[DI+08]
SYS:044C 26            ES:
SYS:044D 8B1D          MOV	BX,[DI]
SYS:044F B440          MOV	AH,40
SYS:0451 CD21          INT	21
SYS:0453 7207          JB	045C
SYS:0455 2BC1          SUB	AX,CX
SYS:0457 7403          JZ	045C
SYS:0459 B86500        MOV	AX,0065
SYS:045C 1F            POP	DS
SYS:045D CA0400        RETF	0004
```

[Back](../README.md)