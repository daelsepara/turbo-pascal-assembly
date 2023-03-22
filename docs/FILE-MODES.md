# File Modes

| Mode      | Value (Hex) |Description                                                                           |
| :-------: | :---------: |--------------------------------------------------------------------------------------|
|fmClosed   | D7B0        | The file is closed                                                                   |
|fmInput    | D7B1        | The file is a *text file* that has been reset                                        |
|fmOutput   | D7B2        | The file is a *text file* that has been rewritten                                    |
|fmInOut    | D7B3        | The file variable is a typed or an untyped file that has been reset or rewritten     |
|*Undefined*| ????        | The file variable hasn't been assigned (and thereby not initialized)                 |

See also: [Input/Output](DATA.md), [Text File Type](TEXT-FILE-TYPE.md), or Go [Back](../README.md)