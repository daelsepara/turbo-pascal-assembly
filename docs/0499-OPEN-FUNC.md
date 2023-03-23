# 0499 OpenFunc

This is the Open([**File**](TEXT-FILE-TYPE.md)) function. It assumes that **[File](TEXT-FILE-TYPE.md)** is already be bound to a filename using the **[Assign](02E6-ASSIGN-FUNC.md)**(**[File](TEXT-FILE-TYPE.md)**, Filename) function.


```
SYS:0499 8BDC          MOV	BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BP   |Return Address (OFFSET)                   |
|BP+02|Return Address (SEGMENT)                  |
|BP+04|Pointer to File/Text Record Data (OFFSET) |
|BP+06|Pointer to File/Text Record Data (SEGMENT)|

```
SYS:049B 1E            PUSH	DS
```

Save **DS** as it will be modified later.

```
SYS:049C 36            SS:
SYS:049D C57F04        LDS	DI,[BX+04]
```

Load pointer to **[File](TEXT-FILE-TYPE.md)** into **DS**:**DI**.

```
SYS:04A0 33C9          XOR	CX,CX
SYS:04A2 890D          MOV	[DI],CX
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)** to 0 (**[STDIN](DOS-STANDARD-HANDLES.md)**).

```
SYS:04A4 B8003D        MOV	AX,3D00
```

Prepare to open **[File](TEXT-FILE-TYPE.md)** using **DOS INT 21h AH = 3Dh** service with parameters:
- **DS**:**DX** = Pointer to buffer containing NULL (00h)-terminated filename sting.
- **AL** = access mode:
  - 00h read-only
  - 01h write-only
  - 02h read/write

```
SYS:04A7 817D02B1D7    CMP	WORD PTR [DI+02],fmInput
SYS:04AC 740D          JZ	04BB
```

Check if **[File (Mode](TEXT-FILE-TYPE.md)** is to be opened using **[Reset()](FILE-MODES.md)**.

```
SYS:04AE B002          MOV	AL,02
SYS:04B0 FF05          INC	WORD PTR [DI]
```

Otherwise, set access mode to read/write and set **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)** to 1 (**[STDOUT](DOS-STANDARD-HANDLES.md)**) by adding to the **[Handle](TEXT-FILE-TYPE.md)** at [**DI**].

```
SYS:04B2 817D02B3D7    CMP	WORD PTR [DI+02],fmInOut
SYS:04B7 7402          JZ	04BB
```

Check if **[File (Mode)](TEXT-FILE-TYPE.md)** is to be opened using **[Reset() and Rewrite()](FILE-MODES.md)**.

```
SYS:04B9 B43C          MOV	AH,3C
```

Prepare to create the file using **DOS INT 21h AH = 3Ch** service with parameters:
- **DS**:**DX** = Pointer to buffer containing NULL (00h)-terminated filename sting.
- **CX** = File attribute

```
SYS:04BB 807D3000      CMP	BYTE PTR [DI+30],00
SYS:04BF 7409          JZ	04CA
```

If the filename is empty, there is no need to call the **DOS** service set up in **AH**. The empty string indicates that this is either **[Input](DATA.md)** or **[Output](DATA.md)**.

```
SYS:04C1 8D5530        LEA	DX,[DI+30]
SYS:04C4 CD21          INT	21
```

Load the pointer to the **[File](TEXT-FILE-TYPE.md)**'s **[Name](TEXT-FILE-TYPE.md)** into **DS**:**DX**. **DS** was already set in **SYS:04A2** above.

```
SYS:04C6 725A          JB	0522
```

On error, exit immediately with an **[error code](ERROR-CODES.md)**. 

```
SYS:04C8 8905          MOV	[DI],AX
```

Upon success of the call to the **DOS** services, AX contains the handle of the openend / created file. Copy AX in **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```
SYS:04CA B80B04        MOV	AX,040B
SYS:04CD BA7007        MOV	DX,SYS
SYS:04D0 33C9          XOR	CX,CX
SYS:04D2 33DB          XOR	BX,BX
SYS:04D4 817D02B1D7    CMP	WORD PTR [DI+02],fmInput
SYS:04D9 742F          JZ	050A
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** to [SYS:040B Read Function](040B-READ-FUNC.md) if **[File](TEXT-FILE-TYPE.md)** was opened with **[Reset()](FILE-MODES.md)**.

```
SYS:04DB 8B1D          MOV	BX,[DI]
SYS:04DD B80044        MOV	AX,4400
SYS:04E0 CD21          INT	21
```

Get device information using **DOS INT 21h AX = 4200h** service with parameter **BX** = File handle.

```
SYS:04E2 F6C280        TEST	DL,80
SYS:04E5 B86004        MOV	AX,0460
SYS:04E8 BA7007        MOV	DX,SYS
SYS:04EB 8BC8          MOV	CX,AX
SYS:04ED 8BDA          MOV	BX,DX
SYS:04EF 7514          JNZ	0505
```

If file is a character device (80h), set **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc and FlushFunc](TEXT-FILE-TYPE.md)** to [SYS:0460 Write Function](0460-WRITE-FUNC.md).


```
SYS:04F1 817D02B3D7    CMP	WORD PTR [DI+02],fmInOut
SYS:04F6 7503          JNZ	04FB
SYS:04F8 E82B00        CALL	0526
```

Check if **[File (Mode)](TEXT-FILE-TYPE.md)** was opened using **[Reset() and Rewrite()](FILE-MODES.md)** then get file size and move file pointer.

```
SYS:04FB B83B04        MOV	AX,043B
SYS:04FE BA7007        MOV	DX,SYS
SYS:0501 33C9          XOR	CX,CX
SYS:0503 33DB          XOR	BX,BX
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** to [SYS:43B Write to File Function](043B-WRITE-TO-FILE-FUNC.md).

```
SYS:0505 C74502B2D7    MOV	WORD PTR [DI+02],fmOutput
```

Mark the **[File](TEXT-FILE-TYPE.md)** as having been opened using **[Rewrite()](FILE-MODES.md)**.


```
SYS:050A 894514        MOV	[DI+14],AX
SYS:050D 895516        MOV	[DI+16],DX
SYS:0510 894D18        MOV	[DI+18],CX
SYS:0513 895D1A        MOV	[DI+1A],BX
```

Set up the handlers for **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** and **[FlushFunc](TEXT-FILE-TYPE.md)**.

```
SYS:0516 C7451C8004    MOV	WORD PTR [DI+1C],0480
SYS:051B C7451E7007    MOV	WORD PTR [DI+1E],SYS
```

Set up the handlers for **[File](TEXT-FILE-TYPE.md)**'s **[CloseFunc](TEXT-FILE-TYPE.md)**.

```
SYS:0520 33C0          XOR	AX,AX
```

Set exit code to 0 on success

```
SYS:0522 1F            POP	DS
```

Restores DS.

```
SYS:0523 CA0400        RETF	0004
```

Return and pop-off parameter from the stack.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:040B Read Function](040B-READ-FUNC.md), [SYS:043B Write to File Function](043B-WRITE-TO-FILE-FUNC.md), [SYS:0460 Write Function](0460-WRITE-FUNC.md), [SYS:0480 CloseFunc](0480-CLOSE-FUNC.md), [SYS:0526](0526-UNKNOWN.md) or go [back](../README.md)