# 0480 Close Function

Upon entry the stack looks like this:

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |Return Address (OFFSET)                   |
|SP+02|Return Address (SEGMENT)                  |
|SP+04|Pointer to File/Text Record Data (OFFSET) |
|SP+06|Pointer to File/Text Record Data (SEGMENT)|


```nasm
SYS0480: MOV BX,SP
```

Use **BX** to address items in the stack.

```nasm
SYS0482: SS:
SYS0483: LES DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS0486: ES:
SYS0487: MOV BX,[DI:Handle]
```

Loads the (**[Handle](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into **BX**.

```nasm
SYS0489: CMP BX,+04
SYS048C: JBE 0494
```

Check if **[Handle](TEXT-FILE-TYPE.md)** is a **[standard handle](DOS-STANDARD-HANDLES.md)** provided by **DOS** and return immediately if it is.

```nasm
SYS048E: MOV AH,3E
SYS0490: INT 21
```

Close file handle using **DOS INT 21h AH = 3Eh** service with parameter **BX** = File handle.

```nasm
SYS0492: JB 0496
```

Exit with a **[DOS Error code](ERROR-CODES.md)** in **AX** if an error occured.

```nasm
SYS0494: XOR AX,AX
```

**AX** = 0 (No errors).

```nasm
SYS0496: RETF 0004
```

Return and pop-off parameters from the stack. The **[error code](ERROR-CODES.md)** in **AX** is later stored in **[InOutRes](DATA.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md) or go [back](../README.md)