# Print Real: WriteLn(*Real*)

Prints a real number onto the display output.

This is a disassembly of the Pascal Program:
```pascal
program main;
        var A: Real;
        begin
                A := 0.3141592654E01;
                WriteLn(A);
        end.
end program;
```

It was compiled with x87 emulation and x87 code disabled using:

```cmd
TPC.EXE -GS -GP -GD -$D+ -$E- -$N- MAIN
```

## Main Program
```pascal
A := 0.3141592654E01;
```

Translates to the following:

```nasm
CODE000F: C70652008292  MOV	WORD PTR [A.Low],9282
CODE0015: C7065400A2DA  MOV	WORD PTR [A.Mid],DAA2
CODE001B: C70656000F49  MOV	WORD PTR [A.High],490F
```

The *Real* variable **A** occupies 6 bytes. **490FDAA29282h** is the 48-bit representation of **0.3141592654E01** (PI ~ 9 digits after the decimal) (see: [***Real* Type**](REAL-TYPE.md)).

```nasm
CODE0021: BF5801        MOV	DI,Output
CODE0024: 1E            PUSH	DS
CODE0025: 57            PUSH	DI
CODE0026: FF365600      PUSH	[A.High]
CODE002A: FF365400      PUSH	[A.Mid]
CODE002E: FF365200      PUSH	[A.Low]
CODE0032: B81100        MOV	AX,0011
CODE0035: 50            PUSH	AX
CODE0036: B8FFFF        MOV	AX,FFFF
CODE0039: 50            PUSH	AX
```

These parameters are pased onto the stack for the successive calls to the system library. Notice that the higher bytes of the number (**A**) are passed first before the lower bytes. **AX** = **0011h/17** characters are used to represent the number (1 for the sign, 4 for the exponent, and 12 for the digits including the decimal point). Another parameter **AX** = **FFFFh** is pushed to the stack. 

## Stack after CODE:0039

|Index|Contents|Description        |
|-----| :----: |-------------------|
|SP   |  FFFF  |Constant Parameter |
|SP+02|  0011  |Constant Parameter |
|SP+04|  9282  |Low Word of **A**  |
|SP+06|  DAA2  |Mid Word of **A**  |
|SP+08|  490F  |High Word of **A** |
|SP+0A|  ????  |DI Output (Offset) |
|SP+0C|  ????  |DS Output (Segment)|

Note that the byte order is actually stored reversed (least significant byte **LSB** first, followed by the most significant byte **MSB**) in memory.

```nasm
CODE003A: 9A93067607    CALL	SYS:0693
CODE003F: 9ADD057607    CALL	SYS:05DD
CODE0044: 9A91027607    CALL	SYS:0291
```

These three calls are used to print the *Real* number. We shall walkthrough each of these calls.

## I/O Result Check (I)

```nasm
SYS0291: 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS0296: 7501          JNZ	0299
SYS0298: CB            RETF
SYS0299: A13C00        MOV	AX,[InOutRes]
SYS029C: E970FE        JMP	010F
```

**SYS:0291** checks whether the printing of the output generated any error. If so, it prints a Runtime error then exits on the jump to **SYS**:**010F**.

## I/O Result check (II)

```nasm
SYS04F7: 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS04FC: 7540          JNZ	053E
```

Check if the last operation generated any error. Exit immediately if there was an error.

```nasm
SYS04FE: 26            ES:
SYS04FF: 817F02B2D7    CMP	WORD PTR [BX+02],fmOutput
SYS0504: 7539          JNZ	053F
```

Verify that **Output** was opened for output. Generate an error code if not then exit.

```nasm
SYS0506: 26            ES:
SYS0507: 8B4F04        MOV	CX,[BX+04]
SYS050A: 26            ES:
SYS050B: 8B7F08        MOV	DI,[BX+08]
SYS050E: 2BCF          SUB	CX,DI
SYS0510: 2BD1          SUB	DX,CX
SYS0512: 7304          JNB	0518
```

Check if buffer has enough room.

```nasm
SYS0514: 03CA          ADD	CX,DX
SYS0516: 33D2          XOR	DX,DX
```

Make adjustments.

```nasm
SYS0518: 06            PUSH	ES
```

Save **ES**.

```nasm
SYS0519: 26            ES:
SYS051A: C4770C        LES	SI,[BX+0C]
SYS051D: 03FE          ADD	DI,SI
```

Get pointer to buffer into **ES**:**DI** (adjusted using **SI**).

```nasm
SYS051F: B020          MOV	AL,20
SYS0521: FC            CLD
SYS0522: F3            REPZ
SYS0523: AA            STOSB
```

