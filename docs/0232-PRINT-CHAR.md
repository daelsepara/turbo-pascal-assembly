# 0232 Print character

This is service is used to a print single character in **AL**. It is all used to print characters of **NULL** (00h) terminated strings or the digits of a number.

```nasm
SYS0232: MOV DL,AL
SYS0234: MOV AH,06
SYS0236: INT 21
SYS0238: RET
```

Prints the ASCII character in **DL** directly to the console using **BIOS INT 10h AH = 06h** service. If **DL** is within the range: 0 (00h) < **DL** < 255 (FEh), then the character is printed on the console, otherwise if **DL** = 255 (FFh) then a single character input is requested instead.

Because it is a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)