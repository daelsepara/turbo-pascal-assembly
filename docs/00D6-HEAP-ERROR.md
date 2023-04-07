# Heap Error Exit Routine

This is set up to be the handler for Heap Errors

```nasm
SYS00D6: XOR AX,AX
SYS00D8: RETF 0002
```

Return to calling program and clean-up 2 bytes from the stack.

[Back](../README.md)