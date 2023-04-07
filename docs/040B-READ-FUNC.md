# 040B Read Function

This is Turbo Pascal's **Read**(**[File](TEXT-FILE-TYPE.md)**) function. It takes one parameter:
- **File**: Pointer to the **[File Record](TEXT-FILE-TYPE.md)**.

This reads bytes from **[File](TEXT-FILE-TYPE.md)**, if available.

```nasm
SYS040B: 8BDC          MOV	BX,SP
```

Use **BX** to address items on the stack.

## Stack after SYS:040B

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS040D: 1E            PUSH	DS
```

Save **DS** to the stack.

```nasm
SYS040E: 36            SS:
SYS040F: C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into ES:DI.

```nasm
SYS0412: 26            ES:
SYS0413: C5550C        LDS	DX,[DI:BufPtr]
```

Loads the pointer to buffer (**[BufPtr](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **DS**:**DX**.

```nasm
SYS0416: 26            ES:
SYS0417: 8B4D04        MOV	CX,[DI:BufSize]
```

Get size of the buffer (**[BufSize](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **CX**.

```nasm
SYS041A: 26            ES:
SYS041B: 8B1D          MOV	BX,[DI:Handle]
SYS041D: B43F          MOV	AH,3F
SYS041F: CD21          INT	21
```

Read from file or device using **DOS INT 21h AH = 3Fh** service with parameters:
- **BX** = Handle from **[File](TEXT-FILE-TYPE.md)**.
- **CX** = Number of bytes to read
- **DS**:**DX** = Pointer to buffer

```nasm
SYS0421: 7210          JB	0433
```

Exit on error.

```nasm
SYS0423: 26            ES:
SYS0424: 89450A        MOV	[DI:BufEnd],AX
```

Set the actual number of bytes read on  **[BufEnd](TEXT-FILE-TYPE.md)** in **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS0427: 33C0          XOR	AX,AX
SYS0429: 26            ES:
SYS042A: C745080000    MOV	WORD PTR [DI:BufPos],0000
```

Reset index (**[BufPos](TEXT-FILE-TYPE.md)**) of next character in buffer to 0 (beginning).

```nasm
SYS042F: 1F            POP	DS
```

Restore DS from the stack.

```nasm
SYS0430: CA0400        RETF	0004
```

Return to caller and pop-off parameters from the stack.

```nasm
SYS0433: 26            ES:
SYS0434: C7450A0000    MOV	WORD PTR [DI:BufEnd],0000
SYS0439: EBEE          JMP	0429
```

On error, set the number of characters read (**[BufEnd](TEXT-FILE-TYPE.md)**) to 0.

See also: [Text File Type](TEXT-FILE-TYPE.md) or go [back](../README.md)