Clear buffer (fill with whitespace (' ') = **20h**/**32**).

```nasm
SYS0524: 2BFE          SUB	DI,SI
```

Adjust pointer to reflect the number of characters to copy.

```nasm
SYS0526: 07            POP	ES
```

Restore **ES**.

```nasm
SYS0527: 26            ES:
SYS0528: 897F08        MOV	[BX+08],DI
```

Set **Output**'s **[BufPos](../TEXT-FILE-TYPE.md)**.

```nasm
SYS052B: 26            ES:
SYS052C: 3B7F04        CMP	DI,[BX+04]
SYS052F: 7509          JNZ	053A
SYS0531: 52            PUSH	DX
SYS0532: 06            PUSH	ES
SYS0533: 53            PUSH	BX
SYS0534: E8E200        CALL	0619
SYS0537: 5B            POP	BX
SYS0538: 07            POP	ES
SYS0539: 5A            POP	DX
SYS053A: 0BD2          OR	DX,DX
SYS053C: 75C8          JNZ	0506
SYS053E: C3            RET
```

```nasm
SYS053F: C7063C006900  MOV	WORD PTR [InOutRes],0069
SYS0545: C3            RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

```nasm
SYS0546: 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS054B: 7548          JNZ	0595
```

Check if last operation had any errors.

```nasm
SYS054D: 26            ES:
SYS054E: 817F02B2D7    CMP	WORD PTR [BX+02],fmOutput
SYS0553: 7541          JNZ	0596
```

Check if file/device has been is open for writing. 

```nasm
SYS0555: 26            ES:
SYS0556: 8B4F04        MOV	CX,[BX+04]
SYS0559: 26            ES:
SYS055A: 8B7F08        MOV	DI,[BX+08]
SYS055D: 2BCF          SUB	CX,DI
SYS055F: 2BC1          SUB	AX,CX
SYS0561: 7304          JNB	0567
SYS0563: 03C8          ADD	CX,AX
SYS0565: 33C0          XOR	AX,AX
SYS0567: 1E            PUSH	DS
SYS0568: 06            PUSH	ES
SYS0569: 53            PUSH	BX
SYS056A: 8EDA          MOV	DS,DX
SYS056C: 26            ES:
SYS056D: C45F0C        LES	BX,[BX+0C]
SYS0570: 03FB          ADD	DI,BX
SYS0572: FC            CLD
SYS0573: F3            REPZ
SYS0574: A4            MOVSB
SYS0575: 2BFB          SUB	DI,BX
SYS0577: 5B            POP	BX
SYS0578: 07            POP	ES
SYS0579: 1F            POP	DS
SYS057A: 26            ES:
SYS057B: 897F08        MOV	[BX+08],DI
SYS057E: 26            ES:
SYS057F: 3B7F04        CMP	DI,[BX+04]
SYS0582: 750D          JNZ	0591
SYS0584: 50            PUSH	AX
SYS0585: 52            PUSH	DX
SYS0586: 56            PUSH	SI
SYS0587: 06            PUSH	ES
SYS0588: 53            PUSH	BX
SYS0589: E88D00        CALL	0619
SYS058C: 5B            POP	BX
SYS058D: 07            POP	ES
SYS058E: 5E            POP	SI
SYS058F: 5A            POP	DX
SYS0590: 58            POP	AX
SYS0591: 0BC0          OR	AX,AX
SYS0593: 75C0          JNZ	0555
SYS0595: C3            RET
```

```nasm
SYS0596: C7063C006900  MOV	WORD PTR [InOutRes],0069
SYS059C: C3            RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

## Call to Output I/O

```nasm
SYS0619: 06            PUSH	ES
SYS061A: 53            PUSH	BX
```

Save registers **ES**:**BX**.

```nasm
SYS061B: 26            ES:
SYS061C: FF5F14        CALL	FAR [BX:InOutFunc]
```

Call **Output**'s I/O handler.

```nasm
SYS061F: 0BC0          OR	AX,AX
SYS0621: 7403          JZ	0626
```

Check if the last operation generated any errors.

```nasm
SYS0623: A33C00        MOV	[InOutRes],AX
```

Record the result of the last operation.

```nasm
SYS0626: C3            RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

## Convert to ASCII routine

```nasm
SYS0693: 55            PUSH	BP
SYS0694: 8BEC          MOV	BP,SP
SYS0696: 83EC40        SUB	SP,+40
```

Save **BP** and use it as index to the stack. Reserve **40h/64** bytes on the stack.

## Stack after SYS:0696

|Index|Contents                 |
| :-: |-------------------------|
|BP-40|(SP Points here)         |
|BP+00|Old BP                   |
|BP+02|Return Address (Offset)  |
|BP+04|Return Address (Segment) |
|BP+06|**Precision** (FFFFh)    |
|BP+08|**Width** (0011h)        |
|BP+0A|Low Word of *Real*       |
|BP+0C|Mid Word of *Real*       |
|BP+0E|High Word of *Real*      |
|BP+10|Output buffer (Offset)   |
|BP+12|Output buffer (Segment)  |

**Precision** and **Width** are used to set the desired precision. If **Precision** is a positive number, it will determine the number of digits after the decimal point. 

```nasm
SYS0699: 8B460A        MOV	AX,[BP+0A]
SYS069C: 8B5E0C        MOV	BX,[BP+0C]
SYS069F: 8B560E        MOV	DX,[BP+0E]
SYS06A2: 8B4E06        MOV	CX,[BP+06]
```

Loads *Real* number passed on stack to **DX**:**BX**:**AX**. Where **DX** = **High** word, **BX** = **Mid** word, and **AX** = **Low** word. Load parameter into **CX** ([**BP+06**] = **FFFFh/-1**).

```nasm
SYS06A5: 0BC9          OR	CX,CX
SYS06A7: 790E          JNS	06B7
```

Check if **CX** is unsigned or a negative number.

```nasm
SYS06A9: B90600        MOV	CX,0006
SYS06AC: 2B4E08        SUB	CX,[BP+08]
SYS06AF: 83F9FE        CMP	CX,-02
SYS06B2: 7E03          JLE	06B7
```

Check if number of characters representation is enough.

```nasm
SYS06B4: B9FEFF        MOV	CX,FFFE
```

If not enough, then set default of width 2 digits (**FFFEh**/**-2**).

```nasm
SYS06B7: 8D7EC0        LEA	DI,[BP-40]
SYS06BA: 16            PUSH	SS
SYS06BB: 07            POP	ES
```

