# 03FA Unknown

```
SYS:03FA 06            PUSH	ES
SYS:03FB 57            PUSH	DI
SYS:03FC 06            PUSH	ES
SYS:03FD 57            PUSH	DI
SYS:03FE 26            ES:
SYS:03FF FF19          CALL	FAR [BX+DI]
SYS:0401 0BC0          OR	AX,AX
SYS:0403 7403          JZ	0408
SYS:0405 A33C00        MOV	[InOutRes],AX
SYS:0408 5F            POP	DI
SYS:0409 07            POP	ES
SYS:040A C3            RET
```

[Go back](../README.md)