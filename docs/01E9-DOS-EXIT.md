# 01E9 DOS Exit

```nasm
SYS01E9: MOV AX,[ExitCode]
SYS01EC: MOV AH,4C
SYS01EE: INT 21
```

Exits the program with an [ExitCode](DATA.md) using the **DOS INT 21h AH=4Ch** function. The exit code is in AL.

[Go back](../README.md)