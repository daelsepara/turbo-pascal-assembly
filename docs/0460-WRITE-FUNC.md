# 0460 Write Function

This is Turbo Pascal's **Write**(**[File](TEXT-FILE-TYPE.md)**) function. It takes one parameter:
- **File**: Pointer to the [File Record](TEXT-FILE-TYPE.md). The bytes that will be written to **[File](TEXT-FILE-TYPE.md)** are contained within it's **[TexBuf](TEXT-FILE-TYPE.md)**. This is usually used when writes are made to the standard **[Output](DATA.md)** file.

```nasm
SYS0460: 8BDC          MOV	BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS0462: 1E            PUSH	DS
```

Save **DS**.

```nasm
SYS0463: 36            SS:
SYS0464: C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS0467: 26            ES:
SYS0468: C5550C        LDS	DX,[DI:BufPtr]
```

Loads the pointer to buffer (**[BufPtr](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **DS**:**DX**.

```nasm
SYS046B: 33C9          XOR	CX,CX
SYS046D: 26            ES:
SYS046E: 874D08        XCHG	CX,[DI:BufPos]
```

Get number of bytes to write from the **[File](TEXT-FILE-TYPE.md)**'s **[BufPos](TEXT-FILE-TYPE.md)**.

```nasm
SYS0471: 26            ES:
SYS0472: 8B1D          MOV	BX,[DI:Handle]
```

Get this **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```nasm
SYS0474: B440          MOV	AH,40
SYS0476: CD21          INT	21
```

Write **CX** bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer 

```nasm
SYS0478: 7202          JB	047C
```

On error, return with error code in **AX**.

```nasm
SYS047A: 33C0          XOR	AX,AX
```

**AX** = 0 (No errors).

```nasm
SYS047C: 1F            POP	DS
```

Restore **DS**.

```nasm
SYS047D: CA0400        RETF	0004
```

Return to caller with error code in **AX**, later to be stored in **[InOutRes](DATA.md)**. Pop-off **FAR PTR** parameter from the stack (4 bytes).

See also: [Text File Type](TEXT-FILE-TYPE.md) or go [back](../README.md)