Point **ES**:**DI** to reserved **40h**/**64** bytes on the stack.

```nasm
SYS06BC: E8FE04        CALL	0BBD
```

Call start of conversion routine in **SYS:0BBD**.

```nasm
SYS06BF: C45E10        LES	BX,[BP+10]
```

Load output buffer address in [**BP+10**] into **ES**:**BX**.

```nasm
SYS06C2: 8B5608        MOV	DX,[BP+08]
```

Load required **width** into DX.

```nasm
SYS06C5: 2BD1          SUB	DX,CX
SYS06C7: 7E05          JLE	06CE
```

Check if all digits have been converted.

```nasm
SYS06C9: 51            PUSH	CX
SYS06CA: E82AFE        CALL	04F7
```

```nasm
SYS06CD: 59            POP	CX
SYS06CE: 8BC1          MOV	AX,CX
SYS06D0: 8D76C0        LEA	SI,[BP-40]
SYS06D3: 8CD2          MOV	DX,SS
SYS06D5: E86EFE        CALL	0546
SYS06D8: 8BE5          MOV	SP,BP
SYS06DA: 5D            POP	BP
SYS06DB: CA0A00        RETF	000A
```

## Digit extaction logic

|Registers|Description                  |Sample value  |              
|---------|-----------------------------|--------------|
|DI:SI:CX |Number read from **SYS:0E7A**|0000 0000 0081|
|DX:BX:AX |Number to be converted       |HHHH MMMM LLLL|

```nasm
SYS0889: E9FC00        JMP	0988
SYS088C: 0AC0          OR	AL,AL
SYS088E: 74F9          JZ	0889
```

Check if **exponent** is **00h** and exit if there is none.

```nasm
SYS0890: 0AC9          OR	CL,CL
SYS0892: 74F5          JZ	0889
```

Check if **CL** is also empty.

```nasm
SYS0894: 55            PUSH	BP
SYS0895: 8BEA          MOV	BP,DX
SYS0897: 33D7          XOR	DX,DI
SYS0899: 81E20080      AND	DX,8000
SYS089D: 86D0          XCHG	DL,AL
SYS089F: 02D1          ADD	DL,CL
SYS08A1: 12F0          ADC	DH,AL
SYS08A3: 8AC8          MOV	CL,AL
SYS08A5: 81CD0080      OR	BP,8000
SYS08A9: 81CF0080      OR	DI,8000
SYS08AD: 52            PUSH	DX
SYS08AE: 0AE4          OR	AH,AH
SYS08B0: 7504          JNZ	08B6
SYS08B2: 0BDB          OR	BX,BX
SYS08B4: 740D          JZ	08C3
SYS08B6: 0AED          OR	CH,CH
SYS08B8: 7526          JNZ	08E0
SYS08BA: 0BF6          OR	SI,SI
SYS08BC: 7522          JNZ	08E0
SYS08BE: 91            XCHG	CX,AX
SYS08BF: 87DE          XCHG	BX,SI
SYS08C1: 87EF          XCHG	BP,DI
SYS08C3: 8BC1          MOV	AX,CX
SYS08C5: F7E5          MUL	BP
SYS08C7: 8BDA          MOV	BX,DX
SYS08C9: 8BC6          MOV	AX,SI
SYS08CB: F7E5          MUL	BP
SYS08CD: 03D8          ADD	BX,AX
SYS08CF: 83D200        ADC	DX,+00
SYS08D2: 8BCA          MOV	CX,DX
SYS08D4: 8BC7          MOV	AX,DI
SYS08D6: F7E5          MUL	BP
SYS08D8: 03C1          ADD	AX,CX
SYS08DA: 83D200        ADC	DX,+00
SYS08DD: EB7C          JMP	095B
```

```nasm
SYS08DF: 90            NOP
```

```nasm
SYS08E0: 57            PUSH	DI
SYS08E1: 56            PUSH	SI
SYS08E2: 51            PUSH	CX
SYS08E3: 55            PUSH	BP
SYS08E4: 53            PUSH	BX
SYS08E5: 50            PUSH	AX
SYS08E6: 8BEC          MOV	BP,SP
SYS08E8: 33C9          XOR	CX,CX
SYS08EA: 8A4601        MOV	AL,[BP+01]
SYS08ED: F66607        MUL	BYTE PTR [BP+07]
SYS08F0: 8BF0          MOV	SI,AX
SYS08F2: 8BF9          MOV	DI,CX
SYS08F4: 8BD9          MOV	BX,CX
SYS08F6: 8B4600        MOV	AX,[BP+00]
SYS08F9: F76608        MUL	WORD PTR [BP+08]
SYS08FC: 03F0          ADD	SI,AX
SYS08FE: 13FA          ADC	DI,DX
SYS0900: 13D9          ADC	BX,CX
SYS0902: 8B4602        MOV	AX,[BP+02]
SYS0905: F76606        MUL	WORD PTR [BP+06]
SYS0908: 03F0          ADD	SI,AX
SYS090A: 13FA          ADC	DI,DX
SYS090C: 13D9          ADC	BX,CX
SYS090E: 8BF1          MOV	SI,CX
SYS0910: 8B4600        MOV	AX,[BP+00]
SYS0913: F7660A        MUL	WORD PTR [BP+0A]
SYS0916: 03F8          ADD	DI,AX
SYS0918: 13DA          ADC	BX,DX
SYS091A: 13F1          ADC	SI,CX
SYS091C: 8B4602        MOV	AX,[BP+02]
SYS091F: F76608        MUL	WORD PTR [BP+08]
SYS0922: 03F8          ADD	DI,AX
SYS0924: 13DA          ADC	BX,DX
SYS0926: 13F1          ADC	SI,CX
SYS0928: 8B4604        MOV	AX,[BP+04]
SYS092B: F76606        MUL	WORD PTR [BP+06]
SYS092E: 03F8          ADD	DI,AX
SYS0930: 13DA          ADC	BX,DX
SYS0932: 13F1          ADC	SI,CX
SYS0934: 8BF9          MOV	DI,CX
SYS0936: 8B4602        MOV	AX,[BP+02]
SYS0939: F7660A        MUL	WORD PTR [BP+0A]
SYS093C: 03D8          ADD	BX,AX
SYS093E: 13F2          ADC	SI,DX
SYS0940: 13F9          ADC	DI,CX
SYS0942: 8B4604        MOV	AX,[BP+04]
SYS0945: F76608        MUL	WORD PTR [BP+08]
SYS0948: 03D8          ADD	BX,AX
SYS094A: 13F2          ADC	SI,DX
SYS094C: 13F9          ADC	DI,CX
SYS094E: 8B4604        MOV	AX,[BP+04]
SYS0951: F7660A        MUL	WORD PTR [BP+0A]
SYS0954: 03C6          ADD	AX,SI
SYS0956: 13D7          ADC	DX,DI
SYS0958: 83C40C        ADD	SP,+0C
```

```nasm
SYS095B: 93            XCHG	BX,AX
SYS095C: 59            POP	CX
SYS095D: 5D            POP	BP
SYS095E: 0AF6          OR	DH,DH
SYS0960: 7807          JS	0969
SYS0962: D1E0          SHL	AX,1
SYS0964: D1D3          RCL	BX,1
SYS0966: D1D2          RCL	DX,1
SYS0968: 49            DEC	CX
SYS0969: 81E98180      SUB	CX,8081
SYS096D: 058000        ADD	AX,0080
SYS0970: 83D300        ADC	BX,+00
SYS0973: 83D200        ADC	DX,+00
SYS0976: 7303          JNB	097B
SYS0978: D1DA          RCR	DX,1
SYS097A: 41            INC	CX
SYS097B: F6C540        TEST	CH,40
SYS097E: 7508          JNZ	0988
SYS0980: 41            INC	CX
SYS0981: 8AC1          MOV	AL,CL
SYS0983: 32F5          XOR	DH,CH
SYS0985: D0ED          SHR	CH,1
SYS0987: C3            RET
```

```nasm
SYS0988: 33C0          XOR	AX,AX
SYS098A: 8BD8          MOV	BX,AX
SYS098C: 8BD0          MOV	DX,AX
SYS098E: C3            RET
```

```nasm
SYS098F: 0AC0          OR	AL,AL
SYS0991: 74F5          JZ	0988
SYS0993: 55            PUSH	BP
SYS0994: 8BEA          MOV	BP,DX
SYS0996: 33D7          XOR	DX,DI
SYS0998: 81CF0080      OR	DI,8000
SYS099C: 81CD0080      OR	BP,8000
SYS09A0: 81E20080      AND	DX,8000
SYS09A4: 86C2          XCHG	AL,DL
SYS09A6: 2AD1          SUB	DL,CL
SYS09A8: 1AF0          SBB	DH,AL
SYS09AA: 52            PUSH	DX
SYS09AB: B002          MOV	AL,02
SYS09AD: BA0100        MOV	DX,0001
SYS09B0: 3BEF          CMP	BP,DI
SYS09B2: 7506          JNZ	09BA
SYS09B4: 3BDE          CMP	BX,SI
SYS09B6: 7502          JNZ	09BA
SYS09B8: 3AE5          CMP	AH,CH
SYS09BA: 7206          JB	09C2
SYS09BC: 2AE5          SUB	AH,CH
SYS09BE: 1BDE          SBB	BX,SI
SYS09C0: 1BEF          SBB	BP,DI
SYS09C2: D1D2          RCL	DX,1
SYS09C4: 7211          JB	09D7
SYS09C6: D0E4          SHL	AH,1
SYS09C8: D1D3          RCL	BX,1
SYS09CA: D1D5          RCL	BP,1
SYS09CC: 73E2          JNB	09B0
SYS09CE: 2AE5          SUB	AH,CH
SYS09D0: 1BDE          SBB	BX,SI
SYS09D2: 1BEF          SBB	BP,DI
SYS09D4: F8            CLC
SYS09D5: EBEB          JMP	09C2
SYS09D7: FEC8          DEC	AL
SYS09D9: 780A          JS	09E5
SYS09DB: 52            PUSH	DX
SYS09DC: BA0100        MOV	DX,0001
SYS09DF: 75E5          JNZ	09C6
SYS09E1: B240          MOV	DL,40
SYS09E3: EBE1          JMP	09C6
SYS09E5: 8BC2          MOV	AX,DX
SYS09E7: B106          MOV	CL,06
SYS09E9: D3E0          SHL	AX,CL
SYS09EB: 5B            POP	BX
SYS09EC: 5A            POP	DX
SYS09ED: 59            POP	CX
SYS09EE: 5D            POP	BP
SYS09EF: F7D0          NOT	AX
SYS09F1: F7D3          NOT	BX
SYS09F3: 83F2FF        XOR	DX,-01
SYS09F6: 7807          JS	09FF
SYS09F8: D1D0          RCL	AX,1
SYS09FA: D1D3          RCL	BX,1
SYS09FC: D1D2          RCL	DX,1
SYS09FE: 49            DEC	CX
SYS09FF: 81C18080      ADD	CX,8080
SYS0A03: E967FF        JMP	096D
```

```nasm
SYS0BBD: 55            PUSH	BP
SYS0BBE: 8BEC          MOV	BP,SP
SYS0BC0: 83EC14        SUB	SP,+14
```

At this point **DX**:**BX**:**AX** contains the *Real* number. Save BP then reserve **14h/20** bytes on the stack.

```nasm
SYS0BC3: 57            PUSH	DI
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


```nasm
SYS0BC4: 83F90B        CMP	CX,+0B
SYS0BC7: 7E03          JLE	0BCC
SYS0BC9: B90B00        MOV	CX,000B
SYS0BCC: 83F9F5        CMP	CX,-0B
SYS0BCF: 7D03          JGE	0BD4
```

Check if CX is within the limit: **-0Bh** <= **CX** <= **+0Bh**.

```nasm
SYS0BD1: B9F5FF        MOV	CX,FFF5
```

Otherwise, set Default **CX** = **FFF5h**/**-11**.

