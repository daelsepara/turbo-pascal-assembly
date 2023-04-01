# Random Function: (*Real*), FPU: 87/287/387 

Returns a random number (***Real*** type, 6-bytes) in the range **0.0** <= ***X*** < **1.0**. It is the same as the [**Random:** ***Real***](RANDOM-REAL.md) function but uses **FPU** code. The main program is compiled with **8087 emulation** disabled (**-$E-**) and **8087/80287/80387 code** enabled (**-$N+**):

```
TPC.EXE -GS -GP -GD -$D+ -$E- -$N+ MAIN
```

## Random: *Real* using FPU

```
CODE:0019 9A270D7407    CALL	SYS:0D27
CODE:001E 9A8E097407    CALL	SYS:098E
```

Random(*Real*) is composed of two separate calls to **System Library** subroutines (**SYS:0D27**) and (**SYS:098E**). The results (***Real***, 6 bytes) is returned in **DX**:**BX**:**AX** where:
- **DX** = High Word
- **BX** = Middle Word
- **AX** = Low Word

## Random: *Real* using FPU (I)
```
SYS:0D27 E82000        CALL	0D4A
```

Call random number generator engine at **SYS:0D4A**.

```
SYS:0D2A CD3C9F06480D  FILD CS:WORD PTR [0D48]
SYS:0D30 CD37063E00    FILD DWORD PTR [RandSeed]
SYS:0D35 CD3C9806440D  FADD CS:DWORD PTR [0D44]
SYS:0D3A CD35FD        FSCALE
SYS:0D3E CD39D9        FSTP ST(1)
SYS:0D41 CD3D          FWAIT
SYS:0D43 CB            RETF
```

## DWORD in SYS:0D44
```
SYS:0D44  00 00 00 4F
```

## WORD in SYS:0D48
```
SYS:0D48  E0 FF
```

## SYS:0D4A Random Number Generator Engine

This is the same random number generator engine (see: **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)** for analysis). The location of this routine has been displaced due to inclusion of additional routines in the **System Library**.

```
SYS:0D4A A13E00        MOV	AX,[RandSeed.Low]
SYS:0D4D 8B1E4000      MOV	BX,[RandSeed.High]
SYS:0D51 8BC8          MOV	CX,AX
SYS:0D53 2E            CS:
SYS:0D54 F726800D      MUL	WORD PTR [0D80]
SYS:0D58 D1E1          SHL	CX,1
SYS:0D5A D1E1          SHL	CX,1
SYS:0D5C D1E1          SHL	CX,1
SYS:0D5E 02E9          ADD	CH,CL
SYS:0D60 03D1          ADD	DX,CX
SYS:0D62 03D3          ADD	DX,BX
SYS:0D64 D1E3          SHL	BX,1
SYS:0D66 D1E3          SHL	BX,1
SYS:0D68 03D3          ADD	DX,BX
SYS:0D6A 02F3          ADD	DH,BL
SYS:0D6C B105          MOV	CL,05
SYS:0D6E D3E3          SHL	BX,CL
SYS:0D70 02F3          ADD	DH,BL
SYS:0D72 050100        ADD	AX,0001
SYS:0D75 83D200        ADC	DX,+00
SYS:0D78 A33E00        MOV	[RandSeed.Low],AX
SYS:0D7B 89164000      MOV	[RandSeed.High],DX
SYS:0D7F C3            RET
```

## WORD in SYS:0D80
```
SYS:0D80  05 84
```

Magic number used bye the random number generator engine (**8405h**/**33797**).

## Random: *Real* using FPU (II)

```
SYS:098E 83EC0A        SUB	SP,+0A
```

Reserve 10 bytes on the stack.

```
SYS:0991 8BDC          MOV	BX,SP
```

Copy offset to this location (**SP**) into **BX**.

```
SYS:0993 CD3C5B3F      FSTP SS:TBYTE PTR [BX]
SYS:0997 CD3D          FWAIT
```

Load 10 bytes from FPU stack into reserved location [**BX**].

```
SYS:0999 83C402        ADD	SP,+02
```

Ignore **MSB**

```
SYS:099C 59            POP	CX
SYS:099D 5B            POP	BX
SYS:099E 5A            POP	DX
SYS:099F 58            POP	AX
```

Load into **AX**:**DX**:**BX**:**CX**.

```
SYS:09A0 8BF8          MOV	DI,AX
SYS:09A2 25FF7F        AND	AX,7FFF
SYS:09A5 2D7E3F        SUB	AX,3F7E
SYS:09A8 761A          JBE	09C4
SYS:09AA 0AE4          OR	AH,AH
SYS:09AC 7521          JNZ	09CF
SYS:09AE 8AE5          MOV	AH,CH
SYS:09B0 D0E1          SHL	CL,1
SYS:09B2 80D400        ADC	AH,00
SYS:09B5 83D300        ADC	BX,+00
SYS:09B8 83D200        ADC	DX,+00
SYS:09BB 720E          JB	09CB
SYS:09BD D1E2          SHL	DX,1
SYS:09BF D1E7          SHL	DI,1
SYS:09C1 D1DA          RCR	DX,1
SYS:09C3 CB            RETF
SYS:09C4 33C0          XOR	AX,AX
SYS:09C6 33DB          XOR	BX,BX
SYS:09C8 33D2          XOR	DX,DX
SYS:09CA CB            RETF
SYS:09CB FEC0          INC	AL
SYS:09CD 75EE          JNZ	09BD
SYS:09CF B8CD00        MOV	AX,00CD
SYS:09D2 E93AF7        JMP	010F
```

See also: [RandSeed](../DATA.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)