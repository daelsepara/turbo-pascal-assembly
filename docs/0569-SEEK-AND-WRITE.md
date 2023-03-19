# 0569 Seek and Write

Moves the file pointer of an opened file then write 0 bytes. This is used to initialize the file and it ready to receive data.

```
SYS:0569 8BD3          MOV	DX,BX
SYS:056B 2BD0          SUB	DX,AX
SYS:056D B9FFFF        MOV	CX,FFFF
```

Setup a signed 32-bit number as file pointer (or offset) in CX:DX.

```
SYS:0570 8B1D          MOV	BX,[DI]
```

Get file handle indicated by [DI]. It's likely that this is the handle for the [**Input** or **Output**](DATA.md) buffer.

```
SYS:0572 B80242        MOV	AX,4202
SYS:0575 CD21          INT	21
```

Seek from end of the file using **DOS Move File Pointer** service (INT 21h AH = 42h AL = 02h offset from end of file BX = Handle, CX:DX = Offset). Because **CX:DX** is a signed 32-bit number, this implies that DOS can only handle a file size limit of up to 2GB (~ **2147483648 bytes**).

```
SYS:0577 33C9          XOR	CX,CX
SYS:0579 8B1D          MOV	BX,[DI]
SYS:057B B440          MOV	AH,40
SYS:057D CD21          INT	21
```

Write 0 bytes using **DOS Write to a File or Device** service (INT 21h AH = 40h BX = Handle, CX = Number of bytes, DS:DX = Pointer to Buffer). 

```
SYS:057F C3            RET
```
Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Go back](../README.md)