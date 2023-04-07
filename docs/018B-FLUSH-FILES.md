# 018B Flush and Close Input and Output Files

```nasm
SYS018B: MOV AX,Input
SYS018E: PUSH DS
SYS018F: PUSH AX
SYS0190: PUSH CS
SYS0191: CALL 03BE
SYS0194: MOV AX,Output
SYS0197: PUSH DS
SYS0198: PUSH AX
SYS0199: PUSH CS
SYS019A: CALL 03BE
```

Flushes and closes **Input** and **Output** buffers on call to **[SYS:03BE Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md)**. Continues to **[SYS:019D Restore interrupt vectors](019D-RESTORE-INT.md)**.

See also: [SYS:03BE Reset/Rewrite Function (II)](03BA-RESET-REWRITE-FUNC-II.md) or go [back](../README.md)