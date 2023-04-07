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
CODE000F: MOV WORD PTR [A.Low],9282
CODE0015: MOV WORD PTR [A.Mid],DAA2
CODE001B: MOV WORD PTR [A.High],490F
```

The *Real* variable **A** occupies 6 bytes. **490FDAA29282h** is the 48-bit representation of **0.3141592654E01** (PI ~ 9 digits after the decimal) (see: [***Real* Type**](REAL-TYPE.md)).

```nasm
CODE0021: MOV DI,Output
CODE0024: PUSH DS
CODE0025: PUSH DI
CODE0026: PUSH [A.High]
CODE002A: PUSH [A.Mid]
CODE002E: PUSH [A.Low]
CODE0032: MOV AX,0011
CODE0035: PUSH AX
CODE0036: MOV AX,FFFF
CODE0039: PUSH AX
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
CODE003A: CALL SYS:0693
CODE003F: CALL SYS:05DD
CODE0044: CALL SYS:0291
```

These three calls are used to print the *Real* number. We shall walkthrough each of these calls.

## I/O Result Check (I)

```nasm
SYS0291: CMP WORD PTR [InOutRes],+00
SYS0296: JNZ 0299
SYS0298: RETF
SYS0299: MOV AX,[InOutRes]
SYS029C: JMP 010F
```

**SYS:0291** checks whether the printing of the output generated any error. If so, it prints a Runtime error then exits on the jump to **SYS**:**010F**.

## I/O Result check (II)

```nasm
SYS04F7: CMP WORD PTR [InOutRes],+00
SYS04FC: JNZ 053E
```

Check if the last operation generated any error. Exit immediately if there was an error.

```nasm
SYS04FE: ES:
SYS04FF: CMP WORD PTR [BX+02],fmOutput
SYS0504: JNZ 053F
```

Verify that **Output** was opened for output. Generate an error code if not then exit.

```nasm
SYS0506: ES:
SYS0507: MOV CX,[BX+04]
SYS050A: ES:
SYS050B: MOV DI,[BX+08]
SYS050E: SUB CX,DI
SYS0510: SUB DX,CX
SYS0512: JNB 0518
```

Check if buffer has enough room.

```nasm
SYS0514: ADD CX,DX
SYS0516: XOR DX,DX
```

Make adjustments.

```nasm
SYS0518: PUSH ES
```

Save **ES**.

```nasm
SYS0519: ES:
SYS051A: LES SI,[BX+0C]
SYS051D: ADD DI,SI
```

Get pointer to buffer into **ES**:**DI** (adjusted using **SI**).

```nasm
SYS051F: MOV AL,20
SYS0521: CLD
SYS0522: REPZ
SYS0523: STOSB
```

Clear buffer (fill with whitespace (' ') = **20h**/**32**).

```nasm
SYS0524: SUB DI,SI
```

Adjust pointer to reflect the number of characters to copy.

```nasm
SYS0526: POP ES
```

Restore **ES**.

```nasm
SYS0527: ES:
SYS0528: MOV [BX+08],DI
```

Set **Output**'s **[BufPos](../TEXT-FILE-TYPE.md)**.

```nasm
SYS052B: ES:
SYS052C: CMP DI,[BX+04]
SYS052F: JNZ 053A
SYS0531: PUSH DX
SYS0532: PUSH ES
SYS0533: PUSH BX
SYS0534: CALL 0619
SYS0537: POP BX
SYS0538: POP ES
SYS0539: POP DX
SYS053A: OR DX,DX
SYS053C: JNZ 0506
SYS053E: RET
```

```nasm
SYS053F: MOV WORD PTR [InOutRes],0069
SYS0545: RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

```nasm
SYS0546: CMP WORD PTR [InOutRes],+00
SYS054B: JNZ 0595
```

Check if last operation had any errors.

```nasm
SYS054D: ES:
SYS054E: CMP WORD PTR [BX+02],fmOutput
SYS0553: JNZ 0596
```

Check if file/device has been is open for writing. 