```nasm
SYS0BD4: 894EFE        MOV	[BP-02],CX
```

Store **CX** into **SS**:[**BP-02**].

```nasm
SYS0BD7: 8876FC        MOV	[BP-04],DH
```

Store the *Real* number's **MSB** into **SS**:[**BP-04**].

```nasm
SYS0BDA: 06            PUSH	ES
SYS0BDB: 57            PUSH	DI
```

Preserve **ES**:**DI**.

```nasm
SYS0BDC: 8D7EEC        LEA	DI,[BP-14]
SYS0BDF: 16            PUSH	SS
SYS0BE0: 07            POP	ES
```

Load pointer to reserved area (**SS**:**BP-14**) into **ES**:**DI**.

```nasm
SYS0BE1: E8D100        CALL	0CB5
```

```nasm
SYS0BE4: 5F            POP	DI
SYS0BE5: 07            POP	ES
```

Restore **ES**:**DI**.

## Start of conversion logic

```nasm
SYS0BE6: 894EFA        MOV	[BP-06],CX
SYS0BE9: 8B76FE        MOV	SI,[BP-02]
SYS0BEC: 0BF6          OR	SI,SI
SYS0BEE: 780C          JS	0BFC
SYS0BF0: 0376FA        ADD	SI,[BP-06]
SYS0BF3: 46            INC	SI
SYS0BF4: 7908          JNS	0BFE
```

