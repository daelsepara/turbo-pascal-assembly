# 043B Write to file with additional error checking

This is Turbo Pascal's **Write**(**[File](TEXT-FILE-TYPE.md)**) function. It takes one parameter:
- **File**: Pointer to the [File Record](TEXT-FILE-TYPE.md). The bytes that will be written to **[File](TEXT-FILE-TYPE.md)** are contained within it's **[TexBuf](TEXT-FILE-TYPE.md)**. This is usually used when writes are made to a **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS043B: MOV BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS043D: PUSH DS
```

Save **DS**.

```nasm
SYS043E: SS:
SYS043F: LES DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS0442: ES:
SYS0443: LDS DX,[DI:BufPtr]
```

Loads the pointer to buffer (**[BufPtr](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **DS**:**DX**.

```nasm
SYS0446: XOR CX,CX
SYS0448: ES:
SYS0449: XCHG CX,[DI:BufPos]
```

Get number of bytes to write from the **[File](TEXT-FILE-TYPE.md)**'s **[BufPos](TEXT-FILE-TYPE.md)**.

```nasm
SYS044C: ES:
SYS044D: MOV BX,[DI:Handle]
```

Get this **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```nasm
SYS044F: MOV AH,40
SYS0451: INT 21
```

Write **CX** bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer

```nasm
SYS0453: JB 045C
```

On error, return with error code in **AX**.

```nasm
SYS0455: SUB AX,CX
SYS0457: JZ 045C
```

Verify if actual number of bytes written in **AX** (returned after call to **DOS INT 21h AH = 40h** service) is equal to what was expected (in **CX**).

```nasm
SYS0459: MOV AX,0065
```

If these do not match return with an code **[65h/101: Disk write error](ERROR-CODES.md)**.

```nasm
SYS045C: POP DS
```

Restore **DS**.

```nasm
SYS045D: RETF 0004
```

Return to caller with error code in **AX**, later to be stored in **[InOutRes](DATA.md)**. Pop-off **FAR PTR** parameter from the stack (4 bytes).

See also: [SYS:0460 Write Function](0460-WRITE-FUNC.md), or go [back](../README.md)