# 0263 0263 Empty string and Hidden Copyright string

```
SYS:0263           00 50 6F 72 74-69 6F 6E 73 20 43 6F 70      .Portions Cop
SYS:0270  79 72 69 67 68 74 20 28-63 29 20 31 39 38 33 2C   yright (c) 1983,
SYS:0280  39 32 20 42 6F 72 6C 61-6E 64                     92 Borland
```

This NULL (00h) byte character at **SYS:0263** is a Pascal "**empty string**". Pascal strings are byte arrays where the first byte is the length. This **empty string** is used in the initialization of **[Input](0088-INIT-INPUT.md)** and **[Output](009C-INIT-OUTPUT.md)**.

The other bytes **SYS:0264-0289** that looks like a copyright string is not used is never visibile.

See also: [SYS:0088 Initialize Input](0088-INIT-INPUT.md), [SYS:009C Initialize Output](009C-INIT-OUTPUT.md), or Go [Back](../README.md)