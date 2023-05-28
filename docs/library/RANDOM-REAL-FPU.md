# Random Function: (*Real*), FPU: 87/287/387

Returns a random number (***Real*** type, 6-bytes) in the range **0.0** <= ***X*** < **1.0**. It is the same as the [**Random:** ***Real***](RANDOM-REAL.md) function but uses **FPU** code. The main program is compiled with **8087 emulation** disabled (**-$E-**) and **8087/80287/80387 code** enabled (**-$N+**):

```cmd
TPC.EXE -GS -GP -GD -$D+ -$E- -$N+ MAIN
```

## Random: *Real* using FPU

```nasm
CODE0019: CALL SYS:0D27
CODE001E: CALL SYS:098E
```

Random(*Real*) is composed of two separate calls to **System Library** subroutines (**SYS:0D27**) and (**SYS:098E**). The results (***Real***, 6 bytes) is returned in **DX**:**BX**:**AX** where:
- **DX** = High Word
- **BX** = Middle Word
- **AX** = Low Word

## Random: *Real* using FPU (I)
```nasm
SYS0D27: CALL 0D4A
```

Call random number generator engine at **SYS:0D4A**.

```nasm
SYS0D2A: FILD CS:WORD PTR [0D48]
SYS0D30: FILD DWORD PTR [RandSeed]
SYS0D35: FADD CS:DWORD PTR [0D44]
SYS0D3A: FSCALE
SYS0D3E: FSTP ST(1)
SYS0D41: FWAIT
SYS0D43: RETF
```

## DWORD in SYS:0D44
```
SYS0D44:  00 00 00 4F
```

## WORD in SYS:0D48
```
SYS0D48:  E0 FF
```

## SYS:0D4A Random Number Generator Engine

This is the same random number generator engine (see: **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)** for analysis). The location of this routine has been displaced due to inclusion of additional routines in the **System Library**.

```nasm
SYS0D4A: MOV AX,[RandSeed.Low]
SYS0D4D: MOV BX,[RandSeed.High]
SYS0D51: MOV CX,AX
SYS0D53: CS:
SYS0D54: MUL WORD PTR [0D80]
SYS0D58: SHL CX,1
SYS0D5A: SHL CX,1
SYS0D5C: SHL CX,1
SYS0D5E: ADD CH,CL
SYS0D60: ADD DX,CX
SYS0D62: ADD DX,BX
SYS0D64: SHL BX,1
SYS0D66: SHL BX,1
SYS0D68: ADD DX,BX
SYS0D6A: ADD DH,BL
SYS0D6C: MOV CL,05
SYS0D6E: SHL BX,CL
SYS0D70: ADD DH,BL
SYS0D72: ADD AX,0001
SYS0D75: ADC DX,+00
SYS0D78: MOV [RandSeed.Low],AX
SYS0D7B: MOV [RandSeed.High],DX
SYS0D7F: RET
```

## WORD in SYS:0D80
```
SYS0D80:  05 84
```

Magic number used bye the random number generator engine (**8405h**/**33797**).

## Random: *Real* using FPU (II)

```nasm
SYS098E: SUB SP,+0A
```

Reserve 10 bytes on the stack.

```nasm
SYS0991: MOV BX,SP
```

Copy offset to this location (**SP**) into **BX**.

```nasm
SYS0993: FSTP SS:TBYTE PTR [BX]
SYS0997: FWAIT
```

Load 10 bytes from FPU stack into reserved location [**BX**].

```nasm
SYS0999: ADD SP,+02
```

Ignore **MSB**

```nasm
SYS099C: POP CX
SYS099D: POP BX
SYS099E: POP DX
SYS099F: POP AX
```

Load into **AX**:**DX**:**BX**:**CX**.

```nasm
SYS09A0: MOV DI,AX
SYS09A2: AND AX,7FFF
SYS09A5: SUB AX,3F7E
SYS09A8: JBE 09C4
SYS09AA: OR AH,AH
SYS09AC: JNZ 09CF
SYS09AE: MOV AH,CH
SYS09B0: SHL CL,1
SYS09B2: ADC AH,00
SYS09B5: ADC BX,+00
SYS09B8: ADC DX,+00
SYS09BB: JB 09CB
SYS09BD: SHL DX,1
SYS09BF: SHL DI,1
SYS09C1: RCR DX,1
SYS09C3: RETF
SYS09C4: XOR AX,AX
SYS09C6: XOR BX,BX
SYS09C8: XOR DX,DX
SYS09CA: RETF
SYS09CB: INC AL
SYS09CD: JNZ 09BD
SYS09CF: MOV AX,00CD
SYS09D2: JMP 010F
```

See also: [RandSeed](../DATA.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)