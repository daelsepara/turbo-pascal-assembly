# 0364 Reset/Rewrite Function

Subroutine for Turbo Pascal's:

- Reset(**[File](TEXT-FILE-TYPE.md)**): Opens an existing file
- Rewrite(**[File](TEXT-FILE-TYPE.md)**): Creates and opens a new file

**[File](TEXT-FILE-TYPE.md)** must already be associated with a filename using the **[Assign](02E6-ASSIGN-FUNC.md)**(**[File](TEXT-FILE-TYPE.md)**, Filename) prior.

```
SYS:0364 BAB1D7        MOV	DX,fmInput
SYS:0367 EB08          JMP	0371
```

Entry point for Reset(**[File](TEXT-FILE-TYPE.md)**).

```
SYS:0369 BAB2D7        MOV	DX,fmOutput
SYS:036C EB03          JMP	0371
```

Entry point for Rewrite(**[File](TEXT-FILE-TYPE.md)**).

```
SYS:036E BAB3D7        MOV	DX,fmInOut
```

Entry point for typed and untyped files.

```
SYS:0371 55            PUSH	BP
SYS:0372 8BEC          MOV	BP,SP
```

Upon entering this subroutine from any of the entrypoints: **[fmInput](FILE-MODES.md)**, **[fmInput](FILE-MODES.md)**, **[fmInput](FILE-MODES.md)**, and saving **BP**, the stack looks like this:

|Index|Contents                                  |
|-----|------------------------------------------|
|BP   |Old **BP** value                          |
|BP+02|Return Address (OFFSET)                   |
|SP+04|Return Address (SEGMENT)                  |
|BP+06|Pointer to File/Text Record Data (OFFSET) |
|BP+08|Pointer to File/Text Record Data (SEGMENT)|

```
SYS:0374 C47E06        LES	DI,[BP+06]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into ES:DI.

```
SYS:0377 26            ES:
SYS:0378 8B4502        MOV	AX,[DI+02]
```

Loads **[Mode](FILE-MODES.md)** int AX.

```
SYS:037B 3DB1D7        CMP	AX,fmInput
SYS:037E 7412          JZ	0392
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Reset()](FILE-MODES.md)**.

```
SYS:0380 3DB2D7        CMP	AX,fmOutput
SYS:0383 740D          JZ	0392
```

Checks if **[File](TEXT-FILE-TYPE.md)** was opened using **[Rewrite()](FILE-MODES.md)**.

```
SYS:0385 3DB0D7        CMP	AX,fmClosed
SYS:0388 7410          JZ	039A
```

Check if **[File](TEXT-FILE-TYPE.md)** was closed.

```
SYS:038A C7063C006600  MOV	WORD PTR [InOutRes],0066
SYS:0390 EB24          JMP	03B6
```

Exit with an error **[66/102: File not assigned](ERROR-CODES.md)**.

```
SYS:0392 52            PUSH	DX
```

Save **[Mode](FILE-MODES.md)** in DX on to the stack.

```
SYS:0393 06            PUSH	ES
SYS:0394 57            PUSH	DI
SYS:0395 0E            PUSH	CS
SYS:0396 E82500        CALL	03BE
```

Call **[SYS:03BE Reset()/Rewrite()](03BA-RESET-REWRITE-FUNC-II.md)** and pushing the pointer to **[File](TEXT-FILE-TYPE.md)** onto the stack.


```
SYS:0399 5A            POP	DX
```

Recover **[Mode](FILE-MODES.md)** from the stack to in DX.

```
SYS:039A 33C0          XOR	AX,AX
```

Clears AX.

```
SYS:039C 26            ES:
SYS:039D 895502        MOV	[DI+02],DX
```

Set **[Mode](FILE-MODES.md)**  in **[File](TEXT-FILE-TYPE.md)**.

```
SYS:03A0 26            ES:
SYS:03A1 894508        MOV	[DI+08],AX
```

Resets buffer to the beginning.

```
SYS:03A4 26            ES:
SYS:03A5 89450A        MOV	[DI+0A],AX
```

Resets the number of characters in the buffer to 0 (Empty).

```
SYS:03A8 BB1000        MOV	BX,0010
SYS:03AB E84C00        CALL	03FA
```

Open **[File](TEXT-FILE-TYPE.md)** with a call to its **[BX = 0010 OpenFunc()](TEXT-FILE-TYPE.md)** using **[SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md)**.

```
SYS:03AE 7406          JZ	03B6
```

Return on succes.

```
SYS:03B0 26            ES:
SYS:03B1 C74502B0D7    MOV	WORD PTR [DI+02],fmClosed
```

Mark the file as  **[fmClosed](FILE-MODES.md)** on error.

```
SYS:03B6 5D            POP	BP
SYS:03B7 CA0400        RETF	0004
```

Return and pop-off parameters from the stack.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:03BA Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md), [SYS:03FA I/O Function Dispatcher](03FA-IO-FUNCTION-DISPATCHER.md), or go [back](../README.md)