# 01B7 Print Runtime error

Prints the Runtime error string which has the form:

```
Runtime error XXX at YYYY:ZZZZ
```

Where **[XXX](ERROR-CODES.md)** is the error code. **YYYY:ZZZZ** is a **FAR PTR** address where the error is triggered. In most cases, the **YYYY:ZZZZ** address here is the return address when this subroutine is called. Because it continues to the terminate/exit code, it never returns to the subroutine that calls it.

```nasm
SYS01B7: MOV AX,[ErrorAddr.Offset]
SYS01BA: OR AX,[ErrorAddr.Segment]
SYS01BE: JZ 01E9
```

This checks if there is a need to print the **[ErrorAddr](DATA.md)** above. If **[ErrorAddr](DATA.md)** is not set, e.g. **0000:0000**, then immediately proceed to [SYS:01E9 DOS Exit](01E9-DOS-EXIT.md).

```nasm
SYS01C0: MOV BX,024C
SYS01C3: CALL 01F0
```

Print [*Runtime error*](024C-STRING-RUNTIME-ERR.md) including the trailing whitespace using the **[SYS:01F0 Print string subroutine](01F0-PRINT-STRING.md)**. **SYS:01C0** is also an entry point for terminating the program with an **[ExitCode](DATA.md)** in **AX**.


```nasm
SYS01C6: MOV AX,[ExitCode]
SYS01C9: CALL 01FE
```

Print the **[ExitCode](DATA.md)** in base 10 using the **[SYS:01FE Convert number subroutine](1FE-CONVERT-NUMBER.md)**.

```nasm
SYS01CC: MOV BX,025B
SYS01CF: CALL 01F0
```

Print the [*at*](024C-STRING-RUNTIME-ERR.md) including both whitespace characters at the front and end of the " **at** " string using **[SYS:01F0](01F0-PRINT-STRING.md)**

```nasm
SYS01D2: MOV AX,[ErrorAddr.Segment]
SYS01D5: CALL 0218
```

Print the segment address **YYYY** in base 16 (hexadecimal) using the **[SYS:0218 Print digits subroutine](0218-PRINT-DIGITS.md)**.

```nasm
SYS01D8: MOV AL,3A
SYS01DA: CALL 0232
```

Prints the *:* (colon) character as part of the **YYYY:ZZZZ** address using the **[SYS:0232 Print character subroutine](0232-PRINT-CHAR.m)**.

```nasm
SYS01DD: MOV AX,[ErrorAddr.Offset]
SYS01E0: CALL 0218
```

Print the offset address **ZZZZ** in base 16 (hexadecimal) using **[SYS:0218](0218-PRINT-DIGITS.md)**.

```nasm
SYS01E3: MOV BX,0260
SYS01E6: CALL 01F0
```

Finally, print a **[.](024C-STRING-RUNTIME-ERR.md)** and the newline characters using **[SYS:01F0 Print String](01F0-PRINT-STRING.md)**. The code continues to the **[SYS:01E9 DOS Exit](01E9-DOS-EXIT.md)** subroutine.

[Go back](../README.md)