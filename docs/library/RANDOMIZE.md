# Randomize

Initializes the built-in random generator with a random value.

## Pascal program used to generate the compiled code

```
program main;
    begin
        Randomize;
    end.
end program;
```

## Main program disassembly

```
CODE:0000 9A00007207    CALL	SYS:0000
CODE:0005 55            PUSH	BP
CODE:0006 89E5          MOV	BP,SP
CODE:0008 31C0          XOR	AX,AX
CODE:000A 9ACD027207    CALL	SYS:02CD
```

Initialization.

``
CODE:000F 9A15067207    CALL	SYS:0615

Call to **Randomize** (no parameters).

```
CODE:0014 5D            POP	BP
CODE:0015 31C0          XOR	AX,AX
CODE:0017 9A16017207    CALL	SYS:0116
```

Exit program with code 0.

## Randomize

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
SYS:0619 890E3E00      MOV	[RandSeed:LSW],CX
SYS:061D 89164000      MOV	[RandSeed:MSW],DX
```

Store the low (**LSW** = least significant word) and high word (**MSW** = most significant word) into **[RandSeed](../DATA.md)** (*LongInt*).

See also: [RandSeed](../DATA.md), [SYS:0000 System Library Initialization](../0000-INIT.md), [SYS:02CD Check Stack](../02CD-CHECK-STACK.md), [SYS:0116 Program Exit](../0113-CTRL-C-HANDLER.md) or go [back](../../README.md)