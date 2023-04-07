# 00DB Critical Error Handler

This is the Turbo Pascal's critical error handler that replaces DOS's.

These registers are set upon entry to this handler:
- **AH** = Error information
- **AL** = DRIVE NUMBER
- **DI** = Error Code
- **BP**:**SI** = Pointer to device drive header.

## The stack on entry

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |DOS IP Register                           |
|SP+02|DOS CS Register                           |
|SP+04|DOS Flags Register                        |
|SP+06|Original Caller's AX register             |
|SP+08|Original Caller's BX register             |
|SP+0A|Original Caller's CX register             |
|SP+0C|Original Caller's DX register             |
|SP+0E|Original Caller's SI register             |
|SP+10|Original Caller's DI register             |
|SP+12|Original Caller's BP register             |
|SP+14|Original Caller's DS register             |
|SP+16|Original Caller's ES register             |
|SP+18|Original Caller's IP register             |
|SP+1A|Original Caller's CS register             |
|SP+1C|Original Caller's Flags register          |

```nasm
SYS:00DB FB            STI
```

Enable interrupts.

```nasm
SYS:00DC 83C406        ADD	SP,+06
SYS:00DF 58            POP	AX
```

Adjust stack and get original caller's **AX** register.

```nasm
SYS:00E0 83E71F        AND	DI,+1F
SYS:00E3 81C79600      ADD	DI,0096
```

Adjust error code (clear upper bits 5-15) and add 150 (96h) ???.

```nasm
SYS:00E7 80FC39        CMP	AH,39
SYS:00EA 7303          JNB	00EF
```

Determine which **DOS** service was called when critical error occured. Check if any action can be made to address this.

```nasm
SYS:00EC BFFFFF        MOV	DI,FFFF
```

Set **DI** = -1 (FFFFh), i.e. error in **DOS** service cannot be handled (??).

```nasm
SYS:00EF 57            PUSH	DI
```

Set original caller's AX by pushing **DI** to the stack.

```nasm
SYS:00F0 B454          MOV	AH,54
SYS:00F2 CD21          INT	21
```

Get read-after-write verify flag **DOS INT 21h AH = 54h**.

On return:
- **AL** = verify flag:
  - 00h verify off
  - 01h verify on

It seems that the result is unused.

```nasm
SYS:00F4 8BEC          MOV	BP,SP
```

Use **BP** to address the stack.

```nasm
SYS:00F6 804E1601      OR	BYTE PTR [BP+16],01
```

Set original caller's **CF** flag to 1.

```nasm
SYS:00FA 58            POP	AX
SYS:00FB 5B            POP	BX
SYS:00FC 59            POP	CX
SYS:00FD 5A            POP	DX
SYS:00FE 5E            POP	SI
SYS:00FF 5F            POP	DI
SYS:0100 5D            POP	BP
SYS:0101 1F            POP	DS
SYS:0102 07            POP	ES
```

Restore all registers to original caller's.

```nasm
SYS:0103 CF            IRET
```

Return to original caller.

[Back](../README.md)