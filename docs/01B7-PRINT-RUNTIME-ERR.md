# 01B7 Print Runtime error

Prints the Runtime error string which has the form:

```
Runtime error XXX at YYYY:ZZZZ
```

Where **[XXX](ERROR-CODES.md)** is the error code. **YYYY:ZZZZ** is a **FAR PTR** address where the error is triggered. In most cases, the **YYYY:ZZZZ** address here is the return address when this subroutine is called. Because it continues to the terminate/exit code, it never returns to the subroutine that calls it.

```
SYS:01B7 A13400        MOV	AX,[ErrorAddr.Offset]
SYS:01BA 0B063600      OR	AX,[ErrorAddr.Segment]
SYS:01BE 7429          JZ	01E9
```

This checks if there is a need to print the **[ErrorAddr](DATA.md)** above. If **[ErrorAddr](DATA.md)** is not set, e.g. **0000:0000**, then immediately proceed to [SYS:01E9 DOS Exit](01E9-DOS-EXIT.md).

```
SYS:01C0 BB4C02        MOV	BX,SYS:024C
SYS:01C3 E82A00        CALL	01F0
```

Print [*Runtime error*](024C-STRING-RUNTIME-ERR.md) including the trailing whitespace using the **[SYS:01F0 Print string subroutine](01F0-PRINT-STRING.md)**. **SYS:01C0** is also an entry point for terminating the program with an **[ExitCode](DATA.md)** in **AX**.


```
SYS:01C6 A13200        MOV	AX,[ExitCode]
SYS:01C9 E83200        CALL	01FE
```

Print the **[ExitCode](DATA.md)** in base 10 using the **[SYS:01FE Convert number subroutine](1FE-CONVERT-NUMBER.md)**.

```
SYS:01CC BB5B02        MOV	BX,SYS:025B
SYS:01CF E81E00        CALL	01F0
```

Print the [*at*](024C-STRING-RUNTIME-ERR.md) including both whitespace characters at the front and end of the " **at** " string using **[SYS:01F0](01F0-PRINT-STRING.md)**

```
SYS:01D2 A13600        MOV	AX,[ErrorAddr.Segment]
SYS:01D5 E84000        CALL	0218
```

Print the segment address **YYYY** in base 16 (hexadecimal) using the **[SYS:0218 Print digits subroutine](0218-PRINT-DIGITS.md)**.

```
SYS:01D8 B03A          MOV	AL,3A
SYS:01DA E85500        CALL	0232
```

Prints the *:* (colon) character as part of the **YYYY:ZZZZ** address using the **[SYS:0232 Print character subroutine](0232-PRINT-CHAR.m)**.

```
SYS:01DD A13400        MOV	AX,[ErrorAddr.Offset]
SYS:01E0 E83500        CALL	0218
```

Print the offset address **ZZZZ** in base 16 (hexadecimal) using **[SYS:0218](0218-PRINT-DIGITS.md)**.

```
SYS:01E3 BB6002        MOV	BX,SYS:0260
SYS:01E6 E80700        CALL	01F0
```

Finally, print a **[.](024C-STRING-RUNTIME-ERR.md)** and the newline characters using **[SYS:01F0 Print String](01F0-PRINT-STRING.md)**. The code continues to the **[SYS:01E9 DOS Exit](01E9-DOS-EXIT.md)** subroutine.

[Go back](../README.md)