```nasm
SYS0555: ES:
SYS0556: MOV CX,[BX+04]
SYS0559: ES:
SYS055A: MOV DI,[BX+08]
SYS055D: SUB CX,DI
SYS055F: SUB AX,CX
SYS0561: JNB 0567
SYS0563: ADD CX,AX
SYS0565: XOR AX,AX
SYS0567: PUSH DS
SYS0568: PUSH ES
SYS0569: PUSH BX
SYS056A: MOV DS,DX
SYS056C: ES:
SYS056D: LES BX,[BX+0C]
SYS0570: ADD DI,BX
SYS0572: CLD
SYS0573: REPZ
SYS0574: MOVSB
SYS0575: SUB DI,BX
SYS0577: POP BX
SYS0578: POP ES
SYS0579: POP DS
SYS057A: ES:
SYS057B: MOV [BX+08],DI
SYS057E: ES:
SYS057F: CMP DI,[BX+04]
SYS0582: JNZ 0591
SYS0584: PUSH AX
SYS0585: PUSH DX
SYS0586: PUSH SI
SYS0587: PUSH ES
SYS0588: PUSH BX
SYS0589: CALL 0619
SYS058C: POP BX
SYS058D: POP ES
SYS058E: POP SI
SYS058F: POP DX
SYS0590: POP AX
SYS0591: OR AX,AX
SYS0593: JNZ 0555
SYS0595: RET
```

```nasm
SYS0596: MOV WORD PTR [InOutRes],0069
SYS059C: RET
```

Return with error error code **[69h/105: File not open for output](../ERROR-CODES.md)**.

## Call to Output I/O

```nasm
SYS0619: PUSH ES
SYS061A: PUSH BX
```

Save registers **ES**:**BX**.

```nasm
SYS061B: ES:
SYS061C: CALL FAR [BX:InOutFunc]
```

Call **Output**'s I/O handler.

```nasm
SYS061F: OR AX,AX
SYS0621: JZ 0626
```

Check if the last operation generated any errors.

```nasm
SYS0623: MOV [InOutRes],AX
```

Record the result of the last operation.

