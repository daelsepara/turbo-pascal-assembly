# A Deep Dive into the Turbo Pascal Compiled Code

## Table of Contents

- [Files in this repository](FILES.md)

- [System Data](DATA.md)

- [Main Program](MAIN.md)

- **System Library**

  - [0000 Initialize System Library](0000-INIT.md)

  - [0088 Initialize Input](0088-INIT-INPUT.md)

  - [009C Initialize Output](009C-INIT-OUTPUT.md)

  - [00B1 Test for 8086/80186/80286](00B1-TEST86.md)

  - [00D6 Heap error exit routine](00D6-HEAP-ERROR.md)

  - [00DB Critical Error Handler](00DB-CRITICAL-ERROR.md)

  - [0104 Overlay manager not installed error](0104-OVERMAN.md)

  - [010C INT 00h Handler](010C-INT00H.md)

  - [019D Restore interrupt vectors](019D-RESTORE-INT.md)

  - [01F0 Print string](01F0-PRINT-STRING.md)

  - [01FE Convert number to ASCII](01FE-CONVERT-NUMBER.md)

  - [0218 Print digits](0218-PRINT-DIGITS.md)

  - [0232 Print character](0232-PRINT-CHAR.md)

  - [0239 Interrupt List](0239-INTERRUPT-LIST.md)

  - [02C1 Range check error](02C1-RANGE.md)

  - [02C7 Arithmetic overflow error](02C7-ARITHMETIC-OVERFLOW.md)

  - [02CD Check stack](02CD-CHECK-STACK.md)

  - [02E6 Unknown](02E6-UNKNOWN.md)

  - [0364 Unknown](0364-UNKNOWN.md)
  
  - [03BE Unknown](03BE-UNKNOWN.md)

  - [0580 Clear input/output buffers and interrupt vector array](0580-CLEAR.md)

- [Runtime Error Codes](ERROR-CODES.md)