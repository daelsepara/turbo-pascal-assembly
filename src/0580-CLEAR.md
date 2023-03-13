# 0580 Clear input/output buffers and interrupt vector array

```
0580 BF5000        MOV	DI,0050
```
Start at 0050 (in system data segment). Input = 0050 (see: [System Data](DATA.md))

```
0583 1E            PUSH	DS
0584 07            POP	ES
```

Copy DS into ES. STOSW stores word (in AX) into ES:[DI].

```
0585 B99C02        MOV	CX,029C
0588 2BCF          SUB	CX,DI
058A D1E9          SHR	CX,1
```

Clear memory from 0050 (**Input**)  to 029C (**SaveInt75**) or around 294 words. Code computes this value

```
058C 33C0          XOR	AX,AX
```

Fill memory with 0000h value

```
058E FC            CLD
```

Proceed in forward direction, i.e. DI increases each iteration.

```
058F F3            REPZ
0590 AB            STOSW
```

Clear until CX = 0. CX is decremented by 1 each time STOSW is executed


```
0591 C3            RET
```

Return to calling program. This is a near return, implying that it is called from system library.

Go back to: [System Library](SYSTEM.md) or [Table of Contents](README.md)