```nasm
SYS0626: RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

## Convert to ASCII routine

```nasm
SYS0693: PUSH BP
SYS0694: MOV BP,SP
SYS0696: SUB SP,+40
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
SYS0699: MOV AX,[BP+0A]
SYS069C: MOV BX,[BP+0C]
SYS069F: MOV DX,[BP+0E]
SYS06A2: MOV CX,[BP+06]
```

Loads *Real* number passed on stack to **DX**:**BX**:**AX**. Where **DX** = **High** word, **BX** = **Mid** word, and **AX** = **Low** word. Load parameter into **CX** ([**BP+06**] = **FFFFh/-1**).

```nasm
SYS06A5: OR CX,CX
SYS06A7: JNS 06B7
```

Check if **CX** is unsigned or a negative number.

```nasm
SYS06A9: MOV CX,0006
SYS06AC: SUB CX,[BP+08]
SYS06AF: CMP CX,-02
SYS06B2: JLE 06B7
```

Check if number of characters representation is enough.

```nasm
SYS06B4: MOV CX,FFFE
```

If not enough, then set default of width 2 digits (**FFFEh**/**-2**).

```nasm
SYS06B7: LEA DI,[BP-40]
SYS06BA: PUSH SS
SYS06BB: POP ES
```

Point **ES**:**DI** to reserved **40h**/**64** bytes on the stack.

```nasm
SYS06BC: CALL 0BBD
```

Call start of conversion routine in **SYS:0BBD**.

```nasm
SYS06BF: LES BX,[BP+10]
```

Load output buffer address in [**BP+10**] into **ES**:**BX**.

```nasm
SYS06C2: MOV DX,[BP+08]
```

Load required **width** into DX.

```nasm
SYS06C5: SUB DX,CX
SYS06C7: JLE 06CE
```

Check if all digits have been converted.

```nasm
SYS06C9: PUSH CX
SYS06CA: CALL 04F7
```

```nasm
SYS06CD: POP CX
SYS06CE: MOV AX,CX
SYS06D0: LEA SI,[BP-40]
SYS06D3: MOV DX,SS
SYS06D5: CALL 0546
SYS06D8: MOV SP,BP
SYS06DA: POP BP
SYS06DB: RETF 000A
```

## Digit extaction logic

|Registers|Description                  |Sample value  |              
|---------|-----------------------------|--------------|
|DI:SI:CX |Number read from **SYS:0E7A**|0000 0000 0081|
|DX:BX:AX |Number to be converted       |HHHH MMMM LLLL|

```nasm
SYS0889: JMP 0988
SYS088C: OR AL,AL
SYS088E: JZ 0889
```

Check if **exponent** is **00h** and exit if there is none.

```nasm
SYS0890: OR CL,CL
SYS0892: JZ 0889
```

Check if **CL** is also empty.

```nasm
SYS0894: PUSH BP
SYS0895: MOV BP,DX
SYS0897: XOR DX,DI
SYS0899: AND DX,8000
SYS089D: XCHG DL,AL
SYS089F: ADD DL,CL
SYS08A1: ADC DH,AL
SYS08A3: MOV CL,AL
SYS08A5: OR BP,8000
SYS08A9: OR DI,8000
SYS08AD: PUSH DX
SYS08AE: OR AH,AH
SYS08B0: JNZ 08B6
SYS08B2: OR BX,BX
SYS08B4: JZ 08C3
SYS08B6: OR CH,CH
SYS08B8: JNZ 08E0
SYS08BA: OR SI,SI
SYS08BC: JNZ 08E0
SYS08BE: XCHG CX,AX
SYS08BF: XCHG BX,SI
SYS08C1: XCHG BP,DI
SYS08C3: MOV AX,CX
SYS08C5: MUL BP
SYS08C7: MOV BX,DX
SYS08C9: MOV AX,SI
SYS08CB: MUL BP
SYS08CD: ADD BX,AX
SYS08CF: ADC DX,+00
SYS08D2: MOV CX,DX
SYS08D4: MOV AX,DI
SYS08D6: MUL BP
SYS08D8: ADD AX,CX
SYS08DA: ADC DX,+00
SYS08DD: JMP 095B
```

```nasm
SYS08DF: NOP
```

```nasm
SYS08E0: PUSH DI
SYS08E1: PUSH SI
SYS08E2: PUSH CX
SYS08E3: PUSH BP
SYS08E4: PUSH BX
SYS08E5: PUSH AX
SYS08E6: MOV BP,SP
SYS08E8: XOR CX,CX
SYS08EA: MOV AL,[BP+01]
SYS08ED: MUL BYTE PTR [BP+07]
SYS08F0: MOV SI,AX
SYS08F2: MOV DI,CX
SYS08F4: MOV BX,CX
SYS08F6: MOV AX,[BP+00]
SYS08F9: MUL WORD PTR [BP+08]
SYS08FC: ADD SI,AX
SYS08FE: ADC DI,DX
SYS0900: ADC BX,CX
SYS0902: MOV AX,[BP+02]
SYS0905: MUL WORD PTR [BP+06]
SYS0908: ADD SI,AX
SYS090A: ADC DI,DX
SYS090C: ADC BX,CX
SYS090E: MOV SI,CX
SYS0910: MOV AX,[BP+00]
SYS0913: MUL WORD PTR [BP+0A]
SYS0916: ADD DI,AX
SYS0918: ADC BX,DX
SYS091A: ADC SI,CX
SYS091C: MOV AX,[BP+02]
SYS091F: MUL WORD PTR [BP+08]
SYS0922: ADD DI,AX
SYS0924: ADC BX,DX
SYS0926: ADC SI,CX
SYS0928: MOV AX,[BP+04]
SYS092B: MUL WORD PTR [BP+06]
SYS092E: ADD DI,AX
SYS0930: ADC BX,DX
SYS0932: ADC SI,CX
SYS0934: MOV DI,CX
SYS0936: MOV AX,[BP+02]
SYS0939: MUL WORD PTR [BP+0A]
SYS093C: ADD BX,AX
SYS093E: ADC SI,DX
SYS0940: ADC DI,CX
SYS0942: MOV AX,[BP+04]
SYS0945: MUL WORD PTR [BP+08]
SYS0948: ADD BX,AX
SYS094A: ADC SI,DX
SYS094C: ADC DI,CX
SYS094E: MOV AX,[BP+04]
SYS0951: MUL WORD PTR [BP+0A]
SYS0954: ADD AX,SI
SYS0956: ADC DX,DI
SYS0958: ADD SP,+0C
```

```nasm
SYS095B: XCHG BX,AX
SYS095C: POP CX
SYS095D: POP BP
SYS095E: OR DH,DH
SYS0960: JS 0969
SYS0962: SHL AX,1
SYS0964: RCL BX,1
SYS0966: RCL DX,1
SYS0968: DEC CX
SYS0969: SUB CX,8081
SYS096D: ADD AX,0080
SYS0970: ADC BX,+00
SYS0973: ADC DX,+00
SYS0976: JNB 097B
SYS0978: RCR DX,1
SYS097A: INC CX
SYS097B: TEST CH,40
SYS097E: JNZ 0988
SYS0980: INC CX
SYS0981: MOV AL,CL
SYS0983: XOR DH,CH
SYS0985: SHR CH,1
SYS0987: RET
```

```nasm
SYS0988: XOR AX,AX
SYS098A: MOV BX,AX
SYS098C: MOV DX,AX
SYS098E: RET
```

```nasm
SYS098F: OR AL,AL
SYS0991: JZ 0988
SYS0993: PUSH BP
SYS0994: MOV BP,DX
SYS0996: XOR DX,DI
SYS0998: OR DI,8000
SYS099C: OR BP,8000
SYS09A0: AND DX,8000
SYS09A4: XCHG AL,DL
SYS09A6: SUB DL,CL
SYS09A8: SBB DH,AL
SYS09AA: PUSH DX
SYS09AB: MOV AL,02
SYS09AD: MOV DX,0001
SYS09B0: CMP BP,DI
SYS09B2: JNZ 09BA
SYS09B4: CMP BX,SI
SYS09B6: JNZ 09BA
SYS09B8: CMP AH,CH
SYS09BA: JB 09C2
SYS09BC: SUB AH,CH
SYS09BE: SBB BX,SI
SYS09C0: SBB BP,DI
SYS09C2: RCL DX,1
SYS09C4: JB 09D7
SYS09C6: SHL AH,1
SYS09C8: RCL BX,1
SYS09CA: RCL BP,1
SYS09CC: JNB 09B0
SYS09CE: SUB AH,CH
SYS09D0: SBB BX,SI
SYS09D2: SBB BP,DI
SYS09D4: CLC
SYS09D5: JMP 09C2
SYS09D7: DEC AL
SYS09D9: JS 09E5
SYS09DB: PUSH DX
SYS09DC: MOV DX,0001
SYS09DF: JNZ 09C6
SYS09E1: MOV DL,40
SYS09E3: JMP 09C6
SYS09E5: MOV AX,DX
SYS09E7: MOV CL,06
SYS09E9: SHL AX,CL
SYS09EB: POP BX
SYS09EC: POP DX
SYS09ED: POP CX
SYS09EE: POP BP
SYS09EF: NOT AX
SYS09F1: NOT BX
SYS09F3: XOR DX,-01
SYS09F6: JS 09FF
SYS09F8: RCL AX,1
SYS09FA: RCL BX,1
SYS09FC: RCL DX,1
SYS09FE: DEC CX
SYS09FF: ADD CX,8080
SYS0A03: JMP 096D
```

```nasm
SYS0BBD: PUSH BP
SYS0BBE: MOV BP,SP
SYS0BC0: SUB SP,+14
```

At this point **DX**:**BX**:**AX** contains the *Real* number. Save BP then reserve **14h/20** bytes on the stack.

```nasm
SYS0BC3: PUSH DI
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
SYS0BC4: CMP CX,+0B
SYS0BC7: JLE 0BCC
SYS0BC9: MOV CX,000B
SYS0BCC: CMP CX,-0B
SYS0BCF: JGE 0BD4
```

Check if CX is within the limit: **-0Bh** <= **CX** <= **+0Bh**.

```nasm
SYS0BD1: MOV CX,FFF5
```

Otherwise, set Default **CX** = **FFF5h**/**-11**.

```nasm
SYS0BD4: MOV [BP-02],CX
```

Store **CX** into **SS**:[**BP-02**].

```nasm
SYS0BD7: MOV [BP-04],DH
```

Store the *Real* number's **MSB** into **SS**:[**BP-04**].

```nasm
SYS0BDA: PUSH ES
SYS0BDB: PUSH DI
```

Preserve **ES**:**DI**.

```nasm
SYS0BDC: LEA DI,[BP-14]
SYS0BDF: PUSH SS
SYS0BE0: POP ES
```

Load pointer to reserved area (**SS**:**BP-14**) into **ES**:**DI**.

```nasm
SYS0BE1: CALL 0CB5
```

```nasm
SYS0BE4: POP DI
SYS0BE5: POP ES
```

Restore **ES**:**DI**.

## Start of conversion logic

```nasm
SYS0BE6: MOV [BP-06],CX
SYS0BE9: MOV SI,[BP-02]
SYS0BEC: OR SI,SI
SYS0BEE: JS 0BFC
SYS0BF0: ADD SI,[BP-06]
SYS0BF3: INC SI
SYS0BF4: JNS 0BFE
```

```nasm
SYS0BF6: MOV BYTE PTR [BP-14],00
SYS0BFA: JMP 0C2A
```

Initialize output buffer by marking the first byte with a **NULL**/**00h** byte.

```nasm
SYS0BFC: NEG SI
```

Negate **SI** to make it positive.

## Truncation logic

```nasm
SYS0BFE: CMP SI,+0C
SYS0C01: JB 0C06
SYS0C03: MOV SI,000B
```

Limit to **0Ch/11** digits.

## Round-off logic

```nasm
SYS0C06: CMP BYTE PTR [BP+SI-14],35
SYS0C0A: MOV BYTE PTR [BP+SI-14],00
SYS0C0E: JB 0C2A
SYS0C10: DEC SI
SYS0C11: JS 0C22
SYS0C13: INC BYTE PTR [BP+SI-14]
SYS0C16: CMP BYTE PTR [BP+SI-14],39
SYS0C1A: JBE 0C2A
SYS0C1C: MOV BYTE PTR [BP+SI-14],00
SYS0C20: JMP 0C10
```

```nasm
SYS0C22: MOV WORD PTR [BP-14],0031
```

Put **NULL** (**00h**)-terminated string '**1**' into start of the buffer.

```nasm
SYS0C27: INC WORD PTR [BP-06]
```

Increase number of digits to convert.

```nasm
SYS0C2A: XOR SI,SI
```

Reset **SI**.

```nasm
SYS0C2C: CLD
SYS0C2D: MOV DX,[BP-02]
SYS0C30: OR DX,DX
SYS0C32: JS 0C69
SYS0C34: TEST BYTE PTR [BP-04],80
SYS0C38: JZ 0C3D
SYS0C3A: MOV AL,2D
SYS0C3C: STOSB
SYS0C3D: MOV CX,[BP-06]
SYS0C40: OR CX,CX
SYS0C42: JNS 0C49
SYS0C44: MOV AL,30
SYS0C46: STOSB
SYS0C47: JMP 0C50
```

```nasm
SYS0C49: CALL 0CA9
SYS0C4C: STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```nasm
SYS0C4D: DEC CX
SYS0C4E: JNS 0C49
SYS0C50: OR DX,DX
SYS0C52: JZ 0CA0
SYS0C54: MOV AL,2E
SYS0C56: STOSB
SYS0C57: INC CX
SYS0C58: JZ 0C60
SYS0C5A: MOV AL,30
SYS0C5C: STOSB
SYS0C5D: DEC DX
SYS0C5E: JNZ 0C57
SYS0C60: DEC DX
SYS0C61: JS 0CA0
```

