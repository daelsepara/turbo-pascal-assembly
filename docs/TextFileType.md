# Text File Type

This is a type of file or record used internally by Turbo Pascal. It is around 256 bytes in size.

## Structure of Text Type

|Address|Size (bytes) |Name     |Type      |Description                                                                     |
| :---: | :---------: | :-----: | :------: |--------------------------------------------------------------------------------|
|0000   |2            |Handle   |WORD      |Handle contains the file's handle (when the file is open) as returned by **DOS**|
|0002   |2            |Mode     |WORD      |The Mode field can assume one of the *magic* values (see **Modes** below)       |
|0004   |2            |BufSize  |WORD      |Size of the *TextBuf* character buffer                                          |
|0006   |2            |Private  |WORD      |Unused but reserved                                                             | 
|0008   |2            |BufPos   |WORD      |Index of the next character in the buffer to read or write                      |
|000A   |2            |BufEnd   |WORD      |Count of characters in the buffer                                               |
|000C   |4            |BufPtr   |FAR PTR   |Pointer to *TextBuf* buffer of *BufSize* bytes                                  |
|0010   |4            |OpenFunc |FAR PTR   |Pointer to I/O routine that opens this file                                     |
|0014   |4            |InOutFunc|FAR PTR   |Pointer to I/O routine that performs read/write on this file                    |
|0018   |4            |FlushFunc|FAR PTR   |Pointer to I/O routine that flushes the buffer                                  |
|001C   |4            |CloseFunc|FAR PTR   |Pointer to I/O routine that closes this file                                    |
|0020   |16           |UserData |BYTE array|Not used by Turbo Pascal. can be freely used to store any data                  |
|0030   |80           |Name     |BYTE array|This file's name which is a NULL (00h) terminated string                        |
|0080   |128          |TextBuf  |BYTE array|Character buffer                                                                |

Total size: 256 (100h) bytes

# Modes

|Name        |Value (Hex) |Description                                                                           |
| :--------: | :--------: |--------------------------------------------------------------------------------------|
|fmClosed    | D7B0       | The file is closed                                                                   |
|fmInput     | D7B1       | The file is a *text file* that has been reset                                        |
|fmInput     | D7B1       | The file is a *text file* that has been rewritten                                    |
|fmInOut     | D7B3       | The file variable is a typed or an untyped file that has been reset or rewritten     |
|*Undefined* | ????       | The file variable hasn't been assigned (and thereby not initialized)                 |

See also: [Input/Output](DATA.md), or Go [Back](../README.md)