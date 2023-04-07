# 0364 Reset/Rewrite Function

Subroutine for Turbo Pascal's:

- Reset(**[File](TEXT-FILE-TYPE.md)**): Opens an existing file
- Rewrite(**[File](TEXT-FILE-TYPE.md)**): Creates and opens a new file

**[File](TEXT-FILE-TYPE.md)** must already be associated with a filename using the **[Assign](02E6-ASSIGN-FUNC.md)**(**[File](TEXT-FILE-TYPE.md)**, Filename) prior.

```nasm
SYS0364: MOV DX,fmInput
SYS0367: JMP 0371
```

Entry point for Reset(**[File](TEXT-FILE-TYPE.md)**).

```nasm
SYS0369: MOV DX,fmOutput
SYS036C: JMP 0371
```

Entry point for Rewrite(**[File](TEXT-FILE-TYPE.md)**).

```nasm
SYS036E: MOV DX,fmInOut
```

Entry point for typed and untyped files.

```nasm
SYS0371: PUSH BP
SYS0372: MOV BP,SP
```

Upon entering this subroutine from any of the entrypoints: **[fmInput](FILE-MODES.md)**, **[fmInput](FILE-MODES.md)**, **[fmInput](FILE-MODES.md)**, and saving **BP**, the stack looks like this:

|Index|Contents                                  |
|-----|------------------------------------------|
|BP   |Old **BP** value                          |
|BP+02|Return Address (OFFSET)                   |
|SP+04|Return Address (SEGMENT)                  |
|BP+06|Pointer to File/Text Record Data (OFFSET) |
|BP+08|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS0374: LES DI,[BP+06]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into ES:DI.

```nasm
SYS0377: ES:
SYS0378: MOV AX,[DI:Mode]
```

Loads **[Mode](FILE-MODES.md)** int AX.

```nasm
SYS037B: CMP AX,fmInput
SYS037E: JZ 0392
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Reset()](FILE-MODES.md)**.

```nasm
SYS0380: CMP AX,fmOutput
SYS0383: JZ 0392
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Rewrite()](FILE-MODES.md)**.

```nasm
SYS0385: CMP AX,fmClosed
SYS0388: JZ 039A
```

Check if **[File](TEXT-FILE-TYPE.md)** was closed.

```nasm
SYS038A: MOV WORD PTR [InOutRes],0066
SYS0390: JMP 03B6
```

Exit with an error **[66h/102: File not assigned](ERROR-CODES.md)**.

```nasm
SYS0392: PUSH DX
```

Save **[Mode](FILE-MODES.md)** in DX on to the stack.

```nasm
SYS0393: PUSH ES
SYS0394: PUSH DI
SYS0395: PUSH CS
SYS0396: CALL 03BE
```

Call **[SYS:03BE Reset()/Rewrite()](03BA-RESET-REWRITE-FUNC-II.md)** and pushing the pointer to **[File](TEXT-FILE-TYPE.md)** onto the stack.


```nasm
SYS0399: POP DX
```

Recover **[Mode](FILE-MODES.md)** from the stack to in DX.

```nasm
SYS039A: XOR AX,AX
```

Clears AX.

```nasm
SYS039C: ES:
SYS039D: MOV [DI:Mode],DX
```

Set **[Mode](FILE-MODES.md)**  in **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS03A0: ES:
SYS03A1: MOV [DI+08],AX
```

Resets buffer to the beginning.

```nasm
SYS03A4: ES:
SYS03A5: MOV [DI+0A],AX
```

Resets the number of characters in the buffer to 0 (Empty).

```nasm
SYS03A8: MOV BX,0010
SYS03AB: CALL 03FA
```

Open **[File](TEXT-FILE-TYPE.md)** with a call to its **[BX = 0010 OpenFunc()](TEXT-FILE-TYPE.md)** using **[SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md)**.

```nasm
SYS03AE: JZ 03B6
```

Return on succes.

```nasm
SYS03B0: ES:
SYS03B1: MOV WORD PTR [DI:Mode],fmClosed
```

Mark the file as  **[fmClosed](FILE-MODES.md)** on error.

```nasm
SYS03B6: POP BP
SYS03B7: RETF 0004
```

Return and pop-off parameters from the stack.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:03BA Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md), [SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md), or go [back](../README.md)