```nasm
SYS0BF6: C646EC00      MOV	BYTE PTR [BP-14],00
SYS0BFA: EB2E          JMP	0C2A
```

Initialize output buffer by marking the first byte with a **NULL**/**00h** byte.

```nasm
SYS0BFC: F7DE          NEG	SI
```

Negate **SI** to make it positive.

## Truncation logic

```nasm
SYS0BFE: 83FE0C        CMP	SI,+0C
SYS0C01: 7203          JB	0C06
SYS0C03: BE0B00        MOV	SI,000B
```

Limit to **0Ch/11** digits.

## Round-off logic

```nasm
SYS0C06: 807AEC35      CMP	BYTE PTR [BP+SI-14],35
SYS0C0A: C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS0C0E: 721A          JB	0C2A
SYS0C10: 4E            DEC	SI
SYS0C11: 780F          JS	0C22
SYS0C13: FE42EC        INC	BYTE PTR [BP+SI-14]
SYS0C16: 807AEC39      CMP	BYTE PTR [BP+SI-14],39
SYS0C1A: 760E          JBE	0C2A
SYS0C1C: C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS0C20: EBEE          JMP	0C10
```

```nasm
SYS0C22: C746EC3100    MOV	WORD PTR [BP-14],0031
```

Put **NULL** (**00h**)-terminated string '**1**' into start of the buffer.

```nasm
SYS0C27: FF46FA        INC	WORD PTR [BP-06]
```

Increase number of digits to convert.

```nasm
SYS0C2A: 33F6          XOR	SI,SI
```

Reset **SI**.

```nasm
SYS0C2C: FC            CLD
SYS0C2D: 8B56FE        MOV	DX,[BP-02]
SYS0C30: 0BD2          OR	DX,DX
SYS0C32: 7835          JS	0C69
SYS0C34: F646FC80      TEST	BYTE PTR [BP-04],80
SYS0C38: 7403          JZ	0C3D
SYS0C3A: B02D          MOV	AL,2D
SYS0C3C: AA            STOSB
SYS0C3D: 8B4EFA        MOV	CX,[BP-06]
SYS0C40: 0BC9          OR	CX,CX
SYS0C42: 7905          JNS	0C49
SYS0C44: B030          MOV	AL,30
SYS0C46: AA            STOSB
SYS0C47: EB07          JMP	0C50
```

```nasm
SYS0C49: E85D00        CALL	0CA9
SYS0C4C: AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```nasm
SYS0C4D: 49            DEC	CX
SYS0C4E: 79F9          JNS	0C49
SYS0C50: 0BD2          OR	DX,DX
SYS0C52: 744C          JZ	0CA0
SYS0C54: B02E          MOV	AL,2E
SYS0C56: AA            STOSB
SYS0C57: 41            INC	CX
SYS0C58: 7406          JZ	0C60
SYS0C5A: B030          MOV	AL,30
SYS0C5C: AA            STOSB
SYS0C5D: 4A            DEC	DX
SYS0C5E: 75F7          JNZ	0C57
SYS0C60: 4A            DEC	DX
SYS0C61: 783D          JS	0CA0
```

```nasm
SYS0C63: E84300        CALL	0CA9
SYS0C66: AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```nasm
SYS0C67: EBF7          JMP	0C60
```

## Format number to scientific notation.

```nasm
SYS0C69: B020          MOV	AL,20
SYS0C6B: F646FC80      TEST	BYTE PTR [BP-04],80
SYS0C6F: 7402          JZ	0C73
```

Prepare ' ' (whitespace) character in **AL**. Check if number is negative.

```nasm
SYS0C71: B02D          MOV	AL,2D
```

Prepare '**-**' character if number is negative.

```nasm
SYS0C73: AA            STOSB
```

Store character in **AL** to **ES**:[**DI**].

## Convert first digit to ASCII

```nasm
SYS0C74: E83200        CALL	0CA9
SYS0C77: AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```nasm
SYS0C78: 42            INC	DX
```

Add to count.

```nasm
SYS0C79: 740A          JZ	0C85
```

Check if there are mo more digits to convert.

```nasm
SYS0C7B: B02E          MOV	AL,2E
SYS0C7D: AA            STOSB
```

Store '**.**' into the buffer at **ES**:[**DI**].

## Convert significand to ASCII

```nasm
SYS0C7E: E82800        CALL	0CA9
SYS0C81: AA            STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```nasm
SYS0C82: 42            INC	DX
SYS0C83: 75F9          JNZ	0C7E
```

Cycle until all of the **significand** **f**'s digits are stored in output buffer.

## Convert Exponent to ASCII

```nasm
SYS0C85: B045          MOV	AL,45
SYS0C87: AA            STOSB
```

Store '**E**' into the buffer at **ES**:[**DI**] (**scientific notation**).

```nasm
SYS0C88: B02B          MOV	AL,2B
```

Prepare '**+**' character if number is exponent is positive.

```nasm
SYS0C8A: 8B56FA        MOV	DX,[BP-06]
SYS0C8D: 0BD2          OR	DX,DX
SYS0C8F: 7904          JNS	0C95
```

Check if exponent is negative (**SF** = **1**, **signed**).

```nasm
SYS0C91: B02D          MOV	AL,2D
SYS0C93: F7DA          NEG	DX
```

Prepare '**-**' character since exponent is negative. Negate **DX** to make the exponent positive.

```nasm
SYS0C95: AA            STOSB
```

Store **exponent** sign into the buffer at **ES**:[**DI**].

```nasm
SYS0C96: 8BC2          MOV	AX,DX
```

Store exponent in **AX**.

```nasm
SYS0C98: B20A          MOV	DL,0A
SYS0C9A: F6FA          IDIV	DL
```

Convert exponent in **AL** to base 10 (**DL** = **0Ah**) by doing a signed division of **AL** by **DL**. Quotient in **AL**, remainder in **AH**.

```nasm
SYS0C9C: 053030        ADD	AX,3030
SYS0C9F: AB            STOSW
```

Convert exponent digits in **AX** to **ASCII** by adding '**00**' to **AX**. Store the converted digits into **ES**:[**DI**].

## Conversion complete

The conversion is now complete.

```nasm
SYS0CA0: 8BCF          MOV	CX,DI
SYS0CA2: 5F            POP	DI
SYS0CA3: 2BCF          SUB	CX,DI
```

Compute actual number of digits converted in **CX**.

```nasm
SYS0CA5: 8BE5          MOV	SP,BP
SYS0CA7: 5D            POP	BP
SYS0CA8: C3            RET
```

Remove temporary reserved space by restoring **SP** (using **BP**) then pop off **BP** before returning.

## Load one character from source buffer

```nasm
SYS0CA9: 8A42EC        MOV	AL,[BP+SI-14]
SYS0CAC: 46            INC	SI
```

Copy byte character into **AL** then move pointer (**SI**) forward by one byte.

```nasm
SYS0CAD: 0AC0          OR	AL,AL
SYS0CAF: 7503          JNZ	0CB4
```

Check if empty.

```nasm
SYS0CB1: B030          MOV	AL,30
SYS0CB3: 4E            DEC	SI
```

Pad with '**0**' if AL is **NULL** then move pointer (**SI**) back by one byte.

```nasm
SYS0CB4: C3            RET
```

Return.

## Convert ***Real*** number to ASCII.

```nasm
SYS0CB5: 0AC0          OR	AL,AL
SYS0CB7: 750D          JNZ	0CC6
```

At this point **AX** contains the exponent (in **AL**) and **LSB** of the number in **AH**. Check if exponent non-zero.

```nasm
SYS0CB9: B90600        MOV	CX,0006
SYS0CBC: B83030        MOV	AX,3030
SYS0CBF: FC            CLD
SYS0CC0: F3            REPZ
SYS0CC1: AB            STOSW
SYS0CC2: 32C0          XOR	AL,AL
SYS0CC4: AA            STOSB
SYS0CC5: C3            RET
```

If exponent is zero, store **'000000000000'** into the buffer then **NULL/00h** terminate it.

```nasm
SYS0CC6: 80E67F        AND	DH,7F
```

Clear the sign bit in **DH** (Bit 7).

```nasm
SYS0CC9: 50            PUSH	AX
```

Preserve **AX** as it is frequently modified by the arithmetic operations.

```nasm
SYS0CCA: 2C80          SUB	AL,80
```

Remove bias from exponent (**80h**)

```nasm
SYS0CCC: B44D          MOV	AH,4D
SYS0CCE: F6EC          IMUL	AH
```

Multiply un-biased exponent in **AL** with **4Dh/77** and store result in **AX**.

```nasm
SYS0CD0: 050500        ADD	AX,0005
SYS0CD3: 8AC4          MOV	AL,AH
SYS0CD5: 98            CBW
```

Add **5** to **AX** then move high byte to lower byte then convert to word.

```nasm
SYS0CD6: 8BC8          MOV	CX,AX
```

Copy result into **CX**.

```nasm
SYS0CD8: 58            POP	AX
```

Restore **AX**.

```nasm
SYS0CD9: 83F9D9        CMP	CX,-27
SYS0CDC: 7501          JNZ	0CDF
```

Check if **CX** = **FFD9h**/**-27h**/**-39**.

```nasm
SYS0CDE: 41            INC	CX
```

If equal, increment by 1.

```nasm
SYS0CDF: 51            PUSH	CX
SYS0CE0: 57            PUSH	DI
```

Save **CX**, and **DI**.

```nasm
SYS0CE1: F7D9          NEG	CX
```

Negate **CX**.

```nasm
SYS0CE3: E84401        CALL	0E2A
```

Check if **CX** is within range/limits on call to **SYS**:**0E2A**.

```nasm
SYS0CE6: 5F            POP	DI
SYS0CE7: 59            POP	CX
```

Restore **CX**, and **DI** saved in **SYS:0CDF** and **SYS:0CE0**.

```nasm
SYS0CE8: 3C81          CMP	AL,81
SYS0CEA: 7304          JNB	0CF0
```

Check exponent in **AL** if below the biased value (**80h** and below).

```nasm
SYS0CEC: E8C701        CALL	0EB6
```

Adjust the number in **DX**:**BX**:**AX** on call to **SYS**:**0EB6**.

```nasm
SYS0CEF: 49            DEC	CX
```

Adjust CX further.

## Digit extraction and ASCII

This subroutine extracts digits into the maximum precision (12 digits) and stores them into a temporary buffer.

```nasm
SYS0CF0: 51            PUSH	CX
SYS0CF1: 80CE80        OR	DH,80
SYS0CF4: B184          MOV	CL,84
SYS0CF6: 2AC8          SUB	CL,AL
SYS0CF8: B000          MOV	AL,00
SYS0CFA: 740A          JZ	0D06
SYS0CFC: D1EA          SHR	DX,1
SYS0CFE: D1DB          RCR	BX,1
SYS0D00: D1D8          RCR	AX,1
SYS0D02: FEC9          DEC	CL
SYS0D04: 75F6          JNZ	0CFC
```

```nasm
SYS0D06: BE0C00        MOV	SI,000C
```

Convert 12 digits (**SI = 000C**).

```nasm
SYS0D09: 8AEE          MOV	CH,DH
SYS0D0B: B104          MOV	CL,04
SYS0D0D: D2ED          SHR	CH,CL
```

Copy digit to convert to ASCII in **DH** to **CH**. Only the upper digit (bits 4-7) are needed.

```nasm
SYS0D0F: 80C530        ADD	CH,30
SYS0D12: 26            ES:
SYS0D13: 882D          MOV	[DI],CH
```

Convert digit in **CH** to ASCII by adding it '**0**' (**30h**/**48**) and storing in **ES**:[**DI**].

