# 009C Initialize Output Text File

Initializes **Output** File. This is similar to the following Pascal routine:

```
Assign(Output, '');
Rewrite(Output);
```

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
```

Assigns empty string **[''](0263-DATA-COPYRIGHT.md)** in [SYS:0263](0263-DATA-COPYRIGHT.md) to **Output** on a call to [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md).

```
SYS:00AC 0E            PUSH	CS
SYS:00AD E8B902        CALL	0369
SYS:00B0 CB            RETF
```

Calls [SYS:0369 Rewrite Function](0364-RESET-REWRITE-FUNC.md) and use **Output**'s **[OpenFunc](TextFileType.md)** to open it.

See also: [Text File Type](TextFileType.md), [SYS:0263 Copyright string (Invisible)](0263-DATA-COPYRIGHT.md), [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md), [SYS:0369 Reset/Rewrite Function](0364-RESET-REWRITE-FUNC.md) or Go [Back](../README.md)

