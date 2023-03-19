# 019D Restore interrupt vectors

```
SYS:019D BF5002        MOV	DI,0250
SYS:01A0 BE3902        MOV	SI,0239
SYS:01A3 B91300        MOV	CX,0013
SYS:01A6 90            NOP
SYS:01A7 FC            CLD
SYS:01A8 2E            CS:
SYS:01A9 AC            LODSB
SYS:01AA B425          MOV	AH,25
SYS:01AC 1E            PUSH	DS
SYS:01AD C515          LDS	DX,[DI]
SYS:01AF CD21          INT	21
SYS:01B1 1F            POP	DS
SYS:01B2 83C704        ADD	DI,+04
SYS:01B5 E2F0          LOOP	01A7
```

Restores the vectors for the 19 interrupts (listed in [SYS:0239-024B](0239-INTERRUPT-LIST.md)) saved on [DATA:0250](DATA.md) and modified during system library initialization. It uses DOS INT 21H AH=25H Service. It expects DS:DX refers to the original interrupt vector assigned to the interrupt number store in AL.

[Go back](README.md)