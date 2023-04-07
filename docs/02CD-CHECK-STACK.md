# 02CD Check Stack

Checks whether the program's stack size is sufficient.

```nasm
SYS02CD: 050002        ADD	AX,0200
SYS02D0: 720D          JB	02DF
SYS02D2: 2BC4          SUB	AX,SP
SYS02D4: 7309          JNB	02DF
SYS02D6: F7D8          NEG	AX
SYS02D8: 3B063A00      CMP	AX,[StackLimit]
SYS02DC: 7201          JB	02DF
SYS02DE: CB            RETF
```

Usually, **[StackLimit](DATA.md)** = 0000h, i.e. unlimited. In this case, stack overflow error only happens when **AX** > **SP - 4** ( = size of return address ). Because this is a **FAR RET**, it can be called from anywhere.

```nasm
SYS02DF: B8CA00        MOV	AX,00CA
SYS02E2: E92AFE        JMP	010F
```

This prints a runtime error message with error code **[CAh/202: Stack overflow error](ERROR-CODES.md)**.

See also: [SYS:10F](010C-INT00H.md) or Go [Back](../README.md)