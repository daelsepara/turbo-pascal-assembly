# Print Real: WriteLn(*Real*)

Prints a real number onto the display output.

This is a disassembly of the Pascal Program:
```
program main;
        var A: Real;
        begin
                A := 0.3141592654E01;
                WriteLn(A);
        end.
end program;
```

It was compiled with x87 emulation and x87 code disabled using:

```
TPC.EXE -GS -GP -GD -$D+ -$E- -$N- MAIN
```

## Main Program

```
A := 0.3141592654E01;
WriteLn(A);
```

Translates to the following:

```
CODE:000F C70652008292  MOV	WORD PTR [0052],9282
CODE:0015 C7065400A2DA  MOV	WORD PTR [0054],DAA2
CODE:001B C70656000F49  MOV	WORD PTR [0056],490F
```

The *Real* variable **A** is stored in **DATA**:[0052] and occupies 6 bytes. **490FDAA29282h** is the 54-bit representation of **0.3141592654E01** (PI ~ 9 digits after the decimal) (see: [***Real* Type**](REAL-TYPE.md)).

```
CODE:0021 BF5801        MOV	DI,Output
CODE:0024 1E            PUSH	DS
CODE:0025 57            PUSH	DI
CODE:0026 FF365600      PUSH	[0056]
CODE:002A FF365400      PUSH	[0054]
CODE:002E FF365200      PUSH	[0052]
CODE:0032 B81100        MOV	AX,0011
CODE:0035 50            PUSH	AX
CODE:0036 B8FFFF        MOV	AX,FFFF
CODE:0039 50            PUSH	AX
```

These parameters are pased onto the stack for the successive calls to the system library. Notice that the higher bytes of the number (**A**) are passed first before the lower bytes. **AX** = **0011h/17** characters are used to represent the number (1 for the sign, 4 for the exponent, and 12 for the digits including the decimal point). Another parameter **AX** = **FFFFh** is pushed to the stack. This is the maximum limit.

## Stack after CODE:0039

|Index|Contents|Description        |
|-----| :----: |-------------------|
|SP   |  FFFF  |Constant Parameter |
|SP+02|  1100  |Constant Parameter |
|SP+04|  8292  |Low Word of **A**  |
|SP+06|  A2DA  |Mid Word of **A**  |
|SP+08|  0F49  |High Word of **A** |
|SP+0A|  ????  |DI Output (Offset) |
|SP+0C|  ????  |DS Output (Segment)|

Note that the byte order has been reversed (least significant byte **LSB** first, followed by the most significant byte **MSB**).

```
CODE:003A 9A93067607    CALL	SYS:0693
CODE:003F 9ADD057607    CALL	SYS:05DD
CODE:0044 9A91027607    CALL	SYS:0291
```

These three calls are used to print the *Real* number. We shall walkthrough each of these calls.

```
SYS:0291 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS:0296 7501          JNZ	0299
SYS:0298 CB            RETF
SYS:0299 A13C00        MOV	AX,[InOutRes]
SYS:029C E970FE        JMP	010F
```

**SYS:0291** checks whether the printing of the output generated any error. If so, it prints a Runtime error then exits.

```
SYS:04F7 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS:04FC 7540          JNZ	053E
SYS:04FE 26            ES:
SYS:04FF 817F02B2D7    CMP	WORD PTR [BX+02],D7B2
SYS:0504 7539          JNZ	053F
SYS:0506 26            ES:
SYS:0507 8B4F04        MOV	CX,[BX+04]
SYS:050A 26            ES:
SYS:050B 8B7F08        MOV	DI,[BX+08]
SYS:050E 2BCF          SUB	CX,DI
SYS:0510 2BD1          SUB	DX,CX
SYS:0512 7304          JNB	0518
SYS:0514 03CA          ADD	CX,DX
SYS:0516 33D2          XOR	DX,DX
SYS:0518 06            PUSH	ES
SYS:0519 26            ES:
SYS:051A C4770C        LES	SI,[BX+0C]
SYS:051D 03FE          ADD	DI,SI
SYS:051F B020          MOV	AL,20
SYS:0521 FC            CLD
SYS:0522 F3            REPZ
SYS:0523 AA            STOSB
SYS:0524 2BFE          SUB	DI,SI
SYS:0526 07            POP	ES
SYS:0527 26            ES:
SYS:0528 897F08        MOV	[BX+08],DI
SYS:052B 26            ES:
SYS:052C 3B7F04        CMP	DI,[BX+04]
SYS:052F 7509          JNZ	053A
SYS:0531 52            PUSH	DX
SYS:0532 06            PUSH	ES
SYS:0533 53            PUSH	BX
SYS:0534 E8E200        CALL	0619
SYS:0537 5B            POP	BX
SYS:0538 07            POP	ES
SYS:0539 5A            POP	DX
SYS:053A 0BD2          OR	DX,DX
SYS:053C 75C8          JNZ	0506
SYS:053E C3            RET
```

