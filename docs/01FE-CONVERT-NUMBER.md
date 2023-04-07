# 01FE Convert number to ASCII

This subroutine takes an unsigned number in AL to a string. The base of the string is set in CL.

```nasm
SYS01FE: MOV CL,64
SYS0200: CALL 020A
```

This converts the number in **AL** into a base-100 digit. This takles only the upper digit in **AL** and converts it to '0', '1', or '2'.

```nasm
SYS0203: MOV CL,0A
SYS0205: CALL 020A
SYS0208: JMP 020E
```

This converts the number in **AL** into a base-10 string. This works best only if **AL** is less than 100 (64h). It converts the first half with a call to **SYS:020A**, then converts the second half with a jump to **SYS:020E** (see below).

```nasm
SYS020A: XOR AH,AH
SYS020C: DIV CL
```

This is the actual digit to ascii conversion. It is possible to call this subroutine at **offset SYS:020A**. To do so, ensure that **CL** contains the base (1-255/01h-FFh) and **AL** contains the number to convert (0-255/00h-FFh).

Conversion is done one digit at a time. First, it clears the upper half of **AX**, i.e. **AH** and divides the **AX** by the base in **CL**. The result is in AL while the remainder is in **AH**.

```nasm
SYS020E: ADD AL,30
SYS0210: PUSH AX
SYS0211: CALL 0232
SYS0214: POP AX
```

This converts the result in **AL** to ASCII by adding 30h ('0') and calling **[SYS:0232 Print Digits](0218-PRINT-DIGITS.md)** to print it.

```nasm
SYS0215: MOV AL,AH
SYS0217: RET
```

This puts the remainder back in **AL** then returns to the calling subroutine. If this was called from **SYS:0200**, it would have converted the upper digit already and will now proceed to **SYS:0203** and convert the lower digits. Otherwise, it will return to the caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)