# Random(*Int*): *Int* Function

Returns a random number (***Int***/***Word***, 2 bytes). It takes one ***Int*** parameter and returns ***X*** in the range: **0** <= ***X*** < ***Int***.

```
SYS:0580 E85A00        CALL	05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

# Stack after SYS:0580

|Index|Contents                |
|-----|------------------------|
|SP   |Return Address (OFFSET) |
|SP+02|Return Address (SEGMENT)|
|SP+04|*Int* parameter         |


```
SYS:0583 8BDC          MOV	BX,SP
SYS:0585 8BCA          MOV	CX,DX
SYS:0587 36            SS:
SYS:0588 F76704        MUL	WORD PTR [BX+04]
SYS:058B 8BC1          MOV	AX,CX
SYS:058D 8BCA          MOV	CX,DX
SYS:058F 36            SS:
SYS:0590 F76704        MUL	WORD PTR [BX+04]
SYS:0593 03C1          ADD	AX,CX
SYS:0595 83D200        ADC	DX,+00
SYS:0598 8BC2          MOV	AX,DX
SYS:059A CA0200        RETF	0002
```

See also: [RandSeed](../DATA.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)