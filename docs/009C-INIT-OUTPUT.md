# 009C Initialize Output Text File

Initializes **[Output](TEXT-FILE-TYPE.md)** File. This is similar to the following Pascal routine:

```pascal
Assign(Output, '');
Rewrite(Output);
```

```nasm
SYS:009C B85001        MOV	AX,Output
SYS:009F 1E            PUSH	DS
SYS:00A0 50            PUSH	AX
SYS:00A1 1E            PUSH	DS
SYS:00A2 50            PUSH	AX
```

Push pointer to **[Output](TEXT-FILE-TYPE.md)** twice. The first one is for **[SYS:0369 Reset Function](0364-RESET-REWRITE-FUNC.md)**, the second one for **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS:00A3 B86302        MOV	AX,SYS:0263
SYS:00A6 0E            PUSH	CS
SYS:00A7 50            PUSH	AX
SYS:00A8 0E            PUSH	CS
SYS:00A9 E83A02        CALL	02E6
```

Assigns empty string **[''](0263-DATA-COPYRIGHT.md)** in **[SYS:0263](0263-DATA-COPYRIGHT.md)** to **[Output](TEXT-FILE-TYPE.md)** on a call to **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS:00AC 0E            PUSH	CS
SYS:00AD E8B902        CALL	0369
```

Calls **[SYS:0369 Rewrite Function](0364-RESET-REWRITE-FUNC.md)** and use **[Output](TEXT-FILE-TYPE.md)**'s **[OpenFunc](TEXT-FILE-TYPE.md)** to open it.

```nasm
SYS:00B0 CB            RETF
```

Initialization is complete. Return to **[MAIN](MAIN.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md), [SYS:0263 Empty string](0263-DATA-COPYRIGHT.md), [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md), [SYS:0369 Reset/Rewrite Function](0364-RESET-REWRITE-FUNC.md) or Go [Back](../README.md)

