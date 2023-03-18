# Main Program

## Registers
```
AX=FFFF  BX=0000  CX=0B64  DX=0000  SP=4000  BP=0000  SI=0000  DI=0000
DS=075E  ES=075E  SS=07F4  CS=076E  IP=0000   NV UP EI PL NZ NA PO NC
```

## Segments

When the program is loaded in memory, ES AND DS points to the Program Segment Prefix (PSP). Stack size is around 16384 kilobytes (SP = 4000h). The system library follows CODE segment immediately but is still aligned on paragraph (16 bytes) boundaries.

|Segment|Value|Distance from CODE segment|
|-------|-----|--------------------------|
|PSP    | 075E|  16 paragraphs above CODE|
|CODE   | 076E|                          |
|SYSTEM | 0770|   2 paragraphs below CODE|
|DATA   | 07CA|  92 paragraphs below CODE|
|STACK  | 07F4| 134 paragraphs below CODE|

Where distance from code segment:
```
(segment) - (code segment) = distance in paragraphs (16 bytes)
```

## Source code

```
CODE:0000 9A00007007    CALL	SYS:0000
```

Initialize System Library: [SYS:0000](0000-INIT.md)

```
CODE:0005 55            PUSH	BP
CODE:0006 89E5          MOV	BP,SP
```

Probably like other compiled high-level languages, Turbo Pascal has a lot of stack manipulation. This sequence of statements PUSH BP/MOV BP, SP is like a defining signature of compiled code. It's likely that the main program is a callable function and receives parameters (e.g. argc, argv) on the stack right next to the return code. BP is used to address these parameters. It can be observed in Turbo Pascal, the compiler takes special care of BP and SP while playing fast and loose with the other registers.

```
CODE:0008 31C0          XOR	AX,AX
CODE:000A 9ACD027007    CALL	SYS:02CD
```

Checks the size of the stack: [SYS:02CD](02CD-CHECK-STACK.md). Exits with an error message if stack size is above the limit.


```
CODE:000F 5D            POP	BP
```

This balances the stack from PUSH BP/MOV BP, SP above. Again, this seems to be a defining feature of compiled code.

```
CODE:0010 31C0          XOR	AX,AX
CODE:0012 9A16017007    CALL	SYS:0116
```

Call Exit Function. AX contains the exit code (AL = 0, no error)


[Back](README.md)