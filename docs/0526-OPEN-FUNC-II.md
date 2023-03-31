# 0526 Open Function (II)

This is part of the **[SYS:0499 Open Function](0499-OPEN-FUNC.md)** but the main purpose is to move the file pointer to the beginning of the filem if the file is new/empty/to be overwritten, or to the end of the file if it is to be appended.

```
SYS:0526 33D2          XOR	DX,DX
SYS:0528 33C9          XOR	CX,CX
SYS:052A 8B1D          MOV	BX,[DI:Handle]
SYS:052C B80242        MOV	AX,4202
SYS:052F CD21          INT	21
```

Move file pointer to end of the file with a call to **DOS INT 21h AH = 42h** service with parameters:
- **AL** = method code
  - 00h offset from beginning of file
  - 01h offset from current position
  - 02h offset from end of file
- **BX** file handle
- **CX**:**DX** unsigned 32-bit offset (~ 4GB).

Upon return: **DX**:**AX** contains the new file pointer location, with **CF** clear on success, an error code in **AX** and **CF** is set. This purpose of this first call to **DOS INT 21h AH = 42h** is to get the file size.

```
SYS:0531 2D8000        SUB	AX,SizeOf(TextBuf)
SYS:0534 83DA00        SBB	DX,+00
SYS:0537 7304          JNB	053D
```

Check if the file is at least 128 (80h) bytes in size then set **DX**:**AX** to 128 (80h) bytes from the end.

```
SYS:0539 33C0          XOR	AX,AX
SYS:053B 33D2          XOR	DX,DX
```

If the file is less than 128 (80h) bytes, then set the file pointer **DX**:**AX** to the beginning of the file.

```
SYS:053D 8BCA          MOV	CX,DX
SYS:053F 8BD0          MOV	DX,AX
```

Copy file pointer in **DX**:**AX** to **CX**:**DX**.

```
SYS:0541 8B1D          MOV	BX,[DI:Handle]
SYS:0543 B80042        MOV	AX,4200
SYS:0546 CD21          INT	21
```

Move the file pointer to new location using **DOS INT 21h AH = 42h** service.

```
SYS:0548 8D958000      LEA	DX,[DI:TextBuf]
SYS:054C B98000        MOV	CX,SizeOf(TextBuf)
SYS:054F 8B1D          MOV	BX,[DI:Handle]
SYS:0551 B43F          MOV	AH,3F
SYS:0553 CD21          INT	21
```

Read 128 (80h) bytes from the **[File](TEXT-FILE-TYPE.md)** and store it in **DS**:**DX** which points to its **[TextBuf](TEXT-FILE-TYPE.md)**. After the call, **AX** contains the number of bytes read and **CF** flag is clear on success and set on error.

```
SYS:0555 7302          JNB	0559
```

Check for errors.

```
SYS:0557 33C0          XOR	AX,AX
```

If there were errors, clear **AX**, i.e. 0 bytes read.

```
SYS:0559 33DB          XOR	BX,BX
```

Set **BX** to the beginning of the buffer.

```
SYS:055B 3BD8          CMP	BX,AX
SYS:055D 7420          JZ	057F
```

Check all bytes up to (the count in) **AX** have been verified then exit.

```
SYS:055F 80B980001A    CMP	BYTE PTR [BX+DI+TextBuf],1A
SYS:0564 7403          JZ	0569
```

Search for the **CTRL-Z**/26/1Ah byte, which is usually the last byte (or signature mark) of a text file.

```
SYS:0566 43            INC	BX
SYS:0567 EBF2          JMP	055B
```

Loop back (to **SYS:055B**) until **BX** = **AX**.

```
SYS:0569 8BD3          MOV	DX,BX
SYS:056B 2BD0          SUB	DX,AX
SYS:056D B9FFFF        MOV	CX,FFFF
```

Setup a signed 32-bit number as file pointer (or offset) in **CX**:**DX**.

```
SYS:0570 8B1D          MOV	BX,[DI:Handle]
```

Get file handle indicated by [DI].

```
SYS:0572 B80242        MOV	AX,4202
SYS:0575 CD21          INT	21
```

Move file pointer to offset **CX**:**DX** from end of the file using **DOS Move File Pointer** service.

```
SYS:0577 33C9          XOR	CX,CX
SYS:0579 8B1D          MOV	BX,[DI:Handle]
SYS:057B B440          MOV	AH,40
SYS:057D CD21          INT	21
```

Write 0 bytes to File/Device using **DOS Write to a File or Device INT 21h AH = 40h** with parameters:
- **BX** = Handle
- **CX** = Number of bytes to write
- **DS**:**DX** = Pointer to Buffer 

```
SYS:057F C3            RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md) or go [back](../README.md)