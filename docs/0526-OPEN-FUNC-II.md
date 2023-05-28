# 0526 Open Function (II)

This is part of the **[SYS:0499 Open Function](0499-OPEN-FUNC.md)** but the main purpose is to move the file pointer to the beginning of the filem if the file is new/empty/to be overwritten, or to the end of the file if it is to be appended.

```nasm
SYS0526: XOR DX,DX
SYS0528: XOR CX,CX
SYS052A: MOV BX,[DI:Handle]
SYS052C: MOV AX,4202
SYS052F: INT 21
```

Move file pointer to end of the file with a call to **DOS INT 21h AH = 42h** service with parameters:
- **AL** = method code
  - 00h offset from beginning of file
  - 01h offset from current position
  - 02h offset from end of file
- **BX** file handle
- **CX**:**DX** unsigned 32-bit offset (~ 4GB).

Upon return: **DX**:**AX** contains the new file pointer location, with **CF** clear on success, an error code in **AX** and **CF** is set. This purpose of this first call to **DOS INT 21h AH = 42h** is to get the file size.

```nasm
SYS0531: SUB AX,SizeOf(TextBuf)
SYS0534: SBB DX,+00
SYS0537: JNB 053D
```

Check if the file is at least 128 (80h) bytes in size then set **DX**:**AX** to 128 (80h) bytes from the end.

```nasm
SYS0539: XOR AX,AX
SYS053B: XOR DX,DX
```

If the file is less than 128 (80h) bytes, then set the file pointer **DX**:**AX** to the beginning of the file.

```nasm
SYS053D: MOV CX,DX
SYS053F: MOV DX,AX
```

Copy file pointer in **DX**:**AX** to **CX**:**DX**.

```nasm
SYS0541: MOV BX,[DI:Handle]
SYS0543: MOV AX,4200
SYS0546: INT 21
```

Move the file pointer to new location using **DOS INT 21h AH = 42h** service.

```nasm
SYS0548: LEA DX,[DI:TextBuf]
SYS054C: MOV CX,SizeOf(TextBuf)
SYS054F: MOV BX,[DI:Handle]
SYS0551: MOV AH,3F
SYS0553: INT 21
```

Read 128 (80h) bytes from the **[File](TEXT-FILE-TYPE.md)** and store it in **DS**:**DX** which points to its **[TextBuf](TEXT-FILE-TYPE.md)**. After the call, **AX** contains the number of bytes read and **CF** flag is clear on success and set on error.

```nasm
SYS0555: JNB 0559
```

Check for errors.

```nasm
SYS0557: XOR AX,AX
```

If there were errors, clear **AX**, i.e. 0 bytes read.

```nasm
SYS0559: XOR BX,BX
```

Set **BX** to the beginning of the buffer.

```nasm
SYS055B: CMP BX,AX
SYS055D: JZ 057F
```

Check all bytes up to (the count in) **AX** have been verified then exit.

```nasm
SYS055F: CMP BYTE PTR [BX+DI+TextBuf],1A
SYS0564: JZ 0569
```

Search for the **CTRL-Z**/26/1Ah byte, which is usually the last byte (or signature mark) of a text file.

```nasm
SYS0566: INC BX
SYS0567: JMP 055B
```

Loop back (to **SYS:055B**) until **BX** = **AX**.

```nasm
SYS0569: MOV DX,BX
SYS056B: SUB DX,AX
SYS056D: MOV CX,FFFF
```

Setup a signed 32-bit number as file pointer (or offset) in **CX**:**DX**.

```nasm
SYS0570: MOV BX,[DI:Handle]
```

Get file handle indicated by [DI].

```nasm
SYS0572: MOV AX,4202
SYS0575: INT 21
```

Move file pointer to offset **CX**:**DX** from end of the file using **INT 21h AH = 42h DOS Move File Pointer** service.

```nasm
SYS0577: XOR CX,CX
SYS0579: MOV BX,[DI:Handle]
SYS057B: MOV AH,40
SYS057D: INT 21
```

Write 0 bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer

```nasm
SYS057F: RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md) or go [back](../README.md)