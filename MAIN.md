# Main Program

## Registers

When the executable **MAIN.EXE** is loaded in memory via **DEBUG.EXE** command, the following registers were assigned these values:

```
AX=FFFF  BX=0000  CX=0B64  DX=0000  SP=4000  BP=0000  SI=0000  DI=0000
DS=075E  ES=075E  SS=07F4  CS=076E  IP=0000   NV UP EI PL NZ NA PO NC
```

The absolute values for DS, ES, SS, and CS itself are not important. What is relevant are the distances from one another (see below).

CX is initialized to the file size minus the header (512 bytes):

```
CX = 3428 - 512 = 2916 (in Decimal)
CX = 0D64 - 200 = 0B64 (in Hexadecimal)
```

Stack size is around 16384 kilobytes (SP = 4000h).

As to why AX = FFFFh, it is still a mystery to me as of now and will be explored later.

## Segments

When the program is loaded in memory, ES AND DS points to the Program Segment Prefix (PSP). The system library follows CODE segment immediately but is still aligned on paragraph (16 bytes) boundaries.

|Segment|Value|Distance from CODE segment|Distance calculation |size / hex / align / paragraphs + start = next|
|-------|-----|--------------------------|---------------------|----------------------------------------------|
|PSP    | 075E| 016 paragraphs above CODE|076E - 0010 = 075E   | 00256 => 0100 => 0100 => 010 + 75E = 76E     |
|CODE   | 076E|                          |                     | 00023 => 0017 => 0020 => 002 + 76E = 770     | 
|SYSTEM | 0770| 002 paragraphs below CODE|076E + 0002 = 0770   | 01426 => 0592 => 05A0 => 05A + 770 = 7CA     |
|DATA   | 07CA| 092 paragraphs below CODE|076E + 005C = 07CA   | 00668 => 029C => 02A0 => 02A + 7CA = 7F4     |
|STACK  | 07F4| 134 paragraphs below CODE|076E + 0086 = 07F4   | 16384 => 4000 => 4000 => 400 + 7F4 = ???     |

Where **Distance from CODE segment** is calculated using:

```
(segment) - (code segment) = distance in paragraphs (1 paragraph = 16 bytes = 10h in hex)
```

Other calculations based on actual sizes of these segments as well as the rounded-off values are shown in the the last column of the table above. The rounded values are due to the assembler putting these segments in paragraph alignments. The location of the HEAP is calculated during the system library initialization.

Refer to [MAP](src/MAIN.MAP) to see where these values are obtained.

## Source code

```
CODE:0000 9A00007007    CALL	SYS:0000
```

Initialize System Library: [SYS:0000](0000-INIT.md)

```
CODE:0005 55            PUSH	BP
CODE:0006 89E5          MOV	BP,SP
```

Probably like other compiled high-level languages, Turbo Pascal has a lot of stack manipulation. This sequence of statements PUSH BP/MOV BP, SP is like a defining signature of compiled code. It's likely that the main program is a callable function and receives parameters (e.g. argc, argv) on the stack right next to the return code. BP is used to address these parameters. It can be observed in Turbo Pascal, the compiler takes special care of BP and SP while playing fast and loose with the other registers.

```
CODE:0008 31C0          XOR	AX,AX
CODE:000A 9ACD027007    CALL	SYS:02CD
```

Checks the size of the stack: [SYS:02CD](02CD-CHECK-STACK.md). Exits with an error message if stack size is above the limit.


```
CODE:000F 5D            POP	BP
```

This balances the stack from PUSH BP/MOV BP, SP above. Again, this seems to be a defining feature of compiled code.

```
CODE:0010 31C0          XOR	AX,AX
CODE:0012 9A16017007    CALL	SYS:0116
```

Call Exit Function. AX contains the exit code (AL = 0, no error)

[Back](README.md)