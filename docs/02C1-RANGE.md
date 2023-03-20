# 02C1 Range check error

```
SYS:02C1 B8C900        MOV	AX,00C9
SYS:02C4 E948FE        JMP	010F
```

This prints a runtime error message with error code [C9/201: Range check error](ERROR-CODES.md).

See also: [SYS:10F](010C-INT00H.md) or Go [Back](../README.md)