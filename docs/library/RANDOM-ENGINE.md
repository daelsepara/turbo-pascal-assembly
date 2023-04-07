# Random Number Generator Engine

```nasm
SYS05DD: A13E00        MOV	AX,[RandSeed.Low]
SYS05E0: 8B1E4000      MOV	BX,[RandSeed.High]
```

Load **[RandomSeed](../DATA.md)** (*LongInt*) into **BX**:**AX**.

```nasm
SYS05E4: 8BC8          MOV	CX,AX
SYS05E6: 2E            CS:
SYS05E7: F7261306      MUL	WORD PTR [0613]
SYS05EB: D1E1          SHL	CX,1
SYS05ED: D1E1          SHL	CX,1
SYS05EF: D1E1          SHL	CX,1
SYS05F1: 02E9          ADD	CH,CL
SYS05F3: 03D1          ADD	DX,CX
SYS05F5: 03D3          ADD	DX,BX
SYS05F7: D1E3          SHL	BX,1
SYS05F9: D1E3          SHL	BX,1
SYS05FB: 03D3          ADD	DX,BX
SYS05FD: 02F3          ADD	DH,BL
SYS05FF: B105          MOV	CL,05
SYS0601: D3E3          SHL	BX,CL
SYS0603: 02F3          ADD	DH,BL
SYS0605: 050100        ADD	AX,0001
SYS0608: 83D200        ADC	DX,+00
```

```nasm
SYS060B: A33E00        MOV	[RandSeed.Low],AX
SYS060E: 89164000      MOV	[RandSeed.High],DX
```

Update **[RandSeed](../DATA.md)** (*LongInt*) with **DX**:**AX**.

```nasm
SYS0612: C3            RET
```

Return to calling **Random** function (*[Real](RANDOM-REAL.md)*/*[Int](RANDOM-INT.md)*). Because it returns with a **NEAR RET**, it can only be called from within the system library.

```nasm
SYS0613: 05 84
```

Magic Number **8405h**/**33797**. Used internally.

See also: [RandSeed](../DATA.md), or go [back](../../README.md)
