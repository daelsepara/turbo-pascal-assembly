# 0218 Print digits

```
SYS:0218 50            PUSH	AX
SYS:0219 8AC4          MOV	AL,AH
SYS:021B E80100        CALL	021F
SYS:021E 58            POP	AX
```

This will convert number in **AX** to ASCII. It first converts the upper half (8-bits) in **AH** by a call to **SYS:021F** (see below), copying **AH** into **AL** beforehand. When it returns, it will now proceed to convert the bottom half in **AL**.

```
SYS:021F 50            PUSH	AX
SYS:0220 B104          MOV	CL,04
SYS:0222 D2E8          SHR	AL,CL
SYS:0224 E80300        CALL	022A
SYS:0227 58            POP	AX
```

This will convert the number in **AL** to an ASCII character. It does the conversion one nibble (4-bits) at a time, converting the upper 4-bits first then calling **SYS:022A** (see below). The upper 4-bits are shifted right into the lower 4-bits prior to the call.

```
SYS:0228 240F          AND	AL,0F
SYS:022A 0430          ADD	AL,30
SYS:022C 3C3A          CMP	AL,3A
SYS:022E 7202          JB	0232
```

Clears upper half (or the upper 4-bits) of **AL** then converts **AL** to an ASCII character. If the ASCII character is from '0' to '9' then go ahead and print it by jumping to **[SYS:0232 Print Character](0232-PRINT-CHAR.md)**. 

Why is the upper half cleared first? This ensures that the conversion is handled properly and the output limited to '0'-'9' and 'A' to 'F'. If this the call was made to **SYS:022A** instead of **SYS:0228**, it is assumed that the upper half is already cleared, usually by shifting **AL** to the right by 4 bits (see above).

```
SYS:0230 0407          ADD	AL,07
```

This convert ASCII digits above '9' to 'A' - 'F'.

From here, it continues into the **[SYS:232 Print Character](0232-PRINT-CHAR.md)**, to print the ASCII character in **AL**.

[Back](../README.md)