```nasm
SYS0C63: CALL 0CA9
SYS0C66: STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:**DI**.

```nasm
SYS0C67: JMP 0C60
```

## Format number to scientific notation.

```nasm
SYS0C69: MOV AL,20
SYS0C6B: TEST BYTE PTR [BP-04],80
SYS0C6F: JZ 0C73
```

Prepare ' ' (whitespace) character in **AL**. Check if number is negative.

```nasm
SYS0C71: MOV AL,2D
```

Prepare '**-**' character if number is negative.

```nasm
SYS0C73: STOSB
```

Store character in **AL** to **ES**:[**DI**].

## Convert first digit to ASCII

```nasm
SYS0C74: CALL 0CA9
SYS0C77: STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```nasm
SYS0C78: INC DX
```

Add to count.

```nasm
SYS0C79: JZ 0C85
```

Check if there are mo more digits to convert.

```nasm
SYS0C7B: MOV AL,2E
SYS0C7D: STOSB
```

Store '**.**' into the buffer at **ES**:[**DI**].

## Convert significand to ASCII

```nasm
SYS0C7E: CALL 0CA9
SYS0C81: STOSB
```

Load one byte into **AL** on call to **SYS**:**0CA9** and store in **ES**:[**DI**].

```nasm
SYS0C82: INC DX
SYS0C83: JNZ 0C7E
```

