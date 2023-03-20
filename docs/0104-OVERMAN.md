# 0104 Overlay manager not installed

```
SYS:0104 B8D000        MOV	AX,00D0
SYS:0107 83C406        ADD	SP,+06
SYS:010A EB03          JMP	010F
```

This prints a runtime error message with error code [D0/208: Overlay manager not installed](ERROR-CODES.md).

See also: [SYS:010F](010C-INT00H.md) or Go [Back](../README.md)