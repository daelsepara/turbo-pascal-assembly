# 0580 Clear input/output file records and interrupt vector array

```nasm
SYS0580: MOV DI,Input
```
Start at **[Input](DATA.md)** (in system data segment).

```nasm
SYS0583: PUSH DS
SYS0584: POP ES
```

Copy **DS** into **ES**. STOSW stores word (in **AX**) into **ES**:[**DI**].

```nasm
SYS0585: MOV CX,SizeOf(DATA)
SYS0588: SUB CX,DI
SYS058A: SHR CX,1
```

Clear memory from **0050h** (**[Input](DATA.md)**) to **SizeOf(DATA) = 029Ch** (**[SaveInt75](DATA.md)**) or around ((**029Ch** - **0050h**) / 2) = **294** words. **029Ch** is the size of the **DATA** segment (see: **[MAIN.MAP](MAIN-MAP.md)**).

```nasm
SYS058C: XOR AX,AX
```

Clear memory with **0000**.

```nasm
SYS058E: CLD
```

Proceed in forward direction, i.e. **DI** increases each iteration.

```nasm
SYS058F: REPZ
SYS0590: STOSW
```

Clear until CX = 0. CX is decremented by 1 each time STOSW is executed


```nasm
SYS0591: RET
```

Return to caller. Because it returns with a **NEAR RET**, it can only be called from within the system library.

See also: [DATA](DATA.md) or go [back](../README.md)