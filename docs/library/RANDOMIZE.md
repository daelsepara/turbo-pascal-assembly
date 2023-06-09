# Randomize

Initializes the built-in random generator with a random value.

```nasm
SYS0615: MOV AH,2C
SYS0617: INT 21
```

Returns the current system time using **DOS INT 21h AH = 2Ch** service.

Upon return:
- **CH** = hour (0 to 23)
- **CL** = minutes (0 to 59)
- **DH** = seconds (0 to 59)
- **DL** = hundreds of a second (0 to 99)

```nasm
SYS0619: MOV [RandSeed.Low],CX
SYS061D: MOV [RandSeed.High],DX
SYS0621: RETF
```

Store the 32-bit number in **DX**:**CX** into **[RandSeed](../DATA.md)** (*LongInt*) and return.

See also: [RandSeed](../DATA.md), or go [back](../../README.md)