Cycle until all of the **significand** **f**'s digits are stored in output buffer.

## Convert Exponent to ASCII

```nasm
SYS0C85: MOV AL,45
SYS0C87: STOSB
```

Store '**E**' into the buffer at **ES**:[**DI**] (**scientific notation**).

```nasm
SYS0C88: MOV AL,2B
```

Prepare '**+**' character if number is exponent is positive.

```nasm
SYS0C8A: MOV DX,[BP-06]
SYS0C8D: OR DX,DX
SYS0C8F: JNS 0C95
```

Check if exponent is negative (**SF** = **1**, **signed**).

```nasm
SYS0C91: MOV AL,2D
SYS0C93: NEG DX
```

Prepare '**-**' character since exponent is negative. Negate **DX** to make the exponent positive.

```nasm
SYS0C95: STOSB
```

Store **exponent** sign into the buffer at **ES**:[**DI**].

```nasm
SYS0C96: MOV AX,DX
```

Store exponent in **AX**.

```nasm
SYS0C98: MOV DL,0A
SYS0C9A: IDIV DL
```

Convert exponent in **AL** to base 10 (**DL** = **0Ah**) by doing a signed division of **AL** by **DL**. Quotient in **AL**, remainder in **AH**.

```nasm
SYS0C9C: ADD AX,3030
SYS0C9F: STOSW
```

Convert exponent digits in **AX** to **ASCII** by adding '**00**' to **AX**. Store the converted digits into **ES**:[**DI**].

## Conversion complete

