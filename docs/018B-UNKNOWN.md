# 018B Unknown

```
SYS:018B B85000        MOV	AX,OFFSET Input
SYS:018E 1E            PUSH	DS
SYS:018F 50            PUSH	AX
SYS:0190 0E            PUSH	CS
SYS:0191 E82A02        CALL	03BE
SYS:0194 B85001        MOV	AX,OFFSET Output
SYS:0197 1E            PUSH	DS
SYS:0198 50            PUSH	AX
SYS:0199 0E            PUSH	CS
SYS:019A E82102        CALL	03BE
```

Flushes and closes **Input** and **Output** buffers on call to **SYS:03BE**. Continues to [SYS:019D Restore interrupt vectors](019D-RESTORE-INT.md).

See also: [SYS:03BE](03BA-UNKNOWN.md) or Go [back](../README.md)