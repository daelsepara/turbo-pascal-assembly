# 0580 Clear input/output file records and interrupt vector array

```
SYS:0580 BF5000        MOV	DI,Input
```
Start at **[Input](DATA.md)** (in system data segment).

```
SYS:0583 1E            PUSH	DS
SYS:0584 07            POP	ES
```

Copy **DS** into **ES**. STOSW stores word (in **AX**) into **ES**:[**DI**].

```
SYS:0585 B99C02        MOV	CX,SizeOf(DATA)
SYS:0588 2BCF          SUB	CX,DI
SYS:058A D1E9          SHR	CX,1
```

Clear memory from **0050h** (**[Input](DATA.md)**) to **SizeOf(DATA) = 029Ch** (**[SaveInt75](DATA.md)**) or around ((**029Ch** - **0050h**) / 2) = **294** words. **029Ch** is the size of the **DATA** segment (see: **[MAIN.MAP](MAIN-MAP.md)**).

```
SYS:058C 33C0          XOR	AX,AX
```

Clear memory with **0000**.

```
SYS:058E FC            CLD
```

Proceed in forward direction, i.e. **DI** increases each iteration.

```
SYS:058F F3            REPZ
SYS:0590 AB            STOSW
```

Clear until CX = 0. CX is decremented by 1 each time STOSW is executed


```
SYS:0591 C3            RET
```

Return to caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [DATA](DATA.md) or go [back](../README.md)