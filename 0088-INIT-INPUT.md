# Initialize INPUT

```
SYS:0088 B85000        MOV	AX,OFFSET INPUT
SYS:008B 1E            PUSH	DS
SYS:008C 50            PUSH	AX
SYS:008D 1E            PUSH	DS
SYS:008E 50            PUSH	AX
SYS:008F B86302        MOV	AX,0263
SYS:0092 0E            PUSH	CS
SYS:0093 50            PUSH	AX
SYS:0094 0E            PUSH	CS
SYS:0095 E84E02        CALL	02E6
SYS:0098 0E            PUSH	CS
SYS:0099 E8C802        CALL	0364
```

[Back](README.md)