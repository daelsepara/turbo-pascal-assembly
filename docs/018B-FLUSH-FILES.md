# 018B Flush and Close Input and Output Files

```nasm
SYS018B: B85000        MOV	AX,Input
SYS018E: 1E            PUSH	DS
SYS018F: 50            PUSH	AX
SYS0190: 0E            PUSH	CS
SYS0191: E82A02        CALL	03BE
SYS0194: B85001        MOV	AX,Output
SYS0197: 1E            PUSH	DS
SYS0198: 50            PUSH	AX
SYS0199: 0E            PUSH	CS
SYS019A: E82102        CALL	03BE
```

Flushes and closes **Input** and **Output** buffers on call to **[SYS:03BE Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md)**. Continues to **[SYS:019D Restore interrupt vectors](019D-RESTORE-INT.md)**.

See also: [SYS:03BE Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md) or go [back](../README.md)