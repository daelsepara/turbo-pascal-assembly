# 0113 Ctrl-C / Int 23h Handler / Program Exit

This CTRL-C handler is also the **Exit Program** subroutine. When called at **SYS:0116**, it is assumed that the error code is already set prior in AX. The CTRL-C handler (starting at **SYS:0113)** simply performs the same exit procedure but with [ExitCode](DATA.md) = -1 (FFh).

```
SYS:0113 B8FF00        MOV	AX,00FF
```

Set ExitCode to FFh (-1). This only happens when the Program Execution is interrupted by pressing CTRL-C.

```
SYS:0116 33C9          XOR	CX,CX
SYS:0118 33DB          XOR	BX,BX
```

**SYS:0116** is the entry point for the Program Exit. It first clears the [ExitProc](DATA.md) Address in BX:CX.

```
SYS:011A BACA07        MOV	DX,DATA
SYS:011D 8EDA          MOV	DS,DX
```

**SYS:011A** is the entry point for stopping the program with an [ExitCode](DATA.md) in AX.

```
SYS:011F FB            STI
SYS:0120 A33200        MOV	[ExitCode],AX
SYS:0123 8BC1          MOV	AX,CX
SYS:0125 0BC3          OR	AX,BX
SYS:0127 743D          JZ	0166
SYS:0129 A11000        MOV	AX,[OvrLoadList]
SYS:012C 0BC0          OR	AX,AX
SYS:012E 742F          JZ	015F
SYS:0130 8EC0          MOV	ES,AX
SYS:0132 26            ES:
SYS:0133 A11000        MOV	AX,[OvrLoadList]
SYS:0136 0BC0          OR	AX,AX
SYS:0138 741B          JZ	0155
SYS:013A 2BC3          SUB	AX,BX
SYS:013C 7717          JA	0155
SYS:013E F7D8          NEG	AX
SYS:0140 3D0010        CMP	AX,1000
SYS:0143 7310          JNB	0155
SYS:0145 BA1000        MOV	DX,0010
SYS:0148 F7E2          MUL	DX
SYS:014A 03C1          ADD	AX,CX
SYS:014C 7207          JB	0155
SYS:014E 26            ES:
SYS:014F 3B060800      CMP	AX,[OvrDebugPtr:SEG]
SYS:0153 7206          JB	015B
SYS:0155 26            ES:
SYS:0156 A11400        MOV	AX,[OvrEmsHandle]
SYS:0159 EBD1          JMP	012C
SYS:015B 8BC8          MOV	CX,AX
SYS:015D 8CC3          MOV	BX,ES
SYS:015F 2B1E3800      SUB	BX,[PrefixSeg]
SYS:0163 83EB10        SUB	BX,+10
SYS:0166 890E3400      MOV	[ErrorAddr:OFF],CX
SYS:016A 891E3600      MOV	[ErrorAddr:SEG],BX
SYS:016E C41E2E00      LES	BX,[ExitProc:OFF]
SYS:0172 8CC0          MOV	AX,ES
SYS:0174 0BC3          OR	AX,BX
SYS:0176 7413          JZ	018B
SYS:0178 33C0          XOR	AX,AX
SYS:017A A32E00        MOV	[ExitProc:OFF],AX
SYS:017D A33000        MOV	[ExitProc:SEG],AX
SYS:0180 A33C00        MOV	[InOutRes],AX
SYS:0183 B86E01        MOV	AX,016E
SYS:0186 0E            PUSH	CS
SYS:0187 50            PUSH	AX
SYS:0188 06            PUSH	ES
SYS:0189 53            PUSH	BX
SYS:018A CB            RETF
```

See also: [SYS:018B](018B-FLUSH-BUFFERS.md) or Go [Back](../README.md)