The conversion is now complete.

```nasm
SYS0CA0: MOV CX,DI
SYS0CA2: POP DI
SYS0CA3: SUB CX,DI
```

Compute actual number of digits converted in **CX**.

```nasm
SYS0CA5: MOV SP,BP
SYS0CA7: POP BP
SYS0CA8: RET
```

Remove temporary reserved space by restoring **SP** (using **BP**) then pop off **BP** before returning.

## Load one character from source buffer

```nasm
SYS0CA9: MOV AL,[BP+SI-14]
SYS0CAC: INC SI
```

Copy byte character into **AL** then move pointer (**SI**) forward by one byte.

```nasm
SYS0CAD: OR AL,AL
SYS0CAF: JNZ 0CB4
```

Check if empty.

```nasm
SYS0CB1: MOV AL,30
SYS0CB3: DEC SI
```

Pad with '**0**' if AL is **NULL** then move pointer (**SI**) back by one byte.

```nasm
SYS0CB4: RET
```

Return.

## Convert ***Real*** number to ASCII.

```nasm
SYS0CB5: OR AL,AL
SYS0CB7: JNZ 0CC6
```

At this point **AX** contains the exponent (in **AL**) and **LSB** of the number in **AH**. Check if exponent non-zero.

```nasm
SYS0CB9: MOV CX,0006
SYS0CBC: MOV AX,3030
SYS0CBF: CLD
SYS0CC0: REPZ
SYS0CC1: STOSW
SYS0CC2: XOR AL,AL
SYS0CC4: STOSB
SYS0CC5: RET
```

If exponent is zero, store **'000000000000'** into the buffer then **NULL/00h** terminate it.

```nasm
SYS0CC6: AND DH,7F
```

Clear the sign bit in **DH** (Bit 7).

```nasm
SYS0CC9: PUSH AX
```

Preserve **AX** as it is frequently modified by the arithmetic operations.

```nasm
SYS0CCA: SUB AL,80
```

Remove bias from exponent (**80h**)

```nasm
SYS0CCC: MOV AH,4D
SYS0CCE: IMUL AH
```

Multiply un-biased exponent in **AL** with **4Dh/77** and store result in **AX**.

```nasm
SYS0CD0: ADD AX,0005
SYS0CD3: MOV AL,AH
SYS0CD5: CBW
```

Add **5** to **AX** then move high byte to lower byte then convert to word.

```nasm
SYS0CD6: MOV CX,AX
```

Copy result into **CX**.

```nasm
SYS0CD8: POP AX
```

Restore **AX**.

```nasm
SYS0CD9: CMP CX,-27
SYS0CDC: JNZ 0CDF
```

Check if **CX** = **FFD9h**/**-27h**/**-39**.

```nasm
SYS0CDE: INC CX
```

If equal, increment by 1.

```nasm
SYS0CDF: PUSH CX
SYS0CE0: PUSH DI
```

Save **CX**, and **DI**.

```nasm
SYS0CE1: NEG CX
```

Negate **CX**.

```nasm
SYS0CE3: CALL 0E2A
```

Check if **CX** is within range/limits on call to **SYS**:**0E2A**.

```nasm
SYS0CE6: POP DI
SYS0CE7: POP CX
```

Restore **CX**, and **DI** saved in **SYS:0CDF** and **SYS:0CE0**.

```nasm
SYS0CE8: CMP AL,81
SYS0CEA: JNB 0CF0
```

Check exponent in **AL** if below the biased value (**80h** and below).

```nasm
SYS0CEC: CALL 0EB6
```

Adjust the number in **DX**:**BX**:**AX** on call to **SYS**:**0EB6**.

```nasm
SYS0CEF: DEC CX
```

Adjust CX further.

## Digit extraction and ASCII

This subroutine extracts digits into the maximum precision (12 digits) and stores them into a temporary buffer.

```nasm
SYS0CF0: PUSH CX
SYS0CF1: OR DH,80
SYS0CF4: MOV CL,84
SYS0CF6: SUB CL,AL
SYS0CF8: MOV AL,00
SYS0CFA: JZ 0D06
SYS0CFC: SHR DX,1
SYS0CFE: RCR BX,1
SYS0D00: RCR AX,1
SYS0D02: DEC CL
SYS0D04: JNZ 0CFC
```

```nasm
SYS0D06: MOV SI,000C
```

Convert 12 digits (**SI = 000C**).

```nasm
SYS0D09: MOV CH,DH
SYS0D0B: MOV CL,04
SYS0D0D: SHR CH,CL
```

Copy digit to convert to ASCII in **DH** to **CH**. Only the upper digit (bits 4-7) are needed.

