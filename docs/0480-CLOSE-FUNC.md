# 0480 CloseFunc

Upon entry the stack looks like this:

|Index|Contents                                  |
|-----|------------------------------------------|
|SP   |Return Address (OFFSET)                   |
|SP+02|Return Address (SEGMENT)                  |
|SP+04|Pointer to File/Text Record Data (OFFSET) |
|SP+06|Pointer to File/Text Record Data (SEGMENT)|


```
SYS:0480 8BDC          MOV	BX,SP
```

Use BX to address items in the stack.

```
SYS:0482 36            SS:
SYS:0483 C47F04        LES	DI,[BX+04]
```

Loads the pointer to the **[File](TEXT-FILE-TYPE.md)** into ES:DI.

```
SYS:0486 26            ES:
SYS:0487 8B1D          MOV	BX,[DI]
```

Loads the  (**[Handle](TEXT-FILE-TYPE.md)**) in **[File](TEXT-FILE-TYPE.md)** into BX.

```
SYS:0489 83FB04        CMP	BX,+04
SYS:048C 7606          JBE	0494
```

Check if *[Handle](TEXT-FILE-TYPE.md)** is a standard handle provided by **DOS**. Exit immediately if it is a **DOS handle**.

```
SYS:048E B43E          MOV	AH,3E
SYS:0490 CD21          INT	21
```

Close file handle using **DOS INT 21h AH=3Eh** service with parameter **BX** = File handle.

```
SYS:0492 7202          JB	0496
```

Exit with DOS Error code if an error occured.

```
SYS:0494 33C0          XOR	AX,AX
```

**AX** = 0 (No errors).

```
SYS:0496 CA0400        RETF	0004
```

Return and pop-off parameters from the stack.

See also: [Text File Type](TEXT-FILE-TYPE.md) or go [back](../README.md)