```
SYS:053F C7063C006900  MOV	WORD PTR [InOutRes],0069
SYS:0545 C3            RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

```
SYS:0546 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS:054B 7548          JNZ	0595
```

Check if last operation had any errors.

```
SYS:054D 26            ES:
SYS:054E 817F02B2D7    CMP	WORD PTR [BX+02],D7B2
SYS:0553 7541          JNZ	0596
```

Check if file/device has been is open for writing. 

```
SYS:0555 26            ES:
SYS:0556 8B4F04        MOV	CX,[BX+04]
SYS:0559 26            ES:
SYS:055A 8B7F08        MOV	DI,[BX+08]
SYS:055D 2BCF          SUB	CX,DI
SYS:055F 2BC1          SUB	AX,CX
SYS:0561 7304          JNB	0567
SYS:0563 03C8          ADD	CX,AX
SYS:0565 33C0          XOR	AX,AX
SYS:0567 1E            PUSH	DS
SYS:0568 06            PUSH	ES
SYS:0569 53            PUSH	BX
SYS:056A 8EDA          MOV	DS,DX
SYS:056C 26            ES:
SYS:056D C45F0C        LES	BX,[BX+0C]
SYS:0570 03FB          ADD	DI,BX
SYS:0572 FC            CLD
SYS:0573 F3            REPZ
SYS:0574 A4            MOVSB
SYS:0575 2BFB          SUB	DI,BX
SYS:0577 5B            POP	BX
SYS:0578 07            POP	ES
SYS:0579 1F            POP	DS
SYS:057A 26            ES:
SYS:057B 897F08        MOV	[BX+08],DI
SYS:057E 26            ES:
SYS:057F 3B7F04        CMP	DI,[BX+04]
SYS:0582 750D          JNZ	0591
SYS:0584 50            PUSH	AX
SYS:0585 52            PUSH	DX
SYS:0586 56            PUSH	SI
SYS:0587 06            PUSH	ES
SYS:0588 53            PUSH	BX
SYS:0589 E88D00        CALL	0619
SYS:058C 5B            POP	BX
SYS:058D 07            POP	ES
SYS:058E 5E            POP	SI
SYS:058F 5A            POP	DX
SYS:0590 58            POP	AX
SYS:0591 0BC0          OR	AX,AX
SYS:0593 75C0          JNZ	0555
SYS:0595 C3            RET
```

```
SYS:0596 C7063C006900  MOV	WORD PTR [InOutRes],0069
SYS:059C C3            RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

```
SYS:0619 06            PUSH	ES
SYS:061A 53            PUSH	BX
SYS:061B 26            ES:
SYS:061C FF5F14        CALL	FAR [BX:InOutFunc]
SYS:061F 0BC0          OR	AX,AX
SYS:0621 7403          JZ	0626
SYS:0623 A33C00        MOV	[InOutRes],AX
SYS:0626 C3            RET
```

## Convert to ASCII routine

```
SYS:0693 55            PUSH	BP
SYS:0694 8BEC          MOV	BP,SP
SYS:0696 83EC40        SUB	SP,+40
```

Save **BP** and use it as index to the stack. Reserve **40h/64** bytes on the stack.

## Stack after SYS:0696

|Index|Contents                 |
| :-: |-------------------------|
|BP-40|(SP Points here)         |
|BP+00|Old BP                   |
|BP+02|Return Address (Offset)  |
|BP+04|Return Address (Segment) |
|BP+06|**Precision** (FFFFh)   |
|BP+08|**Width** (0011h)        |
|BP+0A|Low Word of *Real*       |
|BP+0C|Mid Word of *Real*       |
|BP+0E|High Word of *Real*      |
|BP+10|Output buffer (Offset)   |
|BP+12|Output buffer (Segment)  |

**Precision** and **Width** sets the desired precision. if **Precision** is a positive number, it will determine the number of digits after the decimal point. 

```
SYS:0699 8B460A        MOV	AX,[BP+0A]
SYS:069C 8B5E0C        MOV	BX,[BP+0C]
SYS:069F 8B560E        MOV	DX,[BP+0E]
SYS:06A2 8B4E06        MOV	CX,[BP+06]
```

Loads *Real* number passed on stack to **DX**:**BX**:**AX**. Where **DX** = **High** word, **BX** = **Mid** word, and **AX** = **Low** word. Load parameter into **CX** ([**BP+06**] = **FFFFh/-1**).

```
SYS:06A5 0BC9          OR	CX,CX
SYS:06A7 790E          JNS	06B7
```

Check if **CX** is unsigned or a negative number.

```
SYS:06A9 B90600        MOV	CX,0006
SYS:06AC 2B4E08        SUB	CX,[BP+08]
SYS:06AF 83F9FE        CMP	CX,-02
SYS:06B2 7E03          JLE	06B7
```

Check if number of characters representation is enough.

```
SYS:06B4 B9FEFF        MOV	CX,FFFE
```