```nasm
SYS0D0F: ADD CH,30
SYS0D12: ES:
SYS0D13: MOV [DI],CH
```

Convert digit in **CH** to ASCII by adding it '**0**' (**30h**/**48**) and storing in **ES**:[**DI**].

```nasm
SYS0D15: AND DH,0F
```

Clear upper bits (4-7) in **DH**.

```nasm
SYS0D18: PUSH DX
SYS0D19: PUSH BX
SYS0D1A: PUSH AX
```

Save current number on the stack.

```nasm
SYS0D1B: SHL AX,1
SYS0D1D: RCL BX,1
SYS0D1F: RCL DX,1
SYS0D21: SHL AX,1
SYS0D23: RCL BX,1
SYS0D25: RCL DX,1
SYS0D27: POP CX
SYS0D28: ADD AX,CX
SYS0D2A: POP CX
SYS0D2B: ADC BX,CX
SYS0D2D: POP CX
SYS0D2E: ADC DX,CX
SYS0D30: SHL AX,1
SYS0D32: RCL BX,1
SYS0D34: RCL DX,1
```

This entire sequence multiplies the **DX**:**BX**:**AX** by 10 in place. It does this first by shifting **DX**:**BX**:**AX** twice to the left, with bit 7 of **AX** and **BX** carrying over to **BX** and **DX** through the **CF** flag. Next the number preserved on the stack is added to **DX**:**BX**:**AX**, through successive pops of **CX** then adding it to **AX**, **BX**, and then **DX** with overflows (if any) from **AX**+**CX** and **BX**+**CX**, carrying over to **BX**, and **DX**.

```nasm
SYS0D36: INC DI
SYS0D37: DEC SI
```

Move the index pointer **ES**:**DI** to the next location then decrease the counter in **SI**.

```nasm
SYS0D38: JNZ 0D09
```

Repeat the process on the next digit if there are any left.

```nasm
SYS0D3A: ES:
SYS0D3B: MOV BYTE PTR [DI],00
```

**NULL** (**00h**) terminate the output buffer at **ES**:[**DI**].

```nasm
SYS0D3E: POP CX
SYS0D3F: RET
```

Restore CX then return. Because it returns with a **NEAR RET**, it can only be called from within the system library.

## SYS:0E2A Range/Limit checks

```nasm
SYS0E2A: CMP CL,DA
SYS0E2D: JL 0E78
```

Check if **CX** is less than **DAh**/**-38**.

```nasm
SYS0E2F: CMP CL,26
SYS0E32: JG 0E78
```

Check if **CX** is greater than **26h**/**38**.

```nasm
SYS0E34: PUSH DX
SYS0E35: PUSH BX
SYS0E36: PUSH AX
```

Save number **DX**:**BX**:**AX**.

```nasm
SYS0E37: OR CL,CL
SYS0E39: PUSHF
```

Check if signed, then save results (Flags).

```nasm
SYS0E3A: JNS 0E3E
```

Check if signed.

```nasm
SYS0E3C: NEG CL
```

If signed, then negate.

```nasm
SYS0E3E: MOV BL,CL
SYS0E40: AND BL,FC
```

Copy lower byte into BL then clear Bits 0 and 1.

```nasm
SYS0E43: MOV BH,BL
SYS0E45: SHR BL,1
```

Copy lower byte into BH. Then shift right by 1 bit.

```nasm
SYS0E47: ADD BL,BH
SYS0E49: XOR BH,BH
```

Add to BL then clear BH.

```nasm
SYS0E4B: LEA DI,[BX+0E7A]
```

Set lookup index to [**BX+0E7A**] (see **SYS**:**0E7A** below).

```nasm
SYS0E4F: CS:
SYS0E50: MOV AX,[DI]
SYS0E52: CS:
SYS0E53: MOV BX,[DI+02]
SYS0E56: CS:
SYS0E57: MOV DX,[DI+04]
```

Copy numbers to **DX**:**BX**:**AX**.

```nasm
SYS0E5A: AND CL,03
```

Clear bits 2-7 in **CL**.

```nasm
SYS0E5D: JZ 0E66
```

Check if **CL** is cleared.

```nasm
SYS0E5F: CALL 0EB6
SYS0E62: DEC CL
SYS0E64: JNZ 0E5F
```

Make adjustments to the **DX**:**BX**:**AX** on a call to **SYS**:**0EB6**.

```nasm
SYS0E66: MOV CX,AX
SYS0E68: MOV SI,BX
SYS0E6A: MOV DI,DX
```

Copy **DX**:**BX**:**AX** to **DI**:**SI**:**CX**.

```nasm
SYS0E6C: POPF
```

Restore Flags (saved in **SYS**:**0E39**).

```nasm
SYS0E6D: POP AX
SYS0E6E: POP BX
SYS0E6F: POP DX
```

Restore **DX**:**BX**:**AX**.

```nasm
SYS0E70: JS 0E75
SYS0E72: JMP 088C
SYS0E75: JMP 098F
```

Check if signed or unsigned then handle appropriately.

```nasm
SYS0E78: STC
SYS0E79: RET
```

Return with carry flag set.

## Datablock (SYS:0E7A-0EB5)
```
SYS0E7A:  81 00 00 00 00 00
SYS0E80:  8E 00 00 00 40 1C
SYS0E86:  9B 00 00 20 BC 3E
SYS0E8C:  A8 00 10 A5 D4 68
SYS0E92:  B6 04 BF C9 1B 0E
SYS0E98:  C3 AC C5 EB 78 2D
SYS0E9E:  D0 CD CE 1B C2 53
SYS0EA4:  DE F9 78 39 3F 01
SYS0EAA:  EB 2B A8 AD C5 1D
SYS0EB0:  F8 C9 7B CE 97 40
```

## Preliminary adjustments before conversion

This subroutine is used to make adjustments to the number so that it is in a proper format.

```nasm
SYS0EB6: OR AL,AL
SYS0EB8: JZ 0F03
```

Check if **exponent** = **00h**.

```nasm
SYS0EBA: PUSH CX
SYS0EBB: PUSH SI
```

Preserve **CX** and **SI**.

```nasm
SYS0EBC: OR DH,80
```

Set sign bit.

```nasm
SYS0EBF: MOV CL,AL
```

Copy exponent int **CL**.

```nasm
SYS0EC1: XOR AL,AL
```

Clear exponent.

```nasm
SYS0EC3: PUSH DX
SYS0EC4: PUSH BX
SYS0EC5: PUSH AX
```

Save number on the stack.

```nasm
SYS0EC6: SHR DX,1
SYS0EC8: RCR BX,1
SYS0ECA: RCR AX,1
SYS0ECC: SHR DX,1
SYS0ECE: RCR BX,1
SYS0ED0: RCR AX,1
SYS0ED2: POP SI
SYS0ED3: ADD AX,SI
SYS0ED5: POP SI
SYS0ED6: ADC BX,SI
SYS0ED8: POP SI
SYS0ED9: ADC DX,SI
```

This entire sequence divides **DX**:**BX**:**AX** by 3. It does this first by shifting **DX**:**BX**:**AX** twice to the right, with bit 0's of **DX** and **BX** carrying over to **BX** and **AX** through the **CF** flag. The number preserved on the stack is then added to **DX**:**BX**:**AX**, through successive pops of **SI** then adding it to **AX**, **BX**, and then **DX** with successive overflows (if any) from **AX**+**SI** and **BX**+**SI**, carrying over to **BX**, and **DX**.

```nasm
SYS0EDB: JNB 0EE8
```

Chec if the operation triggered a '**carry**'.

```nasm
SYS0EDD: RCR DX,1
SYS0EDF: RCR BX,1
SYS0EE1: RCR AX,1
SYS0EE3: ADD CL,01
```

Shift to the right (including the carry over in **CF**) then increase the exponent number in CL.

```nasm
SYS0EE6: JB 0F01
```

Check if there is still an overflow in CL then exit.

```nasm
SYS0EE8: ADD AX,0080
SYS0EEB: ADC BX,+00
SYS0EEE: ADC DX,+00
```

Fix the exponent then carry over any overflows into **BX** and **DX**.

```nasm
SYS0EF1: JNB 0EFA
```

Check if there are lingering overflows require another adjustment.

```nasm
SYS0EF3: RCR DX,1
SYS0EF5: ADD CL,01
```

Make space for the sign bit by shifting **DX** to the right once and adjusting the exponent in **CL**.

```nasm
SYS0EF8: JB 0F01
```

If the operation still produced a carry, then it's possible that the number is badly formatted, out of range or inccorect. If so, nothing can be done further now.

```nasm
SYS0EFA: AND DH,7F
```

Clear sign bit.

```nasm
SYS0EFD: MOV AL,CL
SYS0EFF: ADD AL,03
```

Copy the exponent in **CL** back to **AL** then adjust by 3 to account for the divide by 3 operation above.

```nasm
SYS0F01: POP SI
SYS0F02: POP CX
```

Restore **CX** and **SI**.

```nasm
SYS0F03: RET
```

Because it returns with a **NEAR RET**, it can only be called from within the system library.

Go [Back](../../README.md)