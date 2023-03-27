# 8087/80287/80387 Control Word

|Bit(s)|Name|Description          |
|------|----|---------------------|
|15-12 |    |Reserved             |
|11-10 |RC  |Rounding Control     |
|09-08 |PC  |Precision Control    |
|07-06 |    |Reserved             |
|05    |PM  |Precision            |
|04    |UM  |Underflow            |
|03    |OM  |Overflow             |
|02    |ZM  |Zero Divide          |
|01    |DM  |Denormalized Operand |
|00    |IM  |Invalid Operation    |

## Rounding Control (Bits 10-11)

|Bit 11|Bit 10|Function       |
| :--: | :--: |---------------|
|  0   |  0   |To nearest even|
|  0   |  1   |Round down     |
|  1   |  0   |Round up       |
|  1   |  1   |Truncate       |

## Precision Control (Bits 08-09)

|Bit 09|Bit 08|Precision      |
| :--: | :--: |---------------|
|  0   |  0   |24 bits        |
|  0   |  1   |Reserved       |
|  1   |  0   |53 bits        |
|  1   |  1   |64 bits        |

See also: [8087/80287/80387 Status Word](STATUS8087.md) or go [back](../../README.md)


