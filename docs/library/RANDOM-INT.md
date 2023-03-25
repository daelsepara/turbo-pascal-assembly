# Random(A: *Int*): *Int* Function

Returns a random number (*Int/Word*, 2 bytes). It takes one parameter **A** and returns **X** in the range **0** <= *X* < **A**.

## Pascal program used to generate the compiled code

```
program main;
        var A: Integer;
        begin
                Randomize;
                A := Random(100);
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
CODE:0014 B86400        MOV	AX,0064
CODE:0017 50            PUSH	AX
```

Push parameter (**100**) on top of the stack: **AX** = **100**/**64h**.

```
CODE:0018 9A80057307    CALL	SYS:0580
```

Call to **SYS:0580 Random (Int)** (see below). Returns *Int* result in **AX**.

```
CODE:001D A35000        MOV	[OFFSET A],AX
```

A = **A**.

```
CODE:0020 5D            POP	BP
CODE:0021 31C0          XOR	AX,AX
CODE:0023 9A16017307    CALL	SYS:0116
```

Exit program with code 0.

## SYS:0580 Random (*Int*)

```
SYS:0580 E85A00        CALL	05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

# Stack after SYS:0580

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |Return Address (OFFSET)                   |
|SP+02|Return Address (SEGMENT)                  |
|SP+04|**A**: *Int* parameter                    |


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

See also: [RandSeed](../DATA.md), [SYS:0000 System Library Initialization](../0000-INIT.md), [SYS:02CD Check Stack](../02CD-CHECK-STACK.md), [SYS:0116 Program Exit](../0113-CTRL-C-HANDLER.md), [Randomize](RANDOMIZE.md), [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md).