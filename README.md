# A Deep Dive into the Turbo Pascal Compiled Code

## Table of Contents

- [Files in this repository](docs/FILES.md)

- [System Data](docs/DATA.md)

- [Main Program](docs/MAIN.md)

- **System Library**

  - [0000 Initialize System Library](docs/0000-INIT.md)

  - [0088 Initialize Input](docs/0088-INIT-INPUT.md)

  - [009C Initialize Output](docs/009C-INIT-OUTPUT.md)

  - [00B1 Test for 8086/80186/80286](docs/00B1-TEST86.md)

  - [00D6 Heap error exit routine](docs/00D6-HEAP-ERROR.md)

  - [00DB Critical Error Handler](docs/00DB-CRITICAL-ERROR.md)

  - [0104 Overlay manager not installed error](docs/0104-OVERMAN.md)

  - [010C INT 00h Handler](docs/010C-INT00H.md)

  - [018B Unknown](docs/018B-UNKNOWN.md)

  - [019D Restore interrupt vectors](docs/019D-RESTORE-INT.md)

  - [01C0 Print Runtime error](docs/01C0-PRINT-RUNTIME-ERR.md)
 
  - [01E9 DOS Exit](docs/01E9-DOS-EXIT.md)

  - [01F0 Print string](docs/01F0-PRINT-STRING.md)

  - [01FE Convert number to ASCII](docs/01FE-CONVERT-NUMBER.md)

  - [0218 Print digits](docs/0218-PRINT-DIGITS.md)

  - [0232 Print character](docs/0232-PRINT-CHAR.md)

  - [0239 Interrupt List](docs/0239-INTERRUPT-LIST.md)

  - [024C Runtime error strings](docs/024C-STRING-RUNTIME-ERR.md)

  - [0263 Copyright string (invisible)](docs/0263-DATA-COPYRIGHT.md)

  - [02C1 Range check error](docs/02C1-RANGE.md)

  - [02C7 Arithmetic overflow error](docs/02C7-ARITHMETIC-OVERFLOW.md)

  - [02CD Check stack](docs/02CD-CHECK-STACK.md)

  - [02E6 Unknown](docs/02E6-UNKNOWN.md)

  - [0364 Unknown](docs/0364-UNKNOWN.md)
  
  - [03BE Unknown](docs/03BE-UNKNOWN.md)

  - [0580 Clear input/output buffers and interrupt vector array](docs/0580-CLEAR.md)

- [Runtime Error Codes](docs/ERROR-CODES.md)