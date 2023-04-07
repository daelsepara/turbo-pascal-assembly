# 02CD Check Stack

Checks whether the program's stack size is sufficient.

```nasm
SYS02CD: ADD AX,0200
SYS02D0: JB 02DF
SYS02D2: SUB AX,SP
SYS02D4: JNB 02DF
SYS02D6: NEG AX
SYS02D8: CMP AX,[StackLimit]
SYS02DC: JB 02DF
SYS02DE: RETF
```

Usually, **[StackLimit](DATA.md)** = 0000h, i.e. unlimited. In this case, stack overflow error only happens when **AX** > **SP - 4** ( = size of return address ). Because this is a **FAR RET**, it can be called from anywhere.

```nasm
SYS02DF: MOV AX,00CA
SYS02E2: JMP 010F
```

This prints a runtime error message with error code **[CAh/202: Stack overflow error](ERROR-CODES.md)**.

See also: [SYS:10F](010C-INT00H.md) or Go [Back](../README.md)