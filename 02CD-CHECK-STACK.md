# 02CD CHECK STACK

Checks whether the program's stack size is sufficient.

```
SYS:02CD 050002        ADD	AX,0200
SYS:02D0 720D          JB	02DF
SYS:02D2 2BC4          SUB	AX,SP
SYS:02D4 7309          JNB	02DF
SYS:02D6 F7D8          NEG	AX
SYS:02D8 3B063A00      CMP	AX,[StackLimit]
SYS:02DC 7201          JB	02DF
SYS:02DE CB            RETF
```

Usually, DS:[StackLimit] = 0000, i.e. unlimited. In this case, stack overflow error only happens when AX > SP - 4 ( = size of return address). Because this is a **FAR RET**, it can be called from anywhere.

```
SYS:02DF B8CA00        MOV	AX,00CA
SYS:02E2 E92AFE        JMP	010F
```

This prints a runtime error message with error code [CA/202: Stack overflow error](ERROR-CODES.md).

[Back](README.md)