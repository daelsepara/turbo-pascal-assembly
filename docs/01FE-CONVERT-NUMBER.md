# 01FE Convert number to ASCII

This subroutine takes an unsigned number in AL to a string. The base of the string is set in CL.

```
SYS:01FE B164          MOV	CL,64
SYS:0200 E80700        CALL	020A
```

This converts the number in **AL** into a base-100 digit. This takles only the upper digit in **AL** and converts it to '0', '1', or '2'.

```
SYS:0203 B10A          MOV	CL,0A
SYS:0205 E80200        CALL	020A
SYS:0208 EB04          JMP	020E
```

This converts the number in **AL** into a base-10 string. This works best only if **AL** is less than 100 (64h). It converts the first half with a call to **SYS:020A**, then converts the second half with a jump to **SYS:020E** (see below).

```
SYS:020A 32E4          XOR	AH,AH
SYS:020C F6F1          DIV	CL
```

This is the actual digit to ascii conversion. It is possible to call this subroutine at **offset SYS:020A**. To do so, ensure that **CL** contains the base (1-255/01h-FFh) and **AL** contains the number to convert (0-255/00h-FFh).

Conversion is done one digit at a time. First, it clears the upper half of **AX**, i.e. **AH** and divides the **AX** by the base in **CL**. The result is in AL while the remainder is in **AH**.

```
SYS:020E 0430          ADD	AL,30
SYS:0210 50            PUSH	AX
SYS:0211 E81E00        CALL	0232
SYS:0214 58            POP	AX
```

This converts the result in **AL** to ASCII by adding 30h ('0') and calling **[SYS:0232 Print Digits](0218-PRINT-DIGITS.md)** to print it.

```
SYS:0215 8AC4          MOV	AL,AH
SYS:0217 C3            RET
```

This puts the remainder back in **AL** then returns to the calling subroutine. If this was called from **SYS:0200**, it would have converted the upper digit already and will now proceed to **SYS:0203** and convert the lower digits. Otherwise, it will return to the caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)