# Random Function: (*Real*) 

Returns a random number (*Real* type, 6-bytes) in the range **0.0** <= *X* < **1.0**.

## Pascal program used to generate the compiled code

```
program main;
        var A: Real;
        begin
                Randomize;
                A := Random;
        end.
end program;
```

## Main program disassembly

```
CODE:0000 9A00007307    CALL	SYS:0000
CODE:0005 55            PUSH	BP
CODE:0006 89E5          MOV	BP,SP
CODE:0008 31C0          XOR	AX,AX
CODE:000A 9ACD027307    CALL	SYS:02CD
```

Initialization.

```
CODE:000F 9A15067307    CALL	SYS:0615
```

Call to **[Randomize](RANDOMIZE.md)** (no parameters).

```
CODE:0014 9A9D057307    CALL	SYS:059D
```

Call to **SYS:059D Random: Real** (see below). Returns *Real* result in **DX**:**BX**:**AX**.

```
CODE:0019 A35000        MOV	[OFFSET A+00],AX
CODE:001C 891E5200      MOV	[OFFSET A+02],BX
CODE:0020 89165400      MOV	[OFFSET A+04],DX
```

A = **DX**:**BX**:**AX**.

```
CODE:0024 5D            POP	BP
CODE:0025 31C0          XOR	AX,AX
CODE:0027 9A16017307    CALL	SYS:0116
```

Exit program with code 0.

## SYS:059D Random: **Real**

```
0773:059D E83D00        CALL	05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

```
0773:05A0 93            XCHG	BX,AX
0773:05A1 B88000        MOV	AX,0080
0773:05A4 B92000        MOV	CX,0020
0773:05A7 F6C680        TEST	DH,80
0773:05AA 750A          JNZ	05B6
0773:05AC D1E3          SHL	BX,1
0773:05AE D1D2          RCL	DX,1
0773:05B0 FEC8          DEC	AL
0773:05B2 E2F3          LOOP	05A7
0773:05B4 32C0          XOR	AL,AL
0773:05B6 80E67F        AND	DH,7F
0773:05B9 CB            RETF
```

See also: [RandSeed](../DATA.md), [SYS:0000 System Library Initialization](../0000-INIT.md), [SYS:02CD Check Stack](../02CD-CHECK-STACK.md), [SYS:0116 Program Exit](../0113-CTRL-C-HANDLER.md), [Randomize](RANDOMIZE.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md).