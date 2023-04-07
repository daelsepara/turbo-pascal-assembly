# 02C7 Arithmetic overflow error

```nasm
SYS02C7: B8D700        MOV	AX,00D7
SYS02CA: E942FE        JMP	010F
```

This prints a runtime error message with error code **[D7h/202: Arithmetic overflow error](ERROR-CODES.md)**.

See also: [SYS:10F Runtime error](010C-INT00H.md) or Go [Back](../README.md)