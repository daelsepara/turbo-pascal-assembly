# Random Function: (*Real*) 

Returns a random number (***Real*** type, 6-bytes) in the range **0.0** <= ***X*** < **1.0**.

```
SYS:059D E83D00        CALL	05DD
```

Call **[SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md)**.

```
SYS:05A0 93            XCHG	BX,AX
SYS:05A1 B88000        MOV	AX,0080
SYS:05A4 B92000        MOV	CX,0020
SYS:05A7 F6C680        TEST	DH,80
SYS:05AA 750A          JNZ	05B6
SYS:05AC D1E3          SHL	BX,1
SYS:05AE D1D2          RCL	DX,1
SYS:05B0 FEC8          DEC	AL
SYS:05B2 E2F3          LOOP	05A7
SYS:05B4 32C0          XOR	AL,AL
SYS:05B6 80E67F        AND	DH,7F
SYS:05B9 CB            RETF
```

See also: [SYS:05DD Random Number Generator Engine](RANDOM-ENGINE.md) or go [back](../../README.md)