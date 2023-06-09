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
SYS02E6: XOR DX,DX
```

Set name length 0.

```nasm
SYS02E8: MOV BX,SP
```

Sets up BX to address data on the stack. **SYS:02E8** is a possible entry point if a non-zero length string were to be assigned to **[File](TEXT-FILE-TYPE.md)**.

```nasm
SYS02EA: PUSH DS
```

Save DS because it will be modified later.

```nasm
SYS02EB: SS:
SYS02EC: LES DI,[BX+08]
```

Loads the pointer to the **[Text File/Record data](TEXT-FILE-TYPE.md)** into **ES**:**DI**.

```nasm
SYS02EF: SS:
SYS02F0: LDS SI,[BX+04]
```

Loads the pointer to the **[Name](0263-DATA-COPYRIGHT.md)** string into **DS**:**SI**.

```nasm
SYS02F3: CLD
```

Set copy direction to forward (**DF** = 0).

```nasm
SYS02F4: XOR AX,AX
SYS02F6: STOSW
```

Initialize **[Handle](TEXT-FILE-TYPE.md)** to 0.

```nasm
SYS02F7: MOV AX,fmClosed
SYS02FA: STOSW
```

Set **[Mode](TEXT-FILE-TYPE.md)** to **[fmClosed](FILE-MODES.md)**.

```nasm
SYS02FB: MOV AX,0080
SYS02FE: STOSW
```

Set **[BufSize](TEXT-FILE-TYPE.md)** to 128  (80h) bytes.

```nasm
SYS02FF: XOR AX,AX
SYS0301: STOSW
SYS0302: STOSW
SYS0303: STOSW
```

Does the following in order:
- Set **[Private](TEXT-FILE-TYPE.md)** to 0.
- Resets **[BufPos](TEXT-FILE-TYPE.md)** pointer/index to the beginning (0).
- Resets **[BufEnd](TEXT-FILE-TYPE.md)**, the number of characters in the buffer, to 0.

```nasm
SYS0304: LEA AX,[DI+74]
SYS0307: STOSW
```

Loads the offset location of **[TextBuf](TEXT-FILE-TYPE.md)** into AX, then sets the offset part of **[BufPtr](TEXT-FILE-TYPE.md)**.

```nasm
SYS0308: MOV AX,ES
SYS030A: STOSW
```

Copies ES which is the SEGMENT location of **[TextBuf](TEXT-FILE-TYPE.md)** into **AX**, then sets the segment part of **[BufPtr](TEXT-FILE-TYPE.md)**.

```nasm
SYS030B: MOV AX,0499
SYS030E: STOSW
SYS030F: MOV AX,SYS
SYS0312: STOSW
```

Sets this **[File](TEXT-FILE-TYPE.md)**'s **[OpenFunc](TEXT-FILE-TYPE.md)** pointer to **[SYS:0499 Open Function](0499-OPEN-FUNC.md)**.

```nasm
SYS0313: XOR AX,AX
SYS0315: MOV CX,000E
SYS0318: REPZ
SYS0319: STOSW
```

Clears the following:
- **[FlushFunc](TEXT-FILE-TYPE.md)**
- **[CloseFunc](TEXT-FILE-TYPE.md)**
- **[UserData](TEXT-FILE-TYPE.md)**

```nasm
SYS031A: MOV CX,004F
SYS031D: OR DX,DX
SYS031F: JNZ 032A
```

Check if there are bytes to copy (up to **CX** = 79/4Fh).

```nasm
SYS0321: LODSB
SYS0322: CMP CL,AL
SYS0324: JBE 032A
```

Check if the string (**filename**) in **DS**:**SI** is 79 characters or less then copy it to this **[Files](TEXT-FILE-TYPE.md)**'s **[Name](TEXT-FILE-TYPE.md)**.

```nasm
SYS0326: MOV CL,AL
SYS0328: JCXZ 0332
```

If greater than **79** bytes, i.e. **CL > AL**, modify the loop counter in **CX**. This may be used to copy Pascal strings whose first byte stores the string length (up to 255). If **CX** = 0, then return immediately because there are no bytes to copy.

```nasm
SYS032A: LODSB
SYS032B: OR AL,AL
SYS032D: JZ 0332
SYS032F: STOSB
SYS0330: LOOP 032A
```

This does the actual copying of the name in **DS**:**SI** to **[Name](TEXT-FILE-TYPE.md)**. Exit loop immediately if a NULL (00h) byte from the source is encountered.

```nasm
SYS0332: XOR AL,AL
SYS0334: STOSB
```

**NULL** (00h) terminates the **[Name](TEXT-FILE-TYPE.md)** string (or the destination string if not a **[File](TEXT-FILE-TYPE.md)**).

```nasm
SYS0335: POP DS
SYS0336: RETF 0008
```

Return and pop-off the two **FAR PTR** parameters (8 bytes) from the stack.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md) or go [back](../README.md)