# 01F0 Print string

Print a NULL-terminated string whose address is in [BX]. This is used by the system library to print internal strings and error messages.

```nasm
SYS01F0: CS:
SYS01F1: MOV AL,[BX]
SYS01F3: OR AL,AL
SYS01F5: JZ 01FD
SYS01F7: CALL 0232
SYS01FA: INC BX
SYS01FB: JMP 01F0
SYS01FD: RET
```

This will load one character at a time from [BX], then call the [print character service](0232-PRINT-CHAR.md) in [SYS:232](0232-PRINT-CHAR.md). It will continue printing a character until a **NULL** (00h) byte is encountered. The strings it prints must be located within the system library's code segment.

Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)