# 0580 Clear input/output file records and interrupt vector array

```
SYS:0580 BF5000        MOV	DI,0050
```
Start at 0050 (in system data segment). Input = 0050 (see: [System Data](DATA.md))

```
SYS:0583 1E            PUSH	DS
SYS:0584 07            POP	ES
```

Copy DS into ES. STOSW stores word (in AX) into ES:[DI].

```
SYS:0585 B99C02        MOV	CX,029C
SYS:0588 2BCF          SUB	CX,DI
SYS:058A D1E9          SHR	CX,1
```

Clear memory from 0050h (**Input**) to 029Ch (**SaveInt75**) or around ((029Ch - 0050h) / 2) = 294 words (See: [DATA](DATA.md)).

```
SYS:058C 33C0          XOR	AX,AX
```

Fill memory with 0000h value

```
SYS:058E FC            CLD
```

Proceed in forward direction, i.e. DI increases each iteration.

```
SYS:058F F3            REPZ
SYS:0590 AB            STOSW
```

Clear until CX = 0. CX is decremented by 1 each time STOSW is executed


```
SYS:0591 C3            RET
```

Return to caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

[Back](../README.md)