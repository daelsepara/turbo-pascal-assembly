# 043B Write to file with additional error checking

This is Turbo Pascal's **Write**(**[File](TEXT-FILE-TYPE.md)**) function. It takes one parameter:
- **File**: Pointer to the [File Record](TEXT-FILE-TYPE.md). The bytes that will be written to **[File](TEXT-FILE-TYPE.md)** are contained within it's **[TexBuf](TEXT-FILE-TYPE.md)**. This is usually used when writes are made to a **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS043B: 8BDC          MOV	BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS043D: 1E            PUSH	DS
```

Save **DS**.

```nasm
SYS043E: 36            SS:
SYS043F: C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS0442: 26            ES:
SYS0443: C5550C        LDS	DX,[DI:BufPtr]
```

Loads the pointer to buffer (**[BufPtr](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **DS**:**DX**.

```nasm
SYS0446: 33C9          XOR	CX,CX
SYS0448: 26            ES:
SYS0449: 874D08        XCHG	CX,[DI:BufPos]
```

Get number of bytes to write from the **[File](TEXT-FILE-TYPE.md)**'s **[BufPos](TEXT-FILE-TYPE.md)**.

```nasm
SYS044C: 26            ES:
SYS044D: 8B1D          MOV	BX,[DI:Handle]
```

Get this **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```nasm
SYS044F: B440          MOV	AH,40
SYS0451: CD21          INT	21
```

Write **CX** bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer 

```nasm
SYS0453: 7207          JB	045C
```

On error, return with error code in **AX**.

```nasm
SYS0455: 2BC1          SUB	AX,CX
SYS0457: 7403          JZ	045C
```

Verify if actual number of bytes written in **AX** (returned after call to **DOS INT 21h AH = 40h** service) is equal to what was expected (in **CX**).

```nasm
SYS0459: B86500        MOV	AX,0065
```

If these do not match return with an code **[65h/101: Disk write error](ERROR-CODES.md)**.

```nasm
SYS045C: 1F            POP	DS
```

Restore **DS**.

```nasm
SYS045D: CA0400        RETF	0004
```

Return to caller with error code in **AX**, later to be stored in **[InOutRes](DATA.md)**. Pop-off **FAR PTR** parameter from the stack (4 bytes).

See also: [SYS:0460 Write Function](0460-WRITE-FUNC.md), or go [back](../README.md)