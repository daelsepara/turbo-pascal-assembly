# 009C Initialize Output Text File

Initializes **[Output](TEXT-FILE-TYPE.md)** File. This is similar to the following Pascal routine:

```pascal
Assign(Output, '');
Rewrite(Output);
```

```nasm
SYS009C: MOV AX,OFFSET Output
SYS009F: PUSH DS
SYS00A0: PUSH AX
SYS00A1: PUSH DS
SYS00A2: PUSH AX
```

Push pointer to **[Output](TEXT-FILE-TYPE.md)** twice. The first one is for **[SYS:0369 Reset Function](0364-RESET-REWRITE-FUNC.md)**, the second one for **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS00A3: MOV AX,0263
SYS00A6: PUSH CS
SYS00A7: PUSH AX
SYS00A8: PUSH CS
SYS00A9: CALL 02E6
```

Assigns empty string **[''](0263-DATA-COPYRIGHT.md)** in **[SYS:0263](0263-DATA-COPYRIGHT.md)** to **[Output](TEXT-FILE-TYPE.md)** on a call to **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS00AC: PUSH CS
SYS00AD: CALL 0369
```

Calls **[SYS:0369 Rewrite Function](0364-RESET-REWRITE-FUNC.md)** and use **[Output](TEXT-FILE-TYPE.md)**'s **[OpenFunc](TEXT-FILE-TYPE.md)** to open it.

```nasm
SYS00B0: RETF
```

Initialization is complete. Return to **[MAIN](MAIN.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md), [SYS:0263 Empty string](0263-DATA-COPYRIGHT.md), [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md), [SYS:0369 Reset/Rewrite Function](0364-RESET-REWRITE-FUNC.md) or Go [Back](../README.md)