```nasm
SYS0D15: 80E60F        AND	DH,0F
```

Clear upper bits (4-7) in **DH**.

```nasm
SYS0D18: 52            PUSH	DX
SYS0D19: 53            PUSH	BX
SYS0D1A: 50            PUSH	AX
```

Save current number on the stack.

```nasm
SYS0D1B: D1E0          SHL	AX,1
SYS0D1D: D1D3          RCL	BX,1
SYS0D1F: D1D2          RCL	DX,1
SYS0D21: D1E0          SHL	AX,1
SYS0D23: D1D3          RCL	BX,1
SYS0D25: D1D2          RCL	DX,1
SYS0D27: 59            POP	CX
SYS0D28: 03C1          ADD	AX,CX
SYS0D2A: 59            POP	CX
SYS0D2B: 13D9          ADC	BX,CX
SYS0D2D: 59            POP	CX
SYS0D2E: 13D1          ADC	DX,CX
SYS0D30: D1E0          SHL	AX,1
SYS0D32: D1D3          RCL	BX,1
SYS0D34: D1D2          RCL	DX,1
```

This entire sequence multiplies the **DX**:**BX**:**AX** by 10 in place. It does this first by shifting **DX**:**BX**:**AX** twice to the left, with bit 7 of **AX** and **BX** carrying over to **BX** and **DX** through the **CF** flag. Next the number preserved on the stack is added to **DX**:**BX**:**AX**, through successive pops of **CX** then adding it to **AX**, **BX**, and then **DX** with overflows (if any) from **AX**+**CX** and **BX**+**CX**, carrying over to **BX**, and **DX**.

