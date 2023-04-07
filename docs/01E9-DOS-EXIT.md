# 01E9 DOS Exit

```nasm
SYS01E9: A13200        MOV	AX,[ExitCode]
SYS01EC: B44C          MOV	AH,4C
SYS01EE: CD21          INT	21
```

Exits the program with an [ExitCode](DATA.md) using the **DOS INT 21h AH=4Ch** function. The exit code is in AL.

[Go back](../README.md)