# 024C Runtime error strings

```
SYS:024C                                      52 75 6E 74               Runt
SYS:0250  69 6D 65 20 65 72 72 6F-72 20 00 20 61 74 20 00   ime error . at .
SYS:0260  2E 0D 0A 00                                       ...
```

This is the string that is used to print the error code. It contains three parts:
- **SYS:024C**: It contains the *Runtime error* string including the trailing whitespace chracter (20h).
- **SYS:025B**: It contains the *at* string which has a single whitespace character on both ends.
- **SYS:0260**: It is the **.** (period) character followed by newline characters (**0Dh**, **0Ah**).
- **SYS:0263**: It functions as a **NULL**-terminator to the new line string at **SYS:0261** and the **[empty string](0263-DATA-COPYRIGHT.md)** assigned to the files **[Input](TEXT-FILE-TYPE.md)** and **[Output](TEXT-FILE-TYPE.md)**.

[Go back](../README.md)