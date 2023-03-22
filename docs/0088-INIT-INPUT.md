# 0088 Initialize Input Text File

Initializes **Input** File. This is similar to the following Pascal routine:

```
Assign(Input, '');
Reset (Input);
```

```
SYS:0088 B85000        MOV	AX,OFFSET Input
SYS:008B 1E            PUSH	DS
SYS:008C 50            PUSH	AX
SYS:008D 1E            PUSH	DS
SYS:008E 50            PUSH	AX
```

Push pointer to **[Input](DATA.md)** twice. The first one is for [SYS:0364 Reset Function](0364-RESET-REWRITE-FUNC.md), the second one for [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md).

```
SYS:008F B86302        MOV	AX,0263
SYS:0092 0E            PUSH	CS
SYS:0093 50            PUSH	AX
SYS:0094 0E            PUSH	CS
SYS:0095 E84E02        CALL	02E6
```

Assigns empty string **[''](0263-DATA-COPYRIGHT.md)** in [SYS:0263](0263-DATA-COPYRIGHT.md) to **[Input](DATA.md)** on a call to [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md).

```
SYS:0098 0E            PUSH	CS
SYS:0099 E8C802        CALL	0364
```

Calls [SYS:0263 Reset Function](0364-RESET-REWRITE-FUNC.md) and use **Input**'s **[OpenFunc](TextFileType.md)** to open it.

Code continues to [SYS:009C Initialize Output File](009C-INIT-OUTPUT.md).

See also: [Text File Type](TextFileType.md), [SYS:0263 Copyright string (Invisible)](0263-DATA-COPYRIGHT.md), [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md), [SYS:0364 Reset/Rewrite Function](0364-RESET-REWRITE-FUNC.md) or Go [Back](../README.md)