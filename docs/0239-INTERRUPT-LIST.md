# 0239 Interrupt list (InterruptList)

Inside SYS code segment, on addresses 0239-024B is a list of interrupts whose vectors are saved and modified during initialization and restored on exit whether it was a normal termination or due to a runtime error.

```
SYS0230:               00 02 1B 21 23 24 34
SYS0240: 9 3A 3B 3C-3D 3E 3F 75
```

[Go back](../README.md)