```nasm
SYS0D36: 47            INC	DI
SYS0D37: 4E            DEC	SI
```

Move the index pointer **ES**:**DI** to the next location then decrease the counter in **SI**.

```nasm
SYS0D38: 75CF          JNZ	0D09
```

Repeat the process on the next digit if there are any left.

```nasm
SYS0D3A: 26            ES:
SYS0D3B: C60500        MOV	BYTE PTR [DI],00
```

**NULL** (**00h**) terminate the output buffer at **ES**:[**DI**].

```nasm
SYS0D3E: 59            POP	CX
SYS0D3F: C3            RET
```

Restore CX then return. Because it returns with a **NEAR RET**, it can only be called from within the system library.

## SYS:0E2A Range/Limit checks

```nasm
SYS0E2A: 80F9DA        CMP	CL,DA
SYS0E2D: 7C49          JL	0E78
```

Check if **CX** is less than **DAh**/**-38**.

```nasm
SYS0E2F: 80F926        CMP	CL,26
SYS0E32: 7F44          JG	0E78
```

Check if **CX** is greater than **26h**/**38**.

```nasm
SYS0E34: 52            PUSH	DX
SYS0E35: 53            PUSH	BX
SYS0E36: 50            PUSH	AX
```

Save number **DX**:**BX**:**AX**.

```nasm
SYS0E37: 0AC9          OR	CL,CL
SYS0E39: 9C            PUSHF
```

Check if signed, then save results (Flags).

```nasm
SYS0E3A: 7902          JNS	0E3E
```

Check if signed.

```nasm
SYS0E3C: F6D9          NEG	CL
```

If signed, then negate.

```nasm
SYS0E3E: 8AD9          MOV	BL,CL
SYS0E40: 80E3FC        AND	BL,FC
```

Copy lower byte into BL then clear Bits 0 and 1.

```nasm
SYS0E43: 8AFB          MOV	BH,BL
SYS0E45: D0EB          SHR	BL,1
```

Copy lower byte into BH. Then shift right by 1 bit.

```nasm
SYS0E47: 02DF          ADD	BL,BH
SYS0E49: 32FF          XOR	BH,BH
```

Add to BL then clear BH.

```nasm
SYS0E4B: 8DBF7A0E      LEA	DI,[BX+0E7A]
```

Set lookup index to [**BX+0E7A**] (see **SYS**:**0E7A** below).

```nasm
SYS0E4F: 2E            CS:
SYS0E50: 8B05          MOV	AX,[DI]
SYS0E52: 2E            CS:
SYS0E53: 8B5D02        MOV	BX,[DI+02]
SYS0E56: 2E            CS:
SYS0E57: 8B5504        MOV	DX,[DI+04]
```

Copy numbers to **DX**:**BX**:**AX**.

