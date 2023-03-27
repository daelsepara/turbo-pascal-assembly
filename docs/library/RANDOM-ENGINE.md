# Random Number Generator Engine

```
SYS:05DD A13E00        MOV	AX,[RandSeed.Low]
SYS:05E0 8B1E4000      MOV	BX,[RandSeed.High]
```

Load **[RandomSeed](../DATA.md)** (*LongInt*) into **BX**:**AX**.

```
SYS:05E4 8BC8          MOV	CX,AX
SYS:05E6 2E            CS:
SYS:05E7 F7261306      MUL	WORD PTR [0613]
SYS:05EB D1E1          SHL	CX,1
SYS:05ED D1E1          SHL	CX,1
SYS:05EF D1E1          SHL	CX,1
SYS:05F1 02E9          ADD	CH,CL
SYS:05F3 03D1          ADD	DX,CX
SYS:05F5 03D3          ADD	DX,BX
SYS:05F7 D1E3          SHL	BX,1
SYS:05F9 D1E3          SHL	BX,1
SYS:05FB 03D3          ADD	DX,BX
SYS:05FD 02F3          ADD	DH,BL
SYS:05FF B105          MOV	CL,05
SYS:0601 D3E3          SHL	BX,CL
SYS:0603 02F3          ADD	DH,BL
SYS:0605 050100        ADD	AX,0001
SYS:0608 83D200        ADC	DX,+00
```

```
SYS:060B A33E00        MOV	[RandSeed.Low],AX
SYS:060E 89164000      MOV	[RandSeed.Low],DX
```

Update **[RandSeed](../DATA.md)** (*LongInt*) with **DX**:**AX**.

```
SYS:0612 C3            RET
```

Return to calling **Random** function (*[Real](RANDOM-REAL.md)*/*[Int](RANDOM-INT.md)*). Because it returns with a **NEAR RET**, it can only be called from within the system library.

```
SYS:0613 05 84
```

RNG Magic Number **8405h**/**33797**. Used internally.

See also: [RandSeed](../DATA.md), or go [back](../../README.md)
