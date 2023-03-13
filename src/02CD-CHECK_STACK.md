# 02CD CHECK STACK

```
SYS:02CD 050002        ADD	AX,0200
SYS:02D0 720D          JB	02DF
SYS:02D2 2BC4          SUB	AX,SP
SYS:02D4 7309          JNB	02DF
SYS:02D6 F7D8          NEG	AX
SYS:02D8 3B063A00      CMP	AX,[003A]
SYS:02DC 7201          JB	02DF
SYS:02DE CB            RETF
```

Check if stack size is above the limit, otherwise return.


```
SYS:02DF B8CA00        MOV	AX,00CA
SYS:02E2 E92AFE        JMP	010F
```

Exit program with an error code CAh