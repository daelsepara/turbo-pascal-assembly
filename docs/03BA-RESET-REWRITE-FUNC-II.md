# 03BA Reset/Rewrite Function (II)

```nasm
SYS03BA: MOV AL,00
SYS03BC: JMP 03C0
```

Entry point using using DOS Read-Only Access Code (00h).

```nasm
SYS03BE: MOV AL,01
```

Entry point using using DOS Write-Only Access Code (01h).

```nasm
SYS03C0: PUSH BP
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

```nasm
SYS03C1: MOV BP,SP
SYS03C3: LES DI,[BP+06]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS03C6: ES:
SYS03C7: CMP WORD PTR [DI:Mode],fmInput
SYS03CC: JZ 03E6
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Reset()](FILE-MODES.md)**.

```nasm
SYS03CE: ES:
SYS03CF: CMP WORD PTR [DI:Mode],fmOutput
SYS03D4: JZ 03DE
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Rewrite()](FILE-MODES.md)**.

```nasm
SYS03D6: MOV WORD PTR [InOutRes],0067
SYS03DC: JMP 03F6
```

Exit with an error **[67h/103: File not open](ERROR-CODES.md)**.

```nasm
SYS03DE: PUSH AX
SYS03DF: MOV BX,0014
SYS03E2: CALL 03FA
SYS03E5: POP AX
```

Call **[BX = 0014 InOutFunc()](TEXT-FILE-TYPE.md)** using **[SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md)**. This is used to flush the buffer, i.e. write any bytes still left in the buffer.

```nasm
SYS03E6: OR AL,AL
SYS03E8: JZ 03F6
```

If **[Mode](FILE-MODES.md)** = **[fmInput](FILE-MODES.md)** and Access is **Read-Only** the return success, otherwise check for errors on **[Mode](FILE-MODES.md)** = **[fmOutput](FILE-MODES.md)**.

```nasm
SYS03EA: MOV BX,001C
SYS03ED: CALL 03FA
```

Close the **[File](TEXT-FILE-TYPE.md)** on errors with a call to its **[BX = 001C CloseFunc()](TEXT-FILE-TYPE.md)** using **[SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md)**.

```nasm
SYS03F0: ES:
SYS03F1: MOV WORD PTR [DI:Mode],fmClosed
```

Mark **[File](TEXT-FILE-TYPE.md)** as **[fmClosed](FILE-MODES.md)**.

```nasm
SYS03F6: POP BP
SYS03F7: RETF 0004
```

Return to caller and pop-off parameters.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md) or go [back](../README.md)