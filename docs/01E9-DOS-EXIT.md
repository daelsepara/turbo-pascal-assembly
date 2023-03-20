# 01E9 DOS Exit

```
SYS:01E9 A13200        MOV	AX,[ExitCode]
SYS:01EC B44C          MOV	AH,4C
SYS:01EE CD21          INT	21
```

Exits the programe with an [ExitCode](DATA.md) using the DOS INT 21h AH=4Ch function. The exit code is in AL.

[Go back](../README.md)