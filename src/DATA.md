# SYSTEM DATA

## TBD

|Address|   Variable  |
|-------|-------------|
| 0002  | OvrCodeList |
| 0004  | OvrHeapSize |
| 0006  | OvrDebugPtr |
| 000A  | OvrHeapOrg  |
| 000C  | OvrHeapPtr  |
| 000E  | OvrHeapEnd  |
| 0010  | OvrLoadList |
| 0012  | OvrDosHandle|
| 0014  | OvrEmsHandle|
| 0016  | HeapOrg     |
| 001A  | HeapPtr     |
| 001E  | HeapEnd     |
| 0022  | FreeList    |
| 0026  | FreeZero    |
| 002A  | HeapError   |
| 002E  | ExitProc    |
| 0032  | ExitCode    |
| 0034  | ErrorAddr   |

## Program Segment Prefix

|Address|   Variable  |
| 0038  | PrefixSeg   |

Stores PSP segment address setup by DOS upon loading the program.

## TBD

|Address|   Variable  |
| 003A  | StackLimit  |
| 003C  | InOutRes    |
| 003E  | RandSeed    |
| 0042  | SelectorInc |
| 0044  | Seg0040     |
| 0046  | SegA000     |
| 0048  | SegB000     |
| 004A  | SegB800     |
| 004C  | Test8086    |
| 004D  | Test8087    |
| 004E  | FileMode    |

## Input/Output buffers

|Address|   Variable  |
|-------|-------------|
| 0050  | Input       |
| 0150  | Output      |

Each buffer is arround 256 bytes in size.

## Interrupt Vectors

|Address|   Variable  |
|-------|-------------|
| 0250  | SaveInt00   |
| 0254  | SaveInt02   |
| 0258  | SaveInt1B   |
| 025C  | SaveInt21   |
| 0260  | SaveInt23   |
| 0264  | SaveInt24   |
| 0268  | SaveInt34   |
| 026C  | SaveInt35   |
| 0270  | SaveInt36   |
| 0274  | SaveInt37   |
| 0278  | SaveInt38   |
| 027C  | SaveInt39   |
| 0280  | SaveInt3A   |
| 0284  | SaveInt3B   |
| 0288  | SaveInt3C   |
| 028C  | SaveInt3D   |
| 0290  | SaveInt3E   |
| 0294  | SaveInt3F   |
| 0298  | SaveInt75   |

Stores original interrupt vectors for INT 00h/02h/1Bh/21h/23h-24h/34h-39h/75h. Each can contain a FAR address (4 bytes).

[Back](README.md)