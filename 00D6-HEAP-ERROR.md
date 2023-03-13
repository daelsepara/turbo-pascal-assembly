# Heap Error Exit Routine

```
SYS:00D6 33C0          XOR	AX,AX  
SYS:00D8 CA0200        RETF	0002   
```

Return to calling program and clean-up 2 words from the stack.

[Back](README.md)