```nasm
SYS0E5A: 80E103        AND	CL,03
```

Clear bits 2-7 in **CL**.

```nasm
SYS0E5D: 7407          JZ	0E66
```

Check if **CL** is cleared.

```nasm
SYS0E5F: E85400        CALL	0EB6
SYS0E62: FEC9          DEC	CL
SYS0E64: 75F9          JNZ	0E5F
```

Make adjustments to the **DX**:**BX**:**AX** on a call to **SYS**:**0EB6**.

```nasm
SYS0E66: 8BC8          MOV	CX,AX
SYS0E68: 8BF3          MOV	SI,BX
SYS0E6A: 8BFA          MOV	DI,DX
```

Copy **DX**:**BX**:**AX** to **DI**:**SI**:**CX**.

```nasm
SYS0E6C: 9D            POPF
```

Restore Flags (saved in **SYS**:**0E39**).

```nasm
SYS0E6D: 58            POP	AX
SYS0E6E: 5B            POP	BX
SYS0E6F: 5A            POP	DX
```

Restore **DX**:**BX**:**AX**.

```nasm
SYS0E70: 7803          JS	0E75
SYS0E72: E917FA        JMP	088C
SYS0E75: E917FB        JMP	098F
```

Check if signed or unsigned then handle appropriately.

```nasm
SYS0E78: F9            STC
SYS0E79: C3            RET
```

Return with carry flag set.

## Datablock (SYS:0E7A-0EB5)
```
SYS0E7A:                                81 00 00 00 00 00
SYS0E80:  8E 00 00 00 40 1C 9B 00-00 20 BC 3E A8 00 10 A5
SYS0E90:  D4 68 B6 04 BF C9 1B 0E-C3 AC C5 EB 78 2D D0 CD
SYS0EA0:  CE 1B C2 53 DE F9 78 39-3F 01 EB 2B A8 AD C5 1D
SYS0EB0:  F8 C9 7B CE 97 40
```

## Preliminary adjustments before conversion

This subroutine is used to make adjustments to the number so that it is in a proper format.

```nasm
SYS0EB6: 0AC0          OR	AL,AL
SYS0EB8: 7449          JZ	0F03
```

Check if **exponent** = **00h**.

```nasm
SYS0EBA: 51            PUSH	CX
SYS0EBB: 56            PUSH	SI
```

Preserve **CX** and **SI**.

```nasm
SYS0EBC: 80CE80        OR	DH,80
```

Set sign bit.

```nasm
SYS0EBF: 8AC8          MOV	CL,AL
```

Copy exponent int **CL**.

```nasm
SYS0EC1: 32C0          XOR	AL,AL
```

Clear exponent.

```nasm
SYS0EC3: 52            PUSH	DX
SYS0EC4: 53            PUSH	BX
SYS0EC5: 50            PUSH	AX
```

Save number on the stack.

```nasm
SYS0EC6: D1EA          SHR	DX,1
SYS0EC8: D1DB          RCR	BX,1
SYS0ECA: D1D8          RCR	AX,1
SYS0ECC: D1EA          SHR	DX,1
SYS0ECE: D1DB          RCR	BX,1
SYS0ED0: D1D8          RCR	AX,1
SYS0ED2: 5E            POP	SI
SYS0ED3: 03C6          ADD	AX,SI
SYS0ED5: 5E            POP	SI
SYS0ED6: 13DE          ADC	BX,SI
SYS0ED8: 5E            POP	SI
SYS0ED9: 13D6          ADC	DX,SI
```

This entire sequence divides **DX**:**BX**:**AX** by 3. It does this first by shifting **DX**:**BX**:**AX** twice to the right, with bit 0's of **DX** and **BX** carrying over to **BX** and **AX** through the **CF** flag. The number preserved on the stack is then added to **DX**:**BX**:**AX**, through successive pops of **SI** then adding it to **AX**, **BX**, and then **DX** with successive overflows (if any) from **AX**+**SI** and **BX**+**SI**, carrying over to **BX**, and **DX**.

```nasm
SYS0EDB: 730B          JNB	0EE8
```

Chec if the operation triggered a '**carry**'.

```nasm
SYS0EDD: D1DA          RCR	DX,1
SYS0EDF: D1DB          RCR	BX,1
SYS0EE1: D1D8          RCR	AX,1
SYS0EE3: 80C101        ADD	CL,01
```

Shift to the right (including the carry over in **CF**) then increase the exponent number in CL.

```nasm
SYS0EE6: 7219          JB	0F01
```

Check if there is still an overflow in CL then exit.

```nasm
SYS0EE8: 058000        ADD	AX,0080
SYS0EEB: 83D300        ADC	BX,+00
SYS0EEE: 83D200        ADC	DX,+00
```

Fix the exponent then carry over any overflows into **BX** and **DX**.

```nasm
SYS0EF1: 7307          JNB	0EFA
```

Check if there are lingering overflows require another adjustment.

```nasm
SYS0EF3: D1DA          RCR	DX,1
SYS0EF5: 80C101        ADD	CL,01
```

Make space for the sign bit by shifting **DX** to the right once and adjusting the exponent in **CL**.

```nasm
SYS0EF8: 7207          JB	0F01
```

If the operation still produced a carry, then it's possible that the number is badly formatted, out of range or inccorect. If so, nothing can be done further now.

```nasm
SYS0EFA: 80E67F        AND	DH,7F
```

Clear sign bit.

```nasm
SYS0EFD: 8AC1          MOV	AL,CL
SYS0EFF: 0403          ADD	AL,03
```

Copy the exponent in **CL** back to **AL** then adjust by 3 to account for the divide by 3 operation above.

```nasm
SYS0F01: 5E            POP	SI
SYS0F02: 59            POP	CX
```

Restore **CX** and **SI**.

```nasm
SYS0F03: C3            RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

Go [Back](../../README.md)