If not enough, then set default of width 2 digits (**FFFEh**/**-2**).

```
SYS:06B7 8D7EC0        LEA	DI,[BP-40]
SYS:06BA 16            PUSH	SS
SYS:06BB 07            POP	ES
```

Point **ES**:**DI** to reserved **40h**/**64** bytes on the stack.

```
SYS:06BC E8FE04        CALL	0BBD
```

Call **SYS:0BBD**.

```
SYS:06BF C45E10        LES	BX,[BP+10]
```

Load output buffer address in [**BP+10**] into **ES**:**BX**.

```
SYS:06C2 8B5608        MOV	DX,[BP+08]
SYS:06C5 2BD1          SUB	DX,CX
SYS:06C7 7E05          JLE	06CE
SYS:06C9 51            PUSH	CX
SYS:06CA E82AFE        CALL	04F7
SYS:06CD 59            POP	CX
SYS:06CE 8BC1          MOV	AX,CX
SYS:06D0 8D76C0        LEA	SI,[BP-40]
SYS:06D3 8CD2          MOV	DX,SS
SYS:06D5 E86EFE        CALL	0546
SYS:06D8 8BE5          MOV	SP,BP
SYS:06DA 5D            POP	BP
SYS:06DB CA0A00        RETF	000A
```

## Digit extaction logic

|Registers|Description                  |Sample value  |              
|---------|-----------------------------|--------------|
|DI:SI:CX |Number read from **SYS:0E7A**|0000 0000 0081|
|DX:BX:AX |Number to be converted       |HHHH MMMM LLLL|

```
SYS:0889 E9FC00        JMP	0988
SYS:088C 0AC0          OR	AL,AL
SYS:088E 74F9          JZ	0889
```

Check if **exponent** is **00h** and exit if there is none.

```
SYS:0890 0AC9          OR	CL,CL
SYS:0892 74F5          JZ	0889
```

Check if **CL** is also empty.

```
SYS:0894 55            PUSH	BP
SYS:0895 8BEA          MOV	BP,DX
SYS:0897 33D7          XOR	DX,DI
SYS:0899 81E20080      AND	DX,8000
SYS:089D 86D0          XCHG	DL,AL
SYS:089F 02D1          ADD	DL,CL
SYS:08A1 12F0          ADC	DH,AL
SYS:08A3 8AC8          MOV	CL,AL
SYS:08A5 81CD0080      OR	BP,8000
SYS:08A9 81CF0080      OR	DI,8000
SYS:08AD 52            PUSH	DX
SYS:08AE 0AE4          OR	AH,AH
SYS:08B0 7504          JNZ	08B6
SYS:08B2 0BDB          OR	BX,BX
SYS:08B4 740D          JZ	08C3
SYS:08B6 0AED          OR	CH,CH
SYS:08B8 7526          JNZ	08E0
SYS:08BA 0BF6          OR	SI,SI
SYS:08BC 7522          JNZ	08E0
SYS:08BE 91            XCHG	CX,AX
SYS:08BF 87DE          XCHG	BX,SI
SYS:08C1 87EF          XCHG	BP,DI
SYS:08C3 8BC1          MOV	AX,CX
SYS:08C5 F7E5          MUL	BP
SYS:08C7 8BDA          MOV	BX,DX
SYS:08C9 8BC6          MOV	AX,SI
SYS:08CB F7E5          MUL	BP
SYS:08CD 03D8          ADD	BX,AX
SYS:08CF 83D200        ADC	DX,+00
SYS:08D2 8BCA          MOV	CX,DX
SYS:08D4 8BC7          MOV	AX,DI
SYS:08D6 F7E5          MUL	BP
SYS:08D8 03C1          ADD	AX,CX
SYS:08DA 83D200        ADC	DX,+00
SYS:08DD EB7C          JMP	095B
```

```
SYS:08DF 90            NOP
```

```
SYS:08E0 57            PUSH	DI
SYS:08E1 56            PUSH	SI
SYS:08E2 51            PUSH	CX
SYS:08E3 55            PUSH	BP
SYS:08E4 53            PUSH	BX
SYS:08E5 50            PUSH	AX
SYS:08E6 8BEC          MOV	BP,SP
SYS:08E8 33C9          XOR	CX,CX
SYS:08EA 8A4601        MOV	AL,[BP+01]
SYS:08ED F66607        MUL	BYTE PTR [BP+07]
SYS:08F0 8BF0          MOV	SI,AX
SYS:08F2 8BF9          MOV	DI,CX
SYS:08F4 8BD9          MOV	BX,CX
SYS:08F6 8B4600        MOV	AX,[BP+00]
SYS:08F9 F76608        MUL	WORD PTR [BP+08]
SYS:08FC 03F0          ADD	SI,AX
SYS:08FE 13FA          ADC	DI,DX
SYS:0900 13D9          ADC	BX,CX
SYS:0902 8B4602        MOV	AX,[BP+02]
SYS:0905 F76606        MUL	WORD PTR [BP+06]
SYS:0908 03F0          ADD	SI,AX
SYS:090A 13FA          ADC	DI,DX
SYS:090C 13D9          ADC	BX,CX
SYS:090E 8BF1          MOV	SI,CX
SYS:0910 8B4600        MOV	AX,[BP+00]
SYS:0913 F7660A        MUL	WORD PTR [BP+0A]
SYS:0916 03F8          ADD	DI,AX
SYS:0918 13DA          ADC	BX,DX
SYS:091A 13F1          ADC	SI,CX
SYS:091C 8B4602        MOV	AX,[BP+02]
SYS:091F F76608        MUL	WORD PTR [BP+08]
SYS:0922 03F8          ADD	DI,AX
SYS:0924 13DA          ADC	BX,DX
SYS:0926 13F1          ADC	SI,CX
SYS:0928 8B4604        MOV	AX,[BP+04]
SYS:092B F76606        MUL	WORD PTR [BP+06]
SYS:092E 03F8          ADD	DI,AX
SYS:0930 13DA          ADC	BX,DX
SYS:0932 13F1          ADC	SI,CX
SYS:0934 8BF9          MOV	DI,CX
SYS:0936 8B4602        MOV	AX,[BP+02]
SYS:0939 F7660A        MUL	WORD PTR [BP+0A]
SYS:093C 03D8          ADD	BX,AX
SYS:093E 13F2          ADC	SI,DX
SYS:0940 13F9          ADC	DI,CX
SYS:0942 8B4604        MOV	AX,[BP+04]
SYS:0945 F76608        MUL	WORD PTR [BP+08]
SYS:0948 03D8          ADD	BX,AX
SYS:094A 13F2          ADC	SI,DX
SYS:094C 13F9          ADC	DI,CX
SYS:094E 8B4604        MOV	AX,[BP+04]
SYS:0951 F7660A        MUL	WORD PTR [BP+0A]
SYS:0954 03C6          ADD	AX,SI
SYS:0956 13D7          ADC	DX,DI
SYS:0958 83C40C        ADD	SP,+0C
```

```
SYS:095B 93            XCHG	BX,AX
SYS:095C 59            POP	CX
SYS:095D 5D            POP	BP
SYS:095E 0AF6          OR	DH,DH
SYS:0960 7807          JS	0969
SYS:0962 D1E0          SHL	AX,1
SYS:0964 D1D3          RCL	BX,1
SYS:0966 D1D2          RCL	DX,1
SYS:0968 49            DEC	CX
SYS:0969 81E98180      SUB	CX,8081
SYS:096D 058000        ADD	AX,0080
SYS:0970 83D300        ADC	BX,+00
SYS:0973 83D200        ADC	DX,+00
SYS:0976 7303          JNB	097B
SYS:0978 D1DA          RCR	DX,1
SYS:097A 41            INC	CX
SYS:097B F6C540        TEST	CH,40
SYS:097E 7508          JNZ	0988
SYS:0980 41            INC	CX
SYS:0981 8AC1          MOV	AL,CL
SYS:0983 32F5          XOR	DH,CH
SYS:0985 D0ED          SHR	CH,1
SYS:0987 C3            RET
SYS:0988 33C0          XOR	AX,AX
SYS:098A 8BD8          MOV	BX,AX
SYS:098C 8BD0          MOV	DX,AX
SYS:098E C3            RET
```

```
SYS:098F 0AC0          OR	AL,AL
SYS:0991 74F5          JZ	0988
SYS:0993 55            PUSH	BP
SYS:0994 8BEA          MOV	BP,DX
SYS:0996 33D7          XOR	DX,DI
SYS:0998 81CF0080      OR	DI,8000
SYS:099C 81CD0080      OR	BP,8000
SYS:09A0 81E20080      AND	DX,8000
SYS:09A4 86C2          XCHG	AL,DL
SYS:09A6 2AD1          SUB	DL,CL
SYS:09A8 1AF0          SBB	DH,AL
SYS:09AA 52            PUSH	DX
SYS:09AB B002          MOV	AL,02
SYS:09AD BA0100        MOV	DX,0001
SYS:09B0 3BEF          CMP	BP,DI
SYS:09B2 7506          JNZ	09BA
SYS:09B4 3BDE          CMP	BX,SI
SYS:09B6 7502          JNZ	09BA
SYS:09B8 3AE5          CMP	AH,CH
SYS:09BA 7206          JB	09C2
SYS:09BC 2AE5          SUB	AH,CH
SYS:09BE 1BDE          SBB	BX,SI
SYS:09C0 1BEF          SBB	BP,DI
SYS:09C2 D1D2          RCL	DX,1
SYS:09C4 7211          JB	09D7
SYS:09C6 D0E4          SHL	AH,1
SYS:09C8 D1D3          RCL	BX,1
SYS:09CA D1D5          RCL	BP,1
SYS:09CC 73E2          JNB	09B0
SYS:09CE 2AE5          SUB	AH,CH
SYS:09D0 1BDE          SBB	BX,SI
SYS:09D2 1BEF          SBB	BP,DI
SYS:09D4 F8            CLC
SYS:09D5 EBEB          JMP	09C2
SYS:09D7 FEC8          DEC	AL
SYS:09D9 780A          JS	09E5
SYS:09DB 52            PUSH	DX
SYS:09DC BA0100        MOV	DX,0001
SYS:09DF 75E5          JNZ	09C6
SYS:09E1 B240          MOV	DL,40
SYS:09E3 EBE1          JMP	09C6
SYS:09E5 8BC2          MOV	AX,DX
SYS:09E7 B106          MOV	CL,06
SYS:09E9 D3E0          SHL	AX,CL
SYS:09EB 5B            POP	BX
SYS:09EC 5A            POP	DX
SYS:09ED 59            POP	CX
SYS:09EE 5D            POP	BP
SYS:09EF F7D0          NOT	AX
SYS:09F1 F7D3          NOT	BX
SYS:09F3 83F2FF        XOR	DX,-01
SYS:09F6 7807          JS	09FF
SYS:09F8 D1D0          RCL	AX,1
SYS:09FA D1D3          RCL	BX,1
SYS:09FC D1D2          RCL	DX,1
SYS:09FE 49            DEC	CX
SYS:09FF 81C18080      ADD	CX,8080
SYS:0A03 E967FF        JMP	096D
```

```
SYS:0BBD 55            PUSH	BP
SYS:0BBE 8BEC          MOV	BP,SP
SYS:0BC0 83EC14        SUB	SP,+14
```

At this point **DX**:**BX**:**AX** contains the *Real* number. Save BP then reserve **14h/20** bytes on the stack.

```
SYS:0BC3 57            PUSH	DI
```

Save **DI**. At this point **ES**:**DI** contains the pointer temporary output buffer.

Stack after **SYS**:**0BC3**

|Index|Contents                             |
|-----|-------------------------------------|
|BP-16|Saved DI (SP points here)            |
|BP-14|(Temporary buffer)                   |
|BP-06|(number of digits converted)         |
|BP-04|(MSB)                                |
|BP-02|(Precision)                          |
|BP   |Saved BP                             |


```
SYS:0BC4 83F90B        CMP	CX,+0B
SYS:0BC7 7E03          JLE	0BCC
SYS:0BC9 B90B00        MOV	CX,000B
SYS:0BCC 83F9F5        CMP	CX,-0B
SYS:0BCF 7D03          JGE	0BD4
```

Check if CX is within the limit: **-0Bh** <= **CX** <= **+0Bh**.

```
SYS:0BD1 B9F5FF        MOV	CX,FFF5
```

Otherwise, set Default **CX** = **FFF5h**/**-11**.

```
SYS:0BD4 894EFE        MOV	[BP-02],CX
```

Store **CX** into **SS**:[**BP-02**].

```
SYS:0BD7 8876FC        MOV	[BP-04],DH
```

Store the *Real* number's **MSB** into **SS**:[**BP-04**].

```
SYS:0BDA 06            PUSH	ES
SYS:0BDB 57            PUSH	DI
```

Preserve **ES**:**DI**.

```
SYS:0BDC 8D7EEC        LEA	DI,[BP-14]
SYS:0BDF 16            PUSH	SS
SYS:0BE0 07            POP	ES
```

Load pointer to reserved area (**SS**:**BP-14**) into **ES**:**DI**.

```
SYS:0BE1 E8D100        CALL	0CB5
```

```
SYS:0BE4 5F            POP	DI
SYS:0BE5 07            POP	ES
```

Restore **ES**:**DI**.

## Start of conversion logic

```
SYS:0BE6 894EFA        MOV	[BP-06],CX
SYS:0BE9 8B76FE        MOV	SI,[BP-02]
SYS:0BEC 0BF6          OR	SI,SI
SYS:0BEE 780C          JS	0BFC
SYS:0BF0 0376FA        ADD	SI,[BP-06]
SYS:0BF3 46            INC	SI
SYS:0BF4 7908          JNS	0BFE
```

```
SYS:0BF6 C646EC00      MOV	BYTE PTR [BP-14],00
SYS:0BFA EB2E          JMP	0C2A
```

Initialize output buffer by marking the first byte with a **NULL**/**00h** byte.

```
SYS:0BFC F7DE          NEG	SI
```

Negate **SI** to make it positive.

## Truncation logic

```
SYS:0BFE 83FE0C        CMP	SI,+0C
SYS:0C01 7203          JB	0C06
SYS:0C03 BE0B00        MOV	SI,000B
```

Limit to **0Ch/11** digits.

## Round-off logic

```
SYS:0C06 807AEC35      CMP	BYTE PTR [BP+SI-14],35
SYS:0C0A C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS:0C0E 721A          JB	0C2A
SYS:0C10 4E            DEC	SI
SYS:0C11 780F          JS	0C22
SYS:0C13 FE42EC        INC	BYTE PTR [BP+SI-14]
SYS:0C16 807AEC39      CMP	BYTE PTR [BP+SI-14],39
SYS:0C1A 760E          JBE	0C2A
SYS:0C1C C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS:0C20 EBEE          JMP	0C10
```

```
SYS:0C22 C746EC3100    MOV	WORD PTR [BP-14],0031
```

Put **NULL** (**00h**)-terminated string '**1**' into start of the buffer.

```
SYS:0C27 FF46FA        INC	WORD PTR [BP-06]
```

Increase number of digits to convert.

```
SYS:0C2A 33F6          XOR	SI,SI
```

Reset **SI**.

```
SYS:0C2C FC            CLD
SYS:0C2D 8B56FE        MOV	DX,[BP-02]
SYS:0C30 0BD2          OR	DX,DX
SYS:0C32 7835          JS	0C69
SYS:0C34 F646FC80      TEST	BYTE PTR [BP-04],80
SYS:0C38 7403          JZ	0C3D
SYS:0C3A B02D          MOV	AL,2D
SYS:0C3C AA            STOSB
SYS:0C3D 8B4EFA        MOV	CX,[BP-06]
SYS:0C40 0BC9          OR	CX,CX
SYS:0C42 7905          JNS	0C49
SYS:0C44 B030          MOV	AL,30
SYS:0C46 AA            STOSB
SYS:0C47 EB07          JMP	0C50
```

```
SYS:0C49 E85D00        CALL	0CA9
SYS:0C4C AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```
SYS:0C4D 49            DEC	CX
SYS:0C4E 79F9          JNS	0C49
SYS:0C50 0BD2          OR	DX,DX
SYS:0C52 744C          JZ	0CA0
SYS:0C54 B02E          MOV	AL,2E
SYS:0C56 AA            STOSB
SYS:0C57 41            INC	CX
SYS:0C58 7406          JZ	0C60
SYS:0C5A B030          MOV	AL,30
SYS:0C5C AA            STOSB
SYS:0C5D 4A            DEC	DX
SYS:0C5E 75F7          JNZ	0C57
SYS:0C60 4A            DEC	DX
SYS:0C61 783D          JS	0CA0
```

```
SYS:0C63 E84300        CALL	0CA9
SYS:0C66 AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```
SYS:0C67 EBF7          JMP	0C60
```

## Format number to scientific notation.

```
SYS:0C69 B020          MOV	AL,20
SYS:0C6B F646FC80      TEST	BYTE PTR [BP-04],80
SYS:0C6F 7402          JZ	0C73
```

Prepare ' ' (whitespace) character in **AL**. Check if number is negative.

```
SYS:0C71 B02D          MOV	AL,2D
```

Prepare '**-**' character if number is negative.

```
SYS:0C73 AA            STOSB
```

Store character in **AL** to **ES**:[**DI**].

## Convert first digit to ASCII

```
SYS:0C74 E83200        CALL	0CA9
SYS:0C77 AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```
SYS:0C78 42            INC	DX
```

Add to count.

```
SYS:0C79 740A          JZ	0C85
```

Check if there are mo more digits to convert.

```
SYS:0C7B B02E          MOV	AL,2E
SYS:0C7D AA            STOSB
```

Store '**.**' into the buffer at **ES**:[**DI**].

## Convert significand to ASCII

```
SYS:0C7E E82800        CALL	0CA9
SYS:0C81 AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```
SYS:0C82 42            INC	DX
SYS:0C83 75F9          JNZ	0C7E
```

Cycle until all of the **significand** **f**'s digits are stored in output buffer.

## Convert Exponent to ASCII and format in scientific notation

```
SYS:0C85 B045          MOV	AL,45
SYS:0C87 AA            STOSB
```

Store '**E**' into the buffer at **ES**:[**DI**] (**scientific notation**).

```
SYS:0C88 B02B          MOV	AL,2B
```

Prepare '**+**' character if number is exponent is positive.

```
SYS:0C8A 8B56FA        MOV	DX,[BP-06]
SYS:0C8D 0BD2          OR	DX,DX
SYS:0C8F 7904          JNS	0C95
```

Check if exponent is negative (**SF** = **1**, **signed**).

```
SYS:0C91 B02D          MOV	AL,2D
SYS:0C93 F7DA          NEG	DX
```

Prepare '**-**' character since exponent is negative. Negate **DX** to make the exponent positive.

```
SYS:0C95 AA            STOSB
```

Store **exponent** sign into the buffer at **ES**:[**DI**].

```
SYS:0C96 8BC2          MOV	AX,DX
```

Store exponent in **AX**.

```
SYS:0C98 B20A          MOV	DL,0A
SYS:0C9A F6FA          IDIV	DL
```

Convert exponent in **AL** to base 10 (**DL** = **0Ah**) by doing a signed division of **AL** by **DL**. Quotient in **AL**, remainder in **AH**.

```
SYS:0C9C 053030        ADD	AX,3030
SYS:0C9F AB            STOSW
```

Convert exponent digits in **AX** to **ASCII** by adding '**00**' to **AX**. Store the converted digits into **ES**:[**DI**].

## Conversion complete

The conversion is now complete.

```
SYS:0CA0 8BCF          MOV	CX,DI
SYS:0CA2 5F            POP	DI
SYS:0CA3 2BCF          SUB	CX,DI
```

Compute actual number of digits converted in **CX**.

```
SYS:0CA5 8BE5          MOV	SP,BP
SYS:0CA7 5D            POP	BP
SYS:0CA8 C3            RET
```

Remove temporary reserved space by restoring **SP** (using **BP**) then pop off **BP** before returning.

## Load one character from source buffer

```
SYS:0CA9 8A42EC        MOV	AL,[BP+SI-14]
SYS:0CAC 46            INC	SI
```

Copy byte character into **AL** then move pointer (**SI**) forward by one byte.

```
SYS:0CAD 0AC0          OR	AL,AL
SYS:0CAF 7503          JNZ	0CB4
```

Check if empty.

```
SYS:0CB1 B030          MOV	AL,30
SYS:0CB3 4E            DEC	SI
```

Pad with '**0**' if AL is **NULL** then move pointer (**SI**) back by one byte.

```
SYS:0CB4 C3            RET
```

Return.

## Convert ***Real*** number to ASCII.

```
SYS:0CB5 0AC0          OR	AL,AL
SYS:0CB7 750D          JNZ	0CC6
```

At this point **AX** contains the exponent (in **AL**) and **LSB** of the number in **AH**. Check if exponent non-zero.

```
SYS:0CB9 B90600        MOV	CX,0006
SYS:0CBC B83030        MOV	AX,3030
SYS:0CBF FC            CLD
SYS:0CC0 F3            REPZ
SYS:0CC1 AB            STOSW
SYS:0CC2 32C0          XOR	AL,AL
SYS:0CC4 AA            STOSB
SYS:0CC5 C3            RET
```

If exponent is zero, store **'000000000000'** into the buffer then **NULL/00h** terminate it.

```
SYS:0CC6 80E67F        AND	DH,7F
```

Clear the sign bit in **DH** (Bit 7).

```
SYS:0CC9 50            PUSH	AX
```

Preserve **AX** as it is frequently modified by the arithmetic operations.

```
SYS:0CCA 2C80          SUB	AL,80
```

Remove bias from exponent (**80h**)

```
SYS:0CCC B44D          MOV	AH,4D
SYS:0CCE F6EC          IMUL	AH
```

Multiply un-biased exponent in **AL** with **4Dh/77** and store result in **AX**.

```
SYS:0CD0 050500        ADD	AX,0005
SYS:0CD3 8AC4          MOV	AL,AH
SYS:0CD5 98            CBW
```

Add **5** to **AX** then move high byte to lower byte then convert to word.

```
SYS:0CD6 8BC8          MOV	CX,AX
```

Copy result into **CX**.

```
SYS:0CD8 58            POP	AX
```

Restore **AX**.

```
SYS:0CD9 83F9D9        CMP	CX,-27
SYS:0CDC 7501          JNZ	0CDF
```

Check if **CX** = **FFD9h**/**-27h**/**-39**.

```
SYS:0CDE 41            INC	CX
```

If equal, increment by 1.

```
SYS:0CDF 51            PUSH	CX
SYS:0CE0 57            PUSH	DI
```

Save **CX**, and **DI**.

```
SYS:0CE1 F7D9          NEG	CX
```

Negate **CX**.

```
SYS:0CE3 E84401        CALL	0E2A
```

Check if **CX** is within range/limits on call to **SYS**:**0E2A**.

```
SYS:0CE6 5F            POP	DI
SYS:0CE7 59            POP	CX
```

Restore **CX**, and **DI** saved in **SYS:0CDF** and **SYS:0CE0**.

```
SYS:0CE8 3C81          CMP	AL,81
SYS:0CEA 7304          JNB	0CF0
```

Check exponent in **AL**.

```
SYS:0CEC E8C701        CALL	0EB6
```

```
SYS:0CEF 49            DEC	CX
SYS:0CF0 51            PUSH	CX
SYS:0CF1 80CE80        OR	DH,80
SYS:0CF4 B184          MOV	CL,84
SYS:0CF6 2AC8          SUB	CL,AL
SYS:0CF8 B000          MOV	AL,00
SYS:0CFA 740A          JZ	0D06
SYS:0CFC D1EA          SHR	DX,1
SYS:0CFE D1DB          RCR	BX,1
SYS:0D00 D1D8          RCR	AX,1
SYS:0D02 FEC9          DEC	CL
SYS:0D04 75F6          JNZ	0CFC
```

```
SYS:0D06 BE0C00        MOV	SI,000C
```

Convert 12 digits (**SI = 000C**).

```
SYS:0D09 8AEE          MOV	CH,DH
SYS:0D0B B104          MOV	CL,04
SYS:0D0D D2ED          SHR	CH,CL
```

Copy digit to convert to ASCII in **DH** to **CH**. Only the upper digit (bits 4-7) are needed.

```
SYS:0D0F 80C530        ADD	CH,30
SYS:0D12 26            ES:
SYS:0D13 882D          MOV	[DI],CH
```

Convert digit in **CH** to ASCII by adding it '**0**' (**30h**/**48**) and storing in **ES**:[**DI**].

```
SYS:0D15 80E60F        AND	DH,0F
```

Clear upper bits (4-7) in **DH**.

```
SYS:0D18 52            PUSH	DX
SYS:0D19 53            PUSH	BX
SYS:0D1A 50            PUSH	AX
SYS:0D1B D1E0          SHL	AX,1
SYS:0D1D D1D3          RCL	BX,1
SYS:0D1F D1D2          RCL	DX,1
SYS:0D21 D1E0          SHL	AX,1
SYS:0D23 D1D3          RCL	BX,1
SYS:0D25 D1D2          RCL	DX,1
SYS:0D27 59            POP	CX
SYS:0D28 03C1          ADD	AX,CX
SYS:0D2A 59            POP	CX
SYS:0D2B 13D9          ADC	BX,CX
SYS:0D2D 59            POP	CX
SYS:0D2E 13D1          ADC	DX,CX
SYS:0D30 D1E0          SHL	AX,1
SYS:0D32 D1D3          RCL	BX,1
SYS:0D34 D1D2          RCL	DX,1
SYS:0D36 47            INC	DI
SYS:0D37 4E            DEC	SI
SYS:0D38 75CF          JNZ	0D09
SYS:0D3A 26            ES:
SYS:0D3B C60500        MOV	BYTE PTR [DI],00
SYS:0D3E 59            POP	CX
SYS:0D3F C3            RET
```

## SYS:0E2A Range/Limit checks

```
SYS:0E2A 80F9DA        CMP	CL,DA
SYS:0E2D 7C49          JL	0E78
```

Check if **CX** is less than **DAh**/**-38**.

```
SYS:0E2F 80F926        CMP	CL,26
SYS:0E32 7F44          JG	0E78
```

Check if **CX** is greater than **26h**/**38**.

```
SYS:0E34 52            PUSH	DX
SYS:0E35 53            PUSH	BX
SYS:0E36 50            PUSH	AX
```

Save number **DX**:**BX**:**AX**.

```
SYS:0E37 0AC9          OR	CL,CL
SYS:0E39 9C            PUSHF
```

Check if signed, then save results (Flags).

```
SYS:0E3A 7902          JNS	0E3E
```

Check if signed.

```
SYS:0E3C F6D9          NEG	CL
```

If signed, then negate.

```
SYS:0E3E 8AD9          MOV	BL,CL
SYS:0E40 80E3FC        AND	BL,FC
```

Copy lower byte into BL then clear Bits 0 and 1.

```
SYS:0E43 8AFB          MOV	BH,BL
SYS:0E45 D0EB          SHR	BL,1
```

Copy lower byte into BH. Then shift right by 1 bit.

```
SYS:0E47 02DF          ADD	BL,BH
SYS:0E49 32FF          XOR	BH,BH
```

Add to BL then clear BH.

```
SYS:0E4B 8DBF7A0E      LEA	DI,[BX+0E7A]
```

Set lookup index to [**BX+0E7A**] (see **SYS**:**0E7A** below).

```
SYS:0E4F 2E            CS:
SYS:0E50 8B05          MOV	AX,[DI]
SYS:0E52 2E            CS:
SYS:0E53 8B5D02        MOV	BX,[DI+02]
SYS:0E56 2E            CS:
SYS:0E57 8B5504        MOV	DX,[DI+04]
```

Copy numbers to **DX**:**BX**:**AX**.

```
SYS:0E5A 80E103        AND	CL,03
```

Clear bits 2-7 in **CL**.

```
SYS:0E5D 7407          JZ	0E66
```

Check if **CL** is cleared.

```
SYS:0E5F E85400        CALL	0EB6
SYS:0E62 FEC9          DEC	CL
SYS:0E64 75F9          JNZ	0E5F
```

```
SYS:0E66 8BC8          MOV	CX,AX
SYS:0E68 8BF3          MOV	SI,BX
SYS:0E6A 8BFA          MOV	DI,DX
```

Copy **DX**:**BX**:**AX** to **DI**:**SI**:**CX**.

```
SYS:0E6C 9D            POPF
```

Restore Flags.

```
SYS:0E6D 58            POP	AX
SYS:0E6E 5B            POP	BX
SYS:0E6F 5A            POP	DX
```

Restore **DX**:**BX**:**AX**.

```
SYS:0E70 7803          JS	0E75
SYS:0E72 E917FA        JMP	088C
SYS:0E75 E917FB        JMP	098F
```

Check if signed or unsigned then handle appropriately.

```
SYS:0E78 F9            STC
SYS:0E79 C3            RET
```

Return with carry flag set.

```
SYS:0E7A  81 00 00 00 00 00
```

```
SYS:0EB6 0AC0          OR	AL,AL
SYS:0EB8 7449          JZ	0F03
```

Check if **exponent** = **00h**.

## Preliminary conversion

```
SYS:0EBA 51            PUSH	CX
SYS:0EBB 56            PUSH	SI
SYS:0EBC 80CE80        OR	DH,80
SYS:0EBF 8AC8          MOV	CL,AL
SYS:0EC1 32C0          XOR	AL,AL
SYS:0EC3 52            PUSH	DX
SYS:0EC4 53            PUSH	BX
SYS:0EC5 50            PUSH	AX
SYS:0EC6 D1EA          SHR	DX,1
SYS:0EC8 D1DB          RCR	BX,1
SYS:0ECA D1D8          RCR	AX,1
SYS:0ECC D1EA          SHR	DX,1
SYS:0ECE D1DB          RCR	BX,1
SYS:0ED0 D1D8          RCR	AX,1
SYS:0ED2 5E            POP	SI
SYS:0ED3 03C6          ADD	AX,SI
SYS:0ED5 5E            POP	SI
SYS:0ED6 13DE          ADC	BX,SI
SYS:0ED8 5E            POP	SI
SYS:0ED9 13D6          ADC	DX,SI
SYS:0EDB 730B          JNB	0EE8
SYS:0EDD D1DA          RCR	DX,1
SYS:0EDF D1DB          RCR	BX,1
SYS:0EE1 D1D8          RCR	AX,1
SYS:0EE3 80C101        ADD	CL,01
SYS:0EE6 7219          JB	0F01
SYS:0EE8 058000        ADD	AX,0080
SYS:0EEB 83D300        ADC	BX,+00
SYS:0EEE 83D200        ADC	DX,+00
SYS:0EF1 7307          JNB	0EFA
SYS:0EF3 D1DA          RCR	DX,1
SYS:0EF5 80C101        ADD	CL,01
SYS:0EF8 7207          JB	0F01
SYS:0EFA 80E67F        AND	DH,7F
SYS:0EFD 8AC1          MOV	AL,CL
SYS:0EFF 0403          ADD	AL,03
SYS:0F01 5E            POP	SI
SYS:0F02 59            POP	CX
```

```
SYS:0F03 C3            RET
```

Return.

Go [Back](../../README.md)