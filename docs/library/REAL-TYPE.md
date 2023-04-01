# Real Type - Floating Point Number

Represents the number:

+/- **significand** * 2^**exponent**

*Real* is a 6-byte (48-bit) representation of a number *v*

|Bit(s)|Field|Description                             |
| :--: | :-: |----------------------------------------|
|  47  |**s**|Sign, **0** = positive, **1** = negative|
| 8-46 |**f**|Signficand = 1.**f**                    |
| 0-7  |**e**|Exponent, 127 <= **e** <= -128          |

The value **v** of the number is determined by the following:

If **0** < **e** <= **255**, then **v** = (**-1**)^**s** * **2**^(**e** - **129**) * (**l**.**f**).

If **e** = **0**, then **v** = **0**.

Go [Back](../../README.md)