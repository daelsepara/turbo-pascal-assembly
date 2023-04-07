# 0526 Open Function (II)

This is part of the **[SYS:0499 Open Function](0499-OPEN-FUNC.md)** but the main purpose is to move the file pointer to the beginning of the filem if the file is new/empty/to be overwritten, or to the end of the file if it is to be appended.

```nasm
SYS0526: 33D2          XOR	DX,DX
SYS0528: 33C9          XOR	CX,CX
SYS052A: 8B1D          MOV	BX,[DI:Handle]
SYS052C: B80242        MOV	AX,4202
SYS052F: CD21          INT	21
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
SYS0531: 2D8000        SUB	AX,SizeOf(TextBuf)
SYS0534: 83DA00        SBB	DX,+00
SYS0537: 7304          JNB	053D
```

Check if the file is at least 128 (80h) bytes in size then set **DX**:**AX** to 128 (80h) bytes from the end.

```nasm
SYS0539: 33C0          XOR	AX,AX
SYS053B: 33D2          XOR	DX,DX
```

If the file is less than 128 (80h) bytes, then set the file pointer **DX**:**AX** to the beginning of the file.

```nasm
SYS053D: 8BCA          MOV	CX,DX
SYS053F: 8BD0          MOV	DX,AX
```

Copy file pointer in **DX**:**AX** to **CX**:**DX**.

```nasm
SYS0541: 8B1D          MOV	BX,[DI:Handle]
SYS0543: B80042        MOV	AX,4200
SYS0546: CD21          INT	21
```

Move the file pointer to new location using **DOS INT 21h AH = 42h** service.

```nasm
SYS0548: 8D958000      LEA	DX,[DI:TextBuf]
SYS054C: B98000        MOV	CX,SizeOf(TextBuf)
SYS054F: 8B1D          MOV	BX,[DI:Handle]
SYS0551: B43F          MOV	AH,3F
SYS0553: CD21          INT	21
```

Read 128 (80h) bytes from the **[File](TEXT-FILE-TYPE.md)** and store it in **DS**:**DX** which points to its **[TextBuf](TEXT-FILE-TYPE.md)**. After the call, **AX** contains the number of bytes read and **CF** flag is clear on success and set on error.

```nasm
SYS0555: 7302          JNB	0559
```

Check for errors.

```nasm
SYS0557: 33C0          XOR	AX,AX
```

If there were errors, clear **AX**, i.e. 0 bytes read.

```nasm
SYS0559: 33DB          XOR	BX,BX
```

Set **BX** to the beginning of the buffer.

```nasm
SYS055B: 3BD8          CMP	BX,AX
SYS055D: 7420          JZ	057F
```

Check all bytes up to (the count in) **AX** have been verified then exit.

```nasm
SYS055F: 80B980001A    CMP	BYTE PTR [BX+DI+TextBuf],1A
SYS0564: 7403          JZ	0569
```

Search for the **CTRL-Z**/26/1Ah byte, which is usually the last byte (or signature mark) of a text file.

```nasm
SYS0566: 43            INC	BX
SYS0567: EBF2          JMP	055B
```

Loop back (to **SYS:055B**) until **BX** = **AX**.

```nasm
SYS0569: 8BD3          MOV	DX,BX
SYS056B: 2BD0          SUB	DX,AX
SYS056D: B9FFFF        MOV	CX,FFFF
```

Setup a signed 32-bit number as file pointer (or offset) in **CX**:**DX**.

```nasm
SYS0570: 8B1D          MOV	BX,[DI:Handle]
```

Get file handle indicated by [DI].

```nasm
SYS0572: B80242        MOV	AX,4202
SYS0575: CD21          INT	21
```

Move file pointer to offset **CX**:**DX** from end of the file using **INT 21h AH = 42h DOS Move File Pointer** service.

```nasm
SYS0577: 33C9          XOR	CX,CX
SYS0579: 8B1D          MOV	BX,[DI:Handle]
SYS057B: B440          MOV	AH,40
SYS057D: CD21          INT	21
```

Write 0 bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer 

```nasm
SYS057F: C3            RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md) or go [back](../README.md)