# Randomize

Initializes the built-in random generator with a random value.

```
SYS:0615 B42C          MOV	AH,2C
SYS:0617 CD21          INT	21
```

Returns the current system time using **DOS INT 21h AH = 2Ch** service.

Upon return:
- **CH** = hour (0 to 23)
- **CL** = minutes (0 to 59)
- **DH** = seconds (0 to 59)
- **DL** = hundreds of a second (0 to 99)

```
SYS:0619 890E3E00      MOV	[RandSeed.Low],CX
SYS:061D 89164000      MOV	[RandSeed.High],DX
SYS:0621 CB            RETF
```

Store the 32-bit number in **DX**:**CX** into **[RandSeed](../DATA.md)** (*LongInt*) and return.

See also: [RandSeed](../DATA.md), or go [back](../../README.md)