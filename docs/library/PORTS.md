# Ports

## Ports used by the **System Library**.

| Port | Function / Purpose                                                             |
| :--: |--------------------------------------------------------------------------------|
| 0020 | Programmable Interrupt Controller 8259 Initialization Command Word 1 (**ICW1**)|
| 00A0 | NMI mask register (same as port 0020)                                          |
| 00F0 | Math coprocessor clear busy latch                                              |


## Initialization Command Word 1 (Port 20/A0)

|Bit|Set|Function when set       |Unset|Function when clear         |
|:-:|:-:|------------------------| :-: |----------------------------|
| 0 | 1 |ICW4 is needed          |  0  |No ICW4 needed              |
| 1 | 1 |Single 8259             |  0  |Cascading 8259's            |
| 2 | 1 |4 byte interrupt vectors|  0  |8 byte interrupt vectors    |
| 3 | 1 |Level triggered mode    |  0  |Edge triggered mode         |
| 4 | 1 |Must be set for **ICW1**|  0  |                            |
| 5 | 1 |                        |  0  |Must be clear for PC Systems|

See also: [Identify 8087/80287/80387 FPU](TEST8087.md) Go [back](../../README.md)