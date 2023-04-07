# 0499 Open Function

This is the Open([**File**](TEXT-FILE-TYPE.md)) function. It assumes that **[File](TEXT-FILE-TYPE.md)** is already be bound to a filename using the **[Assign](02E6-ASSIGN-FUNC.md)**(**[File](TEXT-FILE-TYPE.md)**, Filename) function.


```nasm
SYS0499: MOV BX,SP
```

Use **BX** to address items on the stack. Upon entry into this subroutine, the stack looks as follows:

|Index|Contents                                  |
|-----|------------------------------------------|
|BX   |Return Address (OFFSET)                   |
|BX+02|Return Address (SEGMENT)                  |
|BX+04|Pointer to File/Text Record Data (OFFSET) |
|BX+06|Pointer to File/Text Record Data (SEGMENT)|

```nasm
SYS049B: PUSH DS
```

Save **DS** as it will be modified later.

```nasm
SYS049C: SS:
SYS049D: LDS DI,[BX+04]
```

Load pointer to **[File](TEXT-FILE-TYPE.md)** into **DS**:**DI**.

```nasm
SYS04A0: XOR CX,CX
SYS04A2: MOV [DI:Handle],CX
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)** to 0 (**[STDIN](DOS-STANDARD-HANDLES.md)**).

```nasm
SYS04A4: MOV AX,3D00
```

Prepare to open **[File](TEXT-FILE-TYPE.md)** using **DOS INT 21h AH = 3Dh** service with parameters:
- **DS**:**DX** = Pointer to buffer containing NULL (00h)-terminated filename sting.
- **AL** = access mode:
  - 00h read-only
  - 01h write-only
  - 02h read/write

```nasm
SYS04A7: CMP WORD PTR [DI:Mode],fmInput
SYS04AC: JZ 04BB
```

Check if **[File (Mode](TEXT-FILE-TYPE.md)** is to be opened using **[Reset()](FILE-MODES.md)**.

```nasm
SYS04AE: MOV AL,02
SYS04B0: INC WORD PTR [DI:Handle]
```

Otherwise, set access mode to read/write and set **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)** to 1 (**[STDOUT](DOS-STANDARD-HANDLES.md)**) by adding 1 [**DI**].

```nasm
SYS04B2: CMP WORD PTR [DI:Mode],fmInOut
SYS04B7: JZ 04BB
```

Check if **[File (Mode)](TEXT-FILE-TYPE.md)** is to be opened using **[Reset() and Rewrite()](FILE-MODES.md)**.

```nasm
SYS04B9: MOV AH,3C
```

Prepare to create the file using **DOS INT 21h AH = 3Ch** service with parameters:
- **DS**:**DX** = Pointer to buffer containing NULL (00h)-terminated filename sting.
- **CX** = File attribute

```nasm
SYS04BB: CMP BYTE PTR [DI:Name],00
SYS04BF: JZ 04CA
```

If the filename is empty, there is no need to call the **DOS** service indicated in **AH**. The empty string indicates that this is either **[Input](DATA.md)** or **[Output](DATA.md)**.

```nasm
SYS04C1: LEA DX,[DI:Name]
SYS04C4: INT 21
```

Load the pointer to the **[File](TEXT-FILE-TYPE.md)**'s **[Name](TEXT-FILE-TYPE.md)** into **DS**:**DX**. **DS** was already set in **SYS:04A2** above. Call on the **DOS** service indicated in **AH**.

```nasm
SYS04C6: JB 0522
```

On error, return immediately with a non-zero **[error code](ERROR-CODES.md)** in AX (to be stored later in **[InOutRes](DATA.md)**).

```nasm
SYS04C8: MOV [DI:Handle],AX
```

Upon success of the call to the **DOS** service, **AX** contains the handle of the openend / created file. Copy **AX** in **[File](TEXT-FILE-TYPE.md)**'s **[Handle](TEXT-FILE-TYPE.md)**.

```nasm
SYS04CA: MOV AX,040B
SYS04CD: MOV DX,SYS
SYS04D0: XOR CX,CX
SYS04D2: XOR BX,BX
SYS04D4: CMP WORD PTR [DI:Mode],fmInput
SYS04D9: JZ 050A
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** to **[SYS:040B Read Function](040B-READ-FUNC.md)** then check if **[File](TEXT-FILE-TYPE.md)** was opened with **[Reset()](FILE-MODES.md)**.

