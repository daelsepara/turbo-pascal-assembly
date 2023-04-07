# 01F0 Print string

Print a NULL-terminated string whose address is in [BX]. This is used by the system library to print internal strings and error messages.

```nasm
SYS:01F0 2E            CS:
SYS:01F1 8A07          MOV	AL,[BX]
SYS:01F3 0AC0          OR	AL,AL
SYS:01F5 7406          JZ	01FD
SYS:01F7 E83800        CALL	0232
SYS:01FA 43            INC	BX
SYS:01FB EBF3          JMP	01F0
SYS:01FD C3            RET
```

This will load one character at a time from [BX], then call the [print character service](0232-PRINT-CHAR.md) in [SYS:232](0232-PRINT-CHAR.md). It will continue printing a character until a **NULL** (00h) byte is encountered. The strings it prints must be located within the system library's code segment.

Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)