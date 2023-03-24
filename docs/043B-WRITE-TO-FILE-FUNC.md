# 043B Write to file with additional error checking

This is Turbo Pascal's **Write**(**[File](TEXT-FILE-TYPE.md)**) function. It takes one parameter:
- **File**: Pointer to the [File Record](TEXT-FILE-TYPE.md). The bytes that will be written to **[File](TEXT-FILE-TYPE.md)** are contained within it's **[TexBuf](TEXT-FILE-TYPE.md)**. This is usually used when writes are made to a **[File](TEXT-FILE-TYPE.md)**.

```
SYS:043B 8BDC          MOV	BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```
SYS:043D 1E            PUSH	DS
```

Save **DS**.

```
SYS:043E 36            SS:
SYS:043F C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```
SYS:0442 26            ES:
SYS:0443 C5550C        LDS	DX,[DI+0C]
```

Loads the pointer to buffer (**[BufPtr](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **DS**:**DX**.

```
SYS:0446 33C9          XOR	CX,CX
SYS:0448 26            ES:
SYS:0449 874D08        XCHG	CX,[DI+08]
```

Get number of bytes to write from the **[File](TEXT-FILE-TYPE.md)**'s **[BufPos](TEXT-FILE-TYPE.md)**.

```
SYS:044C 26            ES:
SYS:044D 8B1D          MOV	BX,[DI]
```

Get this **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```
SYS:044F B440          MOV	AH,40
SYS:0451 CD21          INT	21
```

Write **CX** bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer 

```
SYS:0453 7207          JB	045C
```

On error, return with error code in **AX**.

```
SYS:0455 2BC1          SUB	AX,CX
SYS:0457 7403          JZ	045C
```

Verify if actual number of bytes written in **AX** (returned after call to **DOS INT 21h AH = 40h** service) is equal to what was expected (in **CX**).

```
SYS:0459 B86500        MOV	AX,0065
```

If these do not match return with an code [65/101: Disk write error](ERROR-CODES.md).

```
SYS:045C 1F            POP	DS
```

```
SYS:045D CA0400        RETF	0004
```

Return to caller with error code in **AX**, later to be stored in **[InOutRes](DATA.md)**. Pop-off **FAR PTR** parameter from the stack (4 bytes).

See also: [SYS:0460 Write Function](0460-WRITE-FUNC.md), or go [back](../README.md)