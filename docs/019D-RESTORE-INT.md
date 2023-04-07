# 019D Restore interrupt vectors

```nasm
SYS019D: MOV DI,0250
SYS01A0: MOV SI,0239
SYS01A3: MOV CX,0013
SYS01A6: NOP
SYS01A7: CLD
SYS01A8: CS:
SYS01A9: LODSB
SYS01AA: MOV AH,25
SYS01AC: PUSH DS
SYS01AD: LDS DX,[DI]
SYS01AF: INT 21
SYS01B1: POP DS
SYS01B2: ADD DI,+04
SYS01B5: LOOP 01A7
```

Restores the vectors for the 19 interrupts (listed in **[SYS:0239-024B](0239-INTERRUPT-LIST.md)**) saved on **[DATA:0250](DATA.md)** and modified during system library initialization. It uses **DOS INT 21H AH = 25h** service. It expects **DS**:**DX** refers to the original interrupt vector assigned to the interrupt number store in **AL**.

Code continues to **[SYS:01B7 Print Runtime Error](01B7-PRINT-RUNTIME-ERR.md)**.

[Go back](../README.md)