# MAIN.DMP

## Registers
```
AX=FFFF  BX=0000  CX=0B64  DX=0000  SP=4000  BP=0000  SI=0000  DI=0000  
DS=075E  ES=075E  SS=07F4  CS=076E  IP=0000   NV UP EI PL NZ NA PO NC 
```

## Segments

When the program is loaded in memory, ES AND DS points to the Program Segment Prefix (PSP). Stack size is around 16384 kilobytes (SP = 4000h). The system library follows CODE segment immediately but is still aligned on paragraph (16 bytes) boundaries.

|SEGMENT|VALUE|DISTANCE FROM CODE SEGMENT|
|-------|-----|--------------------------|
|PSP    |075E | 16 paragraphs above code |
|CODE   |076E |                          |
|SYSTEM |0770 | 2 paragraphs below code  |
|STACK  |07F4 | 134 paragraphs below code|

## Source code
```
076E:0000 9A00007007    CALL	0770:0000                          
076E:0005 55            PUSH	BP                                 
076E:0006 89E5          MOV	BP,SP                              
076E:0008 31C0          XOR	AX,AX                              
076E:000A 9ACD027007    CALL	0770:02CD                          
076E:000F 5D            POP	BP                                 
076E:0010 31C0          XOR	AX,AX                              
076E:0012 9A16017007    CALL	0770:0116                          
```
