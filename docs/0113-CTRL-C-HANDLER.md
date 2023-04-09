# 0113 Ctrl-C / Int 23h Handler / Program Exit

This CTRL-C handler is also the **Exit Program** subroutine. When called at **SYS:0116**, it is assumed that the error code is already set prior in AX. The CTRL-C handler (starting at **SYS:0113)** simply performs the same exit procedure but with [ExitCode](DATA.md) = -1 (FFh).

```nasm
SYS0113: MOV AX,00FF
```

Set ExitCode to **FFh (-1)**. This only happens when the Program Execution is interrupted by pressing CTRL-C.

```nasm
SYS0116: XOR CX,CX
SYS0118: XOR BX,BX
```

**SYS:0116** is the entry point for the Program Exit. It first clears the **[ExitProc](DATA.md)** Address in **BX**:**CX**.

```nasm
SYS011A: MOV DX,DATA
SYS011D: MOV DS,DX
```

**SYS:011A** is also an entry point for stopping the program with an [ExitCode](DATA.md) in AX.

```nasm
SYS011F: STI
```

Enable interrupts.

```nasm
SYS0120: MOV [ExitCode],AX
```

Save error code in **AX** to **[ExitCode](DATA.md)**.

```nasm
SYS0123: MOV AX,CX
SYS0125: OR AX,BX
SYS0127: JZ 0166
```

Check if there is no **[ExitProc](DATA.md)** defined.

```nasm
SYS0129: MOV AX,[OvrLoadList]
SYS012C: OR AX,AX
SYS012E: JZ 015F
```

Check if there are no **overlays** (**[OvrLoadList](DATA.md)**) loaded.

```nasm
SYS0130: MOV ES,AX
SYS0132: ES:
SYS0133: MOV AX,[OvrLoadList]
SYS0136: OR AX,AX
SYS0138: JZ 0155
```

Get any overlays the current has loaded.

```nasm
SYS013A: SUB AX,BX
SYS013C: JA 0155
SYS013E: NEG AX
SYS0140: CMP AX,1000
SYS0143: JNB 0155
SYS0145: MOV DX,0010
SYS0148: MUL DX
SYS014A: ADD AX,CX
SYS014C: JB 0155
SYS014E: ES:
SYS014F: CMP AX,[OvrDebugPtr.Segment]
SYS0153: JB 015B
```

???

```nasm
SYS0155: ES:
SYS0156: MOV AX,[OvrEmsHandle]
SYS0159: JMP 012C
```

Load handle of overlays loaded in expanded memory (**[OvrEmsHandle](DATA.md)**) into **AX** then go back to **SYS:012C**.

```nasm
SYS015B: MOV CX,AX
SYS015D: MOV BX,ES
```

Copy address in **ES**:**AX** to **BX**:**CX**.

```nasm
SYS015F: SUB BX,[PrefixSeg]
SYS0163: SUB BX,+10
```

Locate original **PSP** (usually 10h paragraphs below in memory).

```nasm
SYS0166: MOV [ErrorAddr.Offset],CX
SYS016A: MOV [ErrorAddr.Segment],BX
```

Load pointer in **BX**:**CX** (**PSP** Address) to **[ErrorAddr](DATA.md)**.

```nasm
SYS016E: LES BX,[ExitProc]
```

Get address of current **[ExitProc](DATA.md)** into **ES**:**BX**.

```nasm
SYS0172: MOV AX,ES
SYS0174: OR AX,BX
SYS0176: JZ 018B
```

If **[ExitProc](DATA.md)** is undefined then flush buffers ([SYS:018B Flush and Close Input and Output Files](018B-FLUSH-FILES.md)).

```nasm
SYS0178: XOR AX,AX
SYS017A: MOV [ExitProc.Offset],AX
SYS017D: MOV [ExitProc.Segment],AX
SYS0180: MOV [InOutRes],AX
```

Clears the following:
- **[ExitProc](DATA.md)**: Exit routine address
- **[InOutRes](DATA.md)**: I/O result

... of the current overlay.

```nasm
SYS0183: MOV AX,016E
SYS0186: PUSH CS
SYS0187: PUSH AX
```

Setups up a return address to **SYS:016E**.

```nasm
SYS0188: PUSH ES
SYS0189: PUSH BX
```

Pushes the next **[ExitProc](DATA.md)** address in **ES**:**BX** and...

```nasm
SYS018A: RETF
```

Exit from the next overlay in the chain or back to **SYS:016E**.

See also: [SYS:018B Flush and Close Input and Output Files](018B-FLUSH-FILES.md) or go [Back](../README.md)