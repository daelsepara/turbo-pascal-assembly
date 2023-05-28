# Random Function: (*Real*)

Returns a random number (***Real*** type, 6-bytes) in the range **0.0** <= ***X*** < **1.0**.

```nasm
SYS059D: CALL 05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

```nasm
SYS05A0: XCHG BX,AX
SYS05A1: MOV AX,0080
SYS05A4: MOV CX,0020
SYS05A7: TEST DH,80
SYS05AA: JNZ 05B6
SYS05AC: SHL BX,1
SYS05AE: RCL DX,1
SYS05B0: DEC AL
SYS05B2: LOOP 05A7
SYS05B4: XOR AL,AL
SYS05B6: AND DH,7F
SYS05B9: RETF
```

See also: [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)