# 03FA I/O Function Dispatcher

This is the I/O Function Dispatcher for a **[File](TEXT-FILE-TYPE.md)**. It is called from within the system library with **BX** containing the offset to the function we are calling.

## Possible values for BX

|BX  |Function | Description                                                |
|----|---------|------------------------------------------------------------|
|0010|OpenFunc |Pointer to I/O routine that opens this file                 |
|0014|InOutFunc|Pointer to I/O routine that performs read/write on this file|
|0018|FlushFunc|Pointer to I/O routine that flushes the buffer              |
|001C|CloseFunc|Pointer to I/O routine that closes this file                |

These offsets are the are actually offsets to **[File record](TEXT-FILE-TYPE.md)**.

```nasm
SYS03FA: PUSH ES
SYS03FB: PUSH DI
```

Save **ES**:**DI** on the stack.

```nasm
SYS03FC: PUSH ES
SYS03FD: PUSH DI
```

Pass pointer to **[File](TEXT-FILE-TYPE.md)** in **ES**:**DI** to the function.

```nasm
SYS03FE: ES:
SYS03FF: CALL FAR [BX+DI]
```

Make a far pointer to the function determined by **ES**:[**DI+BX**] (see table above).

```nasm
SYS0401: OR AX,AX
```

Check if the I/O functions generated an error code in **AX**.

```nasm
SYS0403: JZ 0408
```

Return to caller immediately if there were no errors.

```nasm
SYS0405: MOV [InOutRes],AX
```

Record the error code in **[InOutRes](DATA.md)**.

```nasm
SYS0408: POP DI
SYS0409: POP ES
```

Upon returning from the I/O functions, the parameters passed to it, i.e. **ES**:**DI** were popped-off from the stack. At this point, after restoring **ES**:**DI**, the stack is already balanced.

```nasm
SYS040A: RET
```

Return to caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [SYS:0499 Open Function](0499-OPEN-FUNC.md), [SYS:0480 Close Function](0480-CLOSE-FUNC.md), [SYS:040B Read Function](040B-READ-FUNC.md), [SYS:0460 Write Function](0460-WRITE-FUNC.md), [SYS:043B Write to File Function](043B-WRITE-TO-FILE-FUNC.md) or go [back](../README.md)