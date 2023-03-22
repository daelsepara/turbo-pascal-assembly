# 0263 Copyright string (Invisible)

```
SYS:0263           00 50 6F 72 74-69 6F 6E 73 20 43 6F 70      .Portions Cop
SYS:0270  79 72 69 67 68 74 20 28-63 29 20 31 39 38 33 2C   yright (c) 1983,
SYS:0280  39 32 20 42 6F 72 6C 61-6E 64                     92 Borland
```

This "**empty string**" is used in the initalizations for [Input](0088-INIT-INPUT.md) and [Output](009C-INIT-OUTPUT.md). It is never visibile as it starts on a NULL (00h). The byte at **SYS:0263** is also used as the string length.

See also: [SYS:0088 Initialize Input](0088-INIT-INPUT.md), [SYS:0088 Initialize Output](009C-INIT-OUTPUT.md), or Go [Back](../README.md)