# 0480 Close Function

Upon entry the stack looks like this:

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |Return Address (OFFSET)                   |
|SP+02|Return Address (SEGMENT)                  |
|SP+04|Pointer to File/Text Record Data (OFFSET) |
|SP+06|Pointer to File/Text Record Data (SEGMENT)|


```nasm
SYS:0480 8BDC          MOV	BX,SP
```

Use **BX** to address items in the stack.

```nasm
SYS:0482 36            SS:
SYS:0483 C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS:0486 26            ES:
SYS:0487 8B1D          MOV	BX,[DI:Handle]
```

Loads the (**[Handle](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **BX**.

```nasm
SYS:0489 83FB04        CMP	BX,+04
SYS:048C 7606          JBE	0494
```

Check if **[Handle](TEXT-FILE-TYPE.md)** is a **[standard handle](DOS-STANDARD-HANDLES.md)** provided by **DOS** and return immediately if it is.

```nasm
SYS:048E B43E          MOV	AH,3E
SYS:0490 CD21          INT	21
```

Close file handle using **DOS INT 21h AH = 3Eh** service with parameter **BX** = File handle.

```nasm
SYS:0492 7202          JB	0496
```

Exit with a **[DOS Error code](ERROR-CODES.md)** in **AX** if an error occured.

```nasm
SYS:0494 33C0          XOR	AX,AX
```

**AX** = 0 (No errors).

```nasm
SYS:0496 CA0400        RETF	0004
```

Return and pop-off parameters from the stack. The **[error code](ERROR-CODES.md)** in **AX** is later stored in **[InOutRes](DATA.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md) or go [back](../README.md)