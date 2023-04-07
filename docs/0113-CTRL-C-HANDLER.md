# 0113 Ctrl-C / Int 23h Handler / Program Exit

This CTRL-C handler is also the **Exit Program** subroutine. When called at **SYS:0116**, it is assumed that the error code is already set prior in AX. The CTRL-C handler (starting at **SYS:0113)** simply performs the same exit procedure but with [ExitCode](DATA.md) = -1 (FFh).

```nasm
SYS0113: B8FF00        MOV	AX,00FF
```

Set ExitCode to FFh (-1). This only happens when the Program Execution is interrupted by pressing CTRL-C.

```nasm
SYS0116: 33C9          XOR	CX,CX
SYS0118: 33DB          XOR	BX,BX
```

**SYS:0116** is the entry point for the Program Exit. It first clears the **[ExitProc](DATA.md)** Address in **BX**:**CX**.

```nasm
SYS011A: BACA07        MOV	DX,DATA
SYS011D: 8EDA          MOV	DS,DX
```

**SYS:011A** is also an entry point for stopping the program with an [ExitCode](DATA.md) in AX.

```nasm
SYS011F: FB            STI
```

Enable interrupts.

```nasm
SYS0120: A33200        MOV	[ExitCode],AX
```

Save error code in **AX** to **[ExitCode](DATA.md)**.

```nasm
SYS0123: 8BC1          MOV	AX,CX
SYS0125: 0BC3          OR	AX,BX
SYS0127: 743D          JZ	0166
```

Check if there is no **[ExitProc](DATA.md)** defined.

```nasm
SYS0129: A11000        MOV	AX,[OvrLoadList]
SYS012C: 0BC0          OR	AX,AX
SYS012E: 742F          JZ	015F
```

Check if there are no **overlays** (**[OvrLoadList](DATA.md)**) loaded.

```nasm
SYS0130: 8EC0          MOV	ES,AX
SYS0132: 26            ES:
SYS0133: A11000        MOV	AX,[OvrLoadList]
SYS0136: 0BC0          OR	AX,AX
SYS0138: 741B          JZ	0155
```

Get any overlays the current has loaded.

```nasm
SYS013A: 2BC3          SUB	AX,BX
SYS013C: 7717          JA	0155
SYS013E: F7D8          NEG	AX
SYS0140: 3D0010        CMP	AX,1000
SYS0143: 7310          JNB	0155
SYS0145: BA1000        MOV	DX,0010
SYS0148: F7E2          MUL	DX
SYS014A: 03C1          ADD	AX,CX
SYS014C: 7207          JB	0155
SYS014E: 26            ES:
SYS014F: 3B060800      CMP	AX,[OvrDebugPtr.Segment]
SYS0153: 7206          JB	015B
```

???

```nasm
SYS0155: 26            ES:
SYS0156: A11400        MOV	AX,[OvrEmsHandle]
SYS0159: EBD1          JMP	012C
```

Load handle of overlays loaded in expanded memory (**[OvrEmsHandle](DATA.md)**) into **AX** then go back to **SYS:012C**.

```nasm
SYS015B: 8BC8          MOV	CX,AX
SYS015D: 8CC3          MOV	BX,ES
```

Copy address in **ES**:**AX** to **BX**:**CX**.

```nasm
SYS015F: 2B1E3800      SUB	BX,[PrefixSeg]
SYS0163: 83EB10        SUB	BX,+10
```

Locate original **PSP** (usually 10h paragraphs below in memory).

```nasm
SYS0166: 890E3400      MOV	[ErrorAddr.Offset],CX
SYS016A: 891E3600      MOV	[ErrorAddr.Segment],BX
```

Load pointer in **BX**:**CX** (**PSP** Address) to **[ErrorAddr](DATA.md)**.

```nasm
SYS016E: C41E2E00      LES	BX,[ExitProc]
```

Get address of current **[ExitProc](DATA.md)** into **ES**:**BX**.

```nasm
SYS0172: 8CC0          MOV	AX,ES
SYS0174: 0BC3          OR	AX,BX
SYS0176: 7413          JZ	018B
```

If **[ExitProc](DATA.md)** is undefined then flush buffers ([SYS:018B Flush and Close Input and Output Files](018B-FLUSH-FILES.md)).

```nasm
SYS0178: 33C0          XOR	AX,AX
SYS017A: A32E00        MOV	[ExitProc.Offset],AX
SYS017D: A33000        MOV	[ExitProc.Segment],AX
SYS0180: A33C00        MOV	[InOutRes],AX
```

Clears the following:
- **[ExitProc](DATA.md)**: Exit routine address
- **[InOutRes](DATA.md)**: I/O result

... of the current overlay.

```nasm
SYS0183: B86E01        MOV	AX,016E
SYS0186: 0E            PUSH	CS
SYS0187: 50            PUSH	AX
```

Setups up a return address to **SYS:016E**.

```nasm
SYS0188: 06            PUSH	ES
SYS0189: 53            PUSH	BX
```

Pushes the next **[ExitProc](DATA.md)** address in **ES**:**BX** and...

```nasm
SYS018A: CB            RETF
```

Exit from the next overlay in the chain or back to **SYS:016E**.

See also: [SYS:018B Flush and Close Input and Output Files](018B-FLUSH-FILES.md) or go [Back](../README.md)