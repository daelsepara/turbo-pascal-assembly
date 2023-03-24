# SYSTEM DATA

## Overlay Manager Internal Variables

|Address| Variable    | Description                                                               |
|-------|-------------|---------------------------------------------------------------------------|
| 0002  | OvrCodeList | Overlay code segment list: 0 = no overlays                                |
| 0004  | OvrHeapSize | Minimum overlay heap size in paragraphs (16 bytes)                        |
| 0006  | OvrDebugPtr | Overlay debugger hook used by Turbo Pascal Debugger                       |
| 000A  | OvrHeapOrg  | Overlay buffer origin (segment address of the start of the overlay buffer)|
| 000C  | OvrHeapPtr  | Overlay buffer pointer (Used internally)                                  |
| 000E  | OvrHeapEnd  | Stores the segment address of the end of the overlay buffer               |
| 0010  | OvrLoadList | Loaded overlays list                                                      |
| 0012  | OvrDosHandle| Overlay file handle. Stores the file handle of the program's overlay file |
| 0014  | OvrEmsHandle| Overlay EMS handle. Handle of overlays in expanded memory block           |

## Heap Internal Variables

|Address| Variable    | Description                                     |
|-------|-------------|-------------------------------------------------|
| 0016  | HeapOrg     | Points to the bottom of the heap                |
| 001A  | HeapPtr     | Points to the top of the heap                   | 
| 001E  | HeapEnd     | Points to the end of DOS memory used by programs|
| 0022  | FreeList    | Points to the first free block in the heap      |
| 0026  | FreeZero    | ???                                             | 
| 002A  | HeapError   | Pointer to Heap Error Handler                   |

# Exit/Error Internal Variables

|Address| Variable    | Description                                                   |
|-------|-------------|---------------------------------------------------------------|
| 002E  | ExitProc    | Pointer to application's exit procedure list                  |
| 0032  | ExitCode    | Contains the application's exit code                          |
| 0034  | ErrorAddr   | Contains the address of the statement causing a run-time error|

## Program Segment Prefix

|Address|   Variable  |
|-------|-------------|
| 0038  | PrefixSeg   |

Stores PSP segment address setup by DOS upon loading the program.

## Constants and Variables

|Address| Variable    |
|-------|-------------|
| 003A  | StackLimit  |

This is the lowest value the SP register can contain before it is considered a stack overflow.

## I/O Function Result

|Address| Variable    |
|-------|-------------|
| 003C  | InOutRes    |

Stores the status of the last 1/a operation performed.

## Miscellaneous Constants and Variables

|Address|   Variable  | Description                                                |
|-------|-------------|------------------------------------------------------------|
| 003E  | RandSeed    | Stores the built-in random number generator's seed         |
| 0042  | SelectorInc | Number to add/subtract to increase/decrease selector by 64K|
| 0044  | Seg0040     | Selector for segment 0040 (ROM BIOS Workspace)             |
| 0046  | SegA000     | Selector for segment A000 (EGA and VGA Graphics Pages)     |
| 0048  | SegB000     | Selector for segment B000 (Monochrome Adapter video memory)|
| 004A  | SegB800     | Selector for segment B800 (CGA Adapter video memory)       |


## 8086 CPU Type

|Address| Variable    |
|-------|-------------|
| 004C  | Test8086    |

Type of 80x86 processor the system contains:
- 0 Processor is an 8086
- 1 Processor is an 80286
- 2 Processor is an 80386 or later

## 8087 fCPU Type

|Address| Variable    |
|-------|-------------|
| 004D  | Test8087    |

Stores the results of the 80x87 autodetection logic and coprocessor classification:
- 0 No coprocessor detected
- 1 8087 detected
- 2 80287 detected
- 3 80387 or later detected

## Constantas and Variables

|Address| Variable    |
|-------|-------------|
| 004E  | FileMode    |

Access code to pass to DOS when typed and untyped files are opened using the **Reset** procedure.

## Input/Output Text File Records

|Address|   Variable  |
|-------|-------------|
| 0050  | Input       |
| 0150  | Output      |

Standard **Input** and **Output** files setup by Turbo Pascal. Each **Text file record** is around 256 bytes in size.

See: [Text File Type](TEXT-FILE-TYPE.md)

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

Stores original interrupt vectors for INT 00h/02h/1Bh/21h/23h-24h/34h-3Fh/75h. Each can contain a FAR address (4 bytes).

[Back](../README.md)