```nasm
SYS04DB: MOV BX,[DI:Handle]
SYS04DD: MOV AX,4400
SYS04E0: INT 21
```

Get device information using **DOS INT 21h AX = 4400h** service with parameter **BX** = File handle. It returns device information in **DX**.

```nasm
SYS04E2: TEST DL,80
SYS04E5: MOV AX,0460
SYS04E8: MOV DX,SYS
SYS04EB: MOV CX,AX
SYS04ED: MOV BX,DX
SYS04EF: JNZ 0505
```

If **[File](TEXT-FILE-TYPE.md)** is a character device (80h), set its **[InOutFunc and FlushFunc](TEXT-FILE-TYPE.md)** to **[SYS:0460 Write Function](0460-WRITE-FUNC.md)**.


```nasm
SYS04F1: CMP WORD PTR [DI:Mode],fmInOut
SYS04F6: JNZ 04FB
SYS04F8: CALL 0526
```

Check if **[File (Mode)](TEXT-FILE-TYPE.md)** was opened using **[Reset() and Rewrite()](FILE-MODES.md)** [move file pointer](0526-OPEN-FUNC-II.md) to end of the file.

```nasm
SYS04FB: MOV AX,043B
SYS04FE: MOV DX,SYS
SYS0501: XOR CX,CX
SYS0503: XOR BX,BX
```

Set **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** to **[SYS:43B Write to File Function](043B-WRITE-TO-FILE-FUNC.md)**.

```nasm
SYS0505: MOV WORD PTR [DI:Mode],fmOutput
```

Mark the **[File](TEXT-FILE-TYPE.md)** as opened using **[Rewrite()](FILE-MODES.md)**.

```nasm
SYS050A: MOV [DI:InOutFunc.Offset],AX
SYS050D: MOV [DI:InOutFunc.Segment],DX
SYS0510: MOV [DI:FlushFunc.Offset],CX
SYS0513: MOV [DI:FlushFunc.Segment],BX
```

Set up the handlers for **[File](TEXT-FILE-TYPE.md)**'s **[InOutFunc](TEXT-FILE-TYPE.md)** and **[FlushFunc](TEXT-FILE-TYPE.md)**. **DX**:**AX**, **BX**:**CX**, at this point contains the correct pointers to the subroutines.

```nasm
SYS0516: MOV WORD PTR [DI:CloseFunc.Offset],0480
SYS051B: MOV WORD PTR [DI:CloseFunc.Segment],SYS
```

Set up the handler for **[File](TEXT-FILE-TYPE.md)**'s **[CloseFunc](TEXT-FILE-TYPE.md)** to **[SYS:0480 Close Function](0480-CLOSE-FUNC.md)**.

```nasm
SYS0520: XOR AX,AX
```

Set I/O result in **[InOutRes](DATA.md)** to 0 on success.

```nasm
SYS0522: POP DS
```

Restores **DS** (Saved in **SYS:049B**).

```nasm
SYS0523: RETF 0004
```

Return and pop-off parameter from the stack. The **[error code](ERROR-CODES.md)** in **AX**, later stored in **[InOutRes](DATA.md)**.

See also: [Text File Type](TEXT-FILE-TYPE.md), [File Modes](FILE-MODES.md), [SYS:040B Read Function](040B-READ-FUNC.md), [SYS:043B Write to File Function](043B-WRITE-TO-FILE-FUNC.md), [SYS:0460 Write Function](0460-WRITE-FUNC.md), [SYS:0480 Close Function](0480-CLOSE-FUNC.md), [SYS:0526 Open Function (II)](0526-OPEN-FUNC-II.md) or go [back](../README.md)