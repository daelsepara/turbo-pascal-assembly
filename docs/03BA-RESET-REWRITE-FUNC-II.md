# 03BA Reset/Rewrite Function (II)

```
SYS:03BA B000          MOV	AL,00
SYS:03BC EB02          JMP	03C0
```

Entry point using using DOS Read-Only Access Code (00h).

```
SYS:03BE B001          MOV	AL,01
```

Entry point using using DOS Write-Only Access Code (01h).

```
SYS:03C0 55            PUSH	BP
```

Use **BP** to index items on the stack.

Upon entering this subroutine from any of the entry points: **SYS:03BA Read-Only** or **SYS:03BE Write-Only**, and saving **BP**, the stack looks like this:

## Stack after SYS:03C0
|Index|Contents                                  |
|-----|------------------------------------------|
|BP   |Old **BP** value                          |
|BP+02|Return Address (OFFSET)                   |
|BP+04|Return Address (SEGMENT)                  |
|BP+06|Pointer to File/Text Record Data (OFFSET) |
|BP+08|Pointer to File/Text Record Data (SEGMENT)|

```
SYS:03C1 8BEC          MOV	BP,SP
SYS:03C3 C47E06        LES	DI,[BP+06]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```
SYS:03C6 26            ES:
SYS:03C7 817D02B1D7    CMP	WORD PTR [DI+02],fmInput
SYS:03CC 7418          JZ	03E6
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Reset()](FILE-MODES.md)**.

```
SYS:03CE 26            ES:
SYS:03CF 817D02B2D7    CMP	WORD PTR [DI+02],fmOutput
SYS:03D4 7408          JZ	03DE
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Rewrite()](FILE-MODES.md)**.

```
SYS:03D6 C7063C006700  MOV	WORD PTR [InOutRes],0067
SYS:03DC EB18          JMP	03F6
```

Exit with an error [67: File not open](ERROR-CODES.md).

```
SYS:03DE 50            PUSH	AX
SYS:03DF BB1400        MOV	BX,0014
SYS:03E2 E81500        CALL	03FA
SYS:03E5 58            POP	AX
```

Call **[BX = 0014 InOutFunc()](TEXT-FILE-TYPE.md)** using **[SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md)**. This is used to flush the buffer, i.e. write any bytes still left in the buffer.

```
SYS:03E6 0AC0          OR	AL,AL
SYS:03E8 740C          JZ	03F6
```

If **[Mode](FILE-MODES.md)** = **[fmInput](FILE-MODES.md)** and Access is **Read-Only** the return success, otherwise check for errors on **[Mode](FILE-MODES.md)** = **[fmOutput](FILE-MODES.md)**.

```
SYS:03EA BB1C00        MOV	BX,001C
SYS:03ED E80A00        CALL	03FA
```

Close the **[File](TEXT-FILE-TYPE.md)** on errors with a call to its **[BX = 001C CloseFunc()](TEXT-FILE-TYPE.md)** using [SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md).

```
SYS:03F0 26            ES:
SYS:03F1 C74502B0D7    MOV	WORD PTR [DI+02],fmClosed
```

Mark **[File](TEXT-FILE-TYPE.md)** as **[fmClosed](FILE-MODES.md)**.

```
SYS:03F6 5D            POP	BP
SYS:03F7 CA0400        RETF	0004
```

Return to caller and pop-off parameters.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md), [SYS:043B Write to File Function](043B-WRITE-TO-FILE-FUNC.md), [SYS:0460 Write Function](0460-WRITE-FUNC.md) or go [back](../README.md)