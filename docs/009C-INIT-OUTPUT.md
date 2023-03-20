# 009C Initialize Output

```
SYS:009C B85001        MOV	AX,OFFSET OUTPUT
SYS:009F 1E            PUSH	DS
SYS:00A0 50            PUSH	AX
SYS:00A1 1E            PUSH	DS
SYS:00A2 50            PUSH	AX
SYS:00A3 B86302        MOV	AX,0263
SYS:00A6 0E            PUSH	CS
SYS:00A7 50            PUSH	AX
SYS:00A8 0E            PUSH	CS
SYS:00A9 E83A02        CALL	02E6
SYS:00AC 0E            PUSH	CS
SYS:00AD E8B902        CALL	0369
SYS:00B0 CB            RETF
```

See also: [SYS:0263 Copyright string (Invisible)](0263-DATA-COPYRIGHT.md), [SYS:02E6](02E6-UNKNOWN.md), [SYS:0369](0364-UNKNOWN.md) or Go [Back](../README.md)
