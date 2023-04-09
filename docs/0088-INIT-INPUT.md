# 0088 Initialize Input Text File

Initializes **[Input](TEXT-FILE-TYPE.md)** file. This is similar to the following Pascal routine:

```pascal
Assign(Input, '');
Reset (Input);
```

```nasm
SYS0088: MOV AX,OFFSET Input
SYS008B: PUSH DS
SYS008C: PUSH AX
SYS008D: PUSH DS
SYS008E: PUSH AX
```

Push pointer to **[Input](TEXT-FILE-TYPE.md)** twice. The first one is for **[SYS:0364 Reset Function](0364-RESET-REWRITE-FUNC.md)**, the second one for **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS008F: MOV AX,0263
SYS0092: PUSH CS
SYS0093: PUSH AX
SYS0094: PUSH CS
SYS0095: CALL 02E6
```

Assigns empty string **[''](0263-DATA-COPYRIGHT.md)** in **[SYS:0263](0263-DATA-COPYRIGHT.md)** to **[Input](TEXT-FILE-TYPE.md)** on a call to **[SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md)**.

```nasm
SYS0098: PUSH CS
SYS0099: CALL 0364
```

Calls **[SYS:0364 Reset Function](0364-RESET-REWRITE-FUNC.md)** and use **[Input](TEXT-FILE-TYPE.md)**'s **[OpenFunc](TEXT-FILE-TYPE.md)** to open it. Continues to **[SYS:009C Initialize Output File](009C-INIT-OUTPUT.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md), [SYS:0263 empty string](0263-DATA-COPYRIGHT.md), [SYS:02E6 Assign Function](02E6-ASSIGN-FUNC.md), [SYS:0364 Reset/Rewrite Function](0364-RESET-REWRITE-FUNC.md) or go [back](../README.md)