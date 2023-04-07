# 02E6 Assign Function

This is Turbo Pascal's **Assign**(**[File](TEXT-FILE-TYPE.md)**, *Name*) function. It takes two parameters:
- **File**: Pointer to the [File Record](TEXT-FILE-TYPE.md)
- **Name**: Pointer to **[string](0263-DATA-COPYRIGHT.md)**  to assign to the **File** 

Upon calling this routine, this is how the stack looks like:

## Stack upon call to SYS:02E6 Assign Function

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |Return Address (OFFSET)                   |
|SP+02|Return Address (SEGMENT)                  |
|SP+04|Pointer to name (OFFSET )                 |
|SP+06|Pointer to name (SEGMENT)                 |
|SP+08|Pointer to File/Text Record Data (OFFSET) |
|SP+0A|Pointer to File/Text Record Data (SEGMENT)|


```nasm
SYS02E6: 33D2          XOR	DX,DX
```

Set name length 0.

```nasm
SYS02E8: 8BDC          MOV	BX,SP
```

Sets up BX to address data on the stack. **SYS:02E8** is a possible entry point if a non-zero length string were to be assigned to **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS02EA: 1E            PUSH	DS
```

Save DS because it will be modified later.

```nasm
SYS02EB: 36            SS:
SYS02EC: C47F08        LES	DI,[BX+08]
```

Loads the pointer to the **[Text File/Record data](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS02EF: 36            SS:
SYS02F0: C57704        LDS	SI,[BX+04]
```

Loads the pointer to the **[Name](0263-DATA-COPYRIGHT.md)** string into **DS**:**SI**.

```nasm
SYS02F3: FC            CLD
```

Set copy direction to forward (**DF** = 0).

```nasm
SYS02F4: 33C0          XOR	AX,AX
SYS02F6: AB            STOSW
```

Initialize **[Handle](TEXT-FILE-TYPE.md)** to 0.

```nasm
SYS02F7: B8B0D7        MOV	AX,fmClosed
SYS02FA: AB            STOSW
```

Set **[Mode](TEXT-FILE-TYPE.md)** to **[fmClosed](FILE-MODES.md)**.

```nasm
SYS02FB: B88000        MOV	AX,0080
SYS02FE: AB            STOSW
```

Set **[BufSize](TEXT-FILE-TYPE.md)** to 128  (80h) bytes.

```nasm
SYS02FF: 33C0          XOR	AX,AX
SYS0301: AB            STOSW
SYS0302: AB            STOSW
SYS0303: AB            STOSW
```

Does the following in order:
- Set **[Private](TEXT-FILE-TYPE.md)** to 0.
- Resets **[BufPos](TEXT-FILE-TYPE.md)** pointer/index to the beginning (0).
- Resets **[BufEnd](TEXT-FILE-TYPE.md)**, the number of characters in the buffer, to 0.

```nasm
SYS0304: 8D4574        LEA	AX,[DI+74]
SYS0307: AB            STOSW
```

Loads the offset location of **[TextBuf](TEXT-FILE-TYPE.md)** into AX, then sets the offset part of **[BufPtr](TEXT-FILE-TYPE.md)**.

```nasm
SYS0308: 8CC0          MOV	AX,ES
SYS030A: AB            STOSW
```

Copies ES which is the SEGMENT location of **[TextBuf](TEXT-FILE-TYPE.md)** into **AX**, then sets the segment part of **[BufPtr](TEXT-FILE-TYPE.md)**.

```nasm
SYS030B: B89904        MOV	AX,0499
SYS030E: AB            STOSW
SYS030F: B87007        MOV	AX,SYS
SYS0312: AB            STOSW
```

Sets this **[File](TEXT-FILE-TYPE.md)**'s **[OpenFunc](TEXT-FILE-TYPE.md)** pointer to **[SYS:0499 Open Function](0499-OPEN-FUNC.md)**.

```nasm
SYS0313: 33C0          XOR	AX,AX
SYS0315: B90E00        MOV	CX,000E
SYS0318: F3            REPZ
SYS0319: AB            STOSW
```

Clears the following:
- **[FlushFunc](TEXT-FILE-TYPE.md)**
- **[CloseFunc](TEXT-FILE-TYPE.md)**
- **[UserData](TEXT-FILE-TYPE.md)**

```nasm
SYS031A: B94F00        MOV	CX,004F
SYS031D: 0BD2          OR	DX,DX
SYS031F: 7509          JNZ	032A
```

Check if there are bytes to copy (up to **CX** = 79/4Fh).

```nasm
SYS0321: AC            LODSB
SYS0322: 3AC8          CMP	CL,AL
SYS0324: 7604          JBE	032A
```

Check if the string (**filename**) in **DS**:**SI** is 79 characters or less then copy it to this **[Files](TEXT-FILE-TYPE.md)**'s **[Name](TEXT-FILE-TYPE.md)**.

```nasm
SYS0326: 8AC8          MOV	CL,AL
SYS0328: E308          JCXZ	0332
```

If greater than **79** bytes, i.e. **CL > AL**, modify the loop counter in **CX**. This may be used to copy Pascal strings whose first byte stores the string length (up to 255). If **CX** = 0, then return immediately because there are no bytes to copy.

```nasm
SYS032A: AC            LODSB
SYS032B: 0AC0          OR	AL,AL
SYS032D: 7403          JZ	0332
SYS032F: AA            STOSB
SYS0330: E2F8          LOOP	032A
```

This does the actual copying of the name in **DS**:**SI** to **[Name](TEXT-FILE-TYPE.md)**. Exit loop immediately if a NULL (00h) byte from the source is encountered.

```nasm
SYS0332: 32C0          XOR	AL,AL
SYS0334: AA            STOSB
```

**NULL** (00h) terminates the **[Name](TEXT-FILE-TYPE.md)** string (or the destination string if not a **[File](TEXT-FILE-TYPE.md)**).

```nasm
SYS0335: 1F            POP	DS
SYS0336: CA0800        RETF	0008
```

Return and pop-off the two **FAR PTR** parameters (8 bytes) from the stack.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md) or go [back](../README.md)