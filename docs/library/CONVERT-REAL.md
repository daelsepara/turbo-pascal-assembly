# Convert *Real* number to string

Converts a ***Real*** number to a string.

This is a disassembly of the Pascal Program:
```pascal
program main;
        var A: Real;
        var B: String;
        begin
                A := 0.3141592654E01;
                Str(A, B);
        end.
end program;
```

It was compiled with x87 emulation and x87 code disabled using:

```cmd
TPC.EXE -GS -GP -GD -$D+ -$E- -$N- MAIN
```

## Complete Disassembly of the Main Program
```nasm
CODE0000: 9A00007507    CALL	SYS:0000
CODE0005: 55            PUSH	BP
CODE0006: 89E5          MOV	BP,SP
CODE0008: 31C0          XOR	AX,AX
CODE000A: 9ACD027507    CALL	SYS:02CD
CODE000F: C70650008292  MOV	WORD PTR [A.Low],9282
CODE0015: C7065200A2DA  MOV	WORD PTR [A.Mid],DAA2
CODE001B: C70654000F49  MOV	WORD PTR [A.High],490F
CODE0021: FF365400      PUSH	[A.High]
CODE0025: FF365200      PUSH	[A.Mid]
CODE0029: FF365000      PUSH	[A.Low]
CODE002D: B81100        MOV	AX,0011
CODE0030: 50            PUSH	AX
CODE0031: B8FFFF        MOV	AX,FFFF
CODE0034: 50            PUSH	AX
CODE0035: BF5600        MOV	DI,OFFSET B
CODE0038: 1E            PUSH	DS
CODE0039: 57            PUSH	DI
CODE003A: B8FF00        MOV	AX,00FF
CODE003D: 50            PUSH	AX
CODE003E: 9ABF0C7507    CALL	SYS:0CBF
CODE0043: 5D            POP	BP
CODE0044: 31C0          XOR	AX,AX
CODE0046: 9A16017507    CALL	SYS:0116
```

## Assignment

Assign a ***Real*** value to **A**.

```pascal
A := 0.3141592654E01;
```

Compiles to:

```nasm
CODE000F: C70650008292  MOV	WORD PTR [A.Low],9282
CODE0015: C7065200A2DA  MOV	WORD PTR [A.Mid],DAA2
CODE001B: C70654000F49  MOV	WORD PTR [A.High],490F
```

## Conversion to String

Call on Pascal **Str()** function to convert ***Real*** variable **A** to a ***string*** in **B**.

```pascal
Str(A, B);
```

Compiles to:

```nasm
CODE0021: FF365400      PUSH	[A.High]
CODE0025: FF365200      PUSH	[A.Mid]
CODE0029: FF365000      PUSH	[A.Low]
CODE002D: B81100        MOV	AX,0011
CODE0030: 50            PUSH	AX
CODE0031: B8FFFF        MOV	AX,FFFF
CODE0034: 50            PUSH	AX
CODE0035: BF5600        MOV	DI,OFFSET B
CODE0038: 1E            PUSH	DS
CODE0039: 57            PUSH	DI
CODE003A: B8FF00        MOV	AX,00FF
CODE003D: 50            PUSH	AX
CODE003E: 9ABF0C7507    CALL	SYS:0CBF
```

## Initialize System Library
```nasm
SYS0000: BA4C08        MOV	DX,DATA
SYS0003: 8EDA          MOV	DS,DX
SYS0005: 8C063800      MOV	[PrefixSeg],ES
SYS0009: 33ED          XOR	BP,BP
SYS000B: E8470D        CALL	0D55
SYS000E: E8A000        CALL	00B1
SYS0011: 8BC4          MOV	AX,SP
SYS0013: 051300        ADD	AX,0013
SYS0016: B104          MOV	CL,04
SYS0018: D3E8          SHR	AX,CL
SYS001A: 8CD2          MOV	DX,SS
SYS001C: 03C2          ADD	AX,DX
SYS001E: A30A00        MOV	[OvrHeapOrg],AX
SYS0021: A30C00        MOV	[OvrHeapPtr],AX
SYS0024: 03060400      ADD	AX,[OvrHeapSize]
SYS0028: A30E00        MOV	[OvrHeapEnd],AX
SYS002B: A31800        MOV	[HeapOrg.Segment],AX
SYS002E: A31C00        MOV	[HeapPtr.Segment],AX
SYS0031: A32400        MOV	[FreeList.Segment],AX
SYS0034: 8E063800      MOV	ES,[PrefixSeg]
SYS0038: 26            ES:
SYS0039: A10200        MOV	AX,[PrefixSeg:0002]
SYS003C: A32000        MOV	[HeapEnd],AX
SYS003F: C7062A00D600  MOV	WORD PTR [HeapError.Offset],00D6
SYS0045: 8C0E2C00      MOV	[HeapError.Segment],CS
```

## Save Interrupt Vectors
```nasm
SYS0049: BF5603        MOV	DI,OFFSET SaveInt00
SYS004C: BE3902        MOV	SI,0239
SYS004F: B91300        MOV	CX,0013
SYS0052: 90            NOP
SYS0053: FC            CLD
SYS0054: 2E            CS:
SYS0055: AC            LODSB
SYS0056: B435          MOV	AH,35
SYS0058: CD21          INT	21
SYS005A: 891D          MOV	[DI],BX
SYS005C: 8C4502        MOV	[DI+02],ES
SYS005F: 83C704        ADD	DI,+04
SYS0062: E2EF          LOOP	0053
```

## Modify Interrupt Vectors
```nasm
SYS0064: 1E            PUSH	DS
SYS0065: 0E            PUSH	CS
SYS0066: 1F            POP	DS
SYS0067: BA0C01        MOV	DX,010C
SYS006A: B80025        MOV	AX,2500
SYS006D: CD21          INT	21
SYS006F: BA1301        MOV	DX,0113
SYS0072: B82325        MOV	AX,2523
SYS0075: CD21          INT	21
SYS0077: BADB00        MOV	DX,00DB
SYS007A: B82425        MOV	AX,2524
SYS007D: CD21          INT	21
SYS007F: BA0401        MOV	DX,0104
SYS0082: B83F25        MOV	AX,253F
SYS0085: CD21          INT	21
```

## Input and Output File/Device Initialization
```nasm
SYS0087: 1F            POP	DS
SYS0088: B85601        MOV	AX,OFFSET Input
SYS008B: 1E            PUSH	DS
SYS008C: 50            PUSH	AX
SYS008D: 1E            PUSH	DS
SYS008E: 50            PUSH	AX
SYS008F: B86302        MOV	AX,0263
SYS0092: 0E            PUSH	CS
SYS0093: 50            PUSH	AX
SYS0094: 0E            PUSH	CS
SYS0095: E84E02        CALL	02E6
SYS0098: 0E            PUSH	CS
SYS0099: E8C802        CALL	0364
SYS009C: B85602        MOV	AX,OFFSET Output
SYS009F: 1E            PUSH	DS
SYS00A0: 50            PUSH	AX
SYS00A1: 1E            PUSH	DS
SYS00A2: 50            PUSH	AX
SYS00A3: B86302        MOV	AX,0263
SYS00A6: 0E            PUSH	CS
SYS00A7: 50            PUSH	AX
SYS00A8: 0E            PUSH	CS
SYS00A9: E83A02        CALL	02E6
SYS00AC: 0E            PUSH	CS
SYS00AD: E8B902        CALL	0369
SYS00B0: CB            RETF
```

## Determine CPU type
```nasm
SYS00B1: 33C0          XOR	AX,AX
SYS00B3: 9C            PUSHF
SYS00B4: 5B            POP	BX
SYS00B5: 80E70F        AND	BH,0F
SYS00B8: 53            PUSH	BX
SYS00B9: 9D            POPF
SYS00BA: 9C            PUSHF
SYS00BB: 59            POP	CX
SYS00BC: 80E5F0        AND	CH,F0
SYS00BF: 80FDF0        CMP	CH,F0
SYS00C2: 740E          JZ	00D2
SYS00C4: 40            INC	AX
SYS00C5: 80CFF0        OR	BH,F0
SYS00C8: 53            PUSH	BX
SYS00C9: 9D            POPF
SYS00CA: 9C            PUSHF
SYS00CB: 59            POP	CX
SYS00CC: 80E5F0        AND	CH,F0
SYS00CF: 7401          JZ	00D2
SYS00D1: 40            INC	AX
SYS00D2: A24C00        MOV	[Test8086],AL
SYS00D5: C3            RET
```

## Heap Error Handler
```nasm
SYS00D6: 33C0          XOR	AX,AX
SYS00D8: CA0200        RETF	0002
```

## INT 24h Critical Error Handler
```nasm
SYS00DB: FB            STI
SYS00DC: 83C406        ADD	SP,+06
SYS00DF: 58            POP	AX
SYS00E0: 83E71F        AND	DI,+1F
SYS00E3: 81C79600      ADD	DI,0096
SYS00E7: 80FC39        CMP	AH,39
SYS00EA: 7303          JNB	00EF
SYS00EC: BFFFFF        MOV	DI,FFFF
SYS00EF: 57            PUSH	DI
SYS00F0: B454          MOV	AH,54
SYS00F2: CD21          INT	21
SYS00F4: 8BEC          MOV	BP,SP
SYS00F6: 804E1601      OR	BYTE PTR [BP+16],01
SYS00FA: 58            POP	AX
SYS00FB: 5B            POP	BX
SYS00FC: 59            POP	CX
SYS00FD: 5A            POP	DX
SYS00FE: 5E            POP	SI
SYS00FF: 5F            POP	DI
SYS0100: 5D            POP	BP
SYS0101: 1F            POP	DS
SYS0102: 07            POP	ES
SYS0103: CF            IRET
```

## INT 3Fh Overlay Manager Handler
```nasm
SYS0104: B8D000        MOV	AX,00D0
SYS0107: 83C406        ADD	SP,+06
SYS010A: EB03          JMP	010F
```

## INT 00h Handler
```nasm
SYS010C: B8C800        MOV	AX,00C8
SYS010F: 59            POP	CX
SYS0110: 5B            POP	BX
SYS0111: EB07          JMP	011A
```

## INT 23h CTRL-C Handler and Exit Routine Entry Point
```nasm
SYS0113: B8FF00        MOV	AX,00FF
SYS0116: 33C9          XOR	CX,CX
SYS0118: 33DB          XOR	BX,BX
SYS011A: BA4C08        MOV	DX,DATA
SYS011D: 8EDA          MOV	DS,DX
SYS011F: FB            STI
SYS0120: A33200        MOV	[ExitCode],AX
SYS0123: 8BC1          MOV	AX,CX
SYS0125: 0BC3          OR	AX,BX
SYS0127: 743D          JZ	0166
SYS0129: A11000        MOV	AX,[OvrLoadList]
SYS012C: 0BC0          OR	AX,AX
SYS012E: 742F          JZ	015F
SYS0130: 8EC0          MOV	ES,AX
SYS0132: 26            ES:
SYS0133: A11000        MOV	AX,[OvrLoadList]
SYS0136: 0BC0          OR	AX,AX
SYS0138: 741B          JZ	0155
SYS013A: 2BC3          SUB	AX,BX
SYS013C: 7717          JA	0155
SYS013E: F7D8          NEG	AX
SYS0140: 3D0010        CMP	AX,1000
SYS0143: 7310          JNB	0155
SYS0145: BA1000        MOV	DX,0010
SYS0148: F7E2          MUL	DX
SYS014A: 03C1          ADD	AX,CX
SYS014C: 7207          JB	0155
SYS014E: 26            ES:
SYS014F: 3B060800      CMP	AX,[OvrDebugPtr.Segment]
SYS0153: 7206          JB	015B
SYS0155: 26            ES:
SYS0156: A11400        MOV	AX,[OvrEmsHandle]
SYS0159: EBD1          JMP	012C
SYS015B: 8BC8          MOV	CX,AX
SYS015D: 8CC3          MOV	BX,ES
SYS015F: 2B1E3800      SUB	BX,[PrefixSeg]
SYS0163: 83EB10        SUB	BX,+10
SYS0166: 890E3400      MOV	[ErrorAddr.Offset],CX
SYS016A: 891E3600      MOV	[ErrorAddr.Segment],BX
SYS016E: C41E2E00      LES	BX,[ExitProc]
SYS0172: 8CC0          MOV	AX,ES
SYS0174: 0BC3          OR	AX,BX
SYS0176: 7413          JZ	018B
SYS0178: 33C0          XOR	AX,AX
SYS017A: A32E00        MOV	[ExitProc.Offset],AX
SYS017D: A33000        MOV	[ExitProc.Segment],AX
SYS0180: A33C00        MOV	[InOutRes],AX
SYS0183: B86E01        MOV	AX,016E
SYS0186: 0E            PUSH	CS
SYS0187: 50            PUSH	AX
SYS0188: 06            PUSH	ES
SYS0189: 53            PUSH	BX
SYS018A: CB            RETF
```

## Flush Input and Output File/Device
```nasm
SYS018B: B85601        MOV	AX,OFFSET Input
SYS018E: 1E            PUSH	DS
SYS018F: 50            PUSH	AX
SYS0190: 0E            PUSH	CS
SYS0191: E82A02        CALL	03BE
SYS0194: B85602        MOV	AX,OFFSET Output
SYS0197: 1E            PUSH	DS
SYS0198: 50            PUSH	AX
SYS0199: 0E            PUSH	CS
SYS019A: E82102        CALL	03BE
```

## Restore Interrupt Vectors
```nasm
SYS019D: BF5603        MOV	DI,OFFSET SaveInt00
SYS01A0: BE3902        MOV	SI,0239
SYS01A3: B91300        MOV	CX,0013
SYS01A6: 90            NOP
SYS01A7: FC            CLD
SYS01A8: 2E            CS:
SYS01A9: AC            LODSB
SYS01AA: B425          MOV	AH,25
SYS01AC: 1E            PUSH	DS
SYS01AD: C515          LDS	DX,[DI]
SYS01AF: CD21          INT	21
SYS01B1: 1F            POP	DS
SYS01B2: 83C704        ADD	DI,+04
SYS01B5: E2F0          LOOP	01A7
```

## Exit
```nasm
SYS01B7: A13400        MOV	AX,[ErrorAddr.Offset]
SYS01BA: 0B063600      OR	AX,[ErrorAddr.Segment]
SYS01BE: 7429          JZ	01E9
SYS01C0: BB4C02        MOV	BX,024C
SYS01C3: E82A00        CALL	01F0
SYS01C6: A13200        MOV	AX,[ExitCode]
SYS01C9: E83200        CALL	01FE
SYS01CC: BB5B02        MOV	BX,025B
SYS01CF: E81E00        CALL	01F0
SYS01D2: A13600        MOV	AX,[ErrorAddr.Segment]
SYS01D5: E84000        CALL	0218
SYS01D8: B03A          MOV	AL,3A
SYS01DA: E85500        CALL	0232
SYS01DD: A13400        MOV	AX,[ErrorAddr.Offset]
SYS01E0: E83500        CALL	0218
SYS01E3: BB6002        MOV	BX,0260
SYS01E6: E80700        CALL	01F0
SYS01E9: A13200        MOV	AX,[ExitCode]
SYS01EC: B44C          MOV	AH,4C
SYS01EE: CD21          INT	21
```

## Print string directly to console
```nasm
SYS01F0: 2E            CS:
SYS01F1: 8A07          MOV	AL,[BX]
SYS01F3: 0AC0          OR	AL,AL
SYS01F5: 7406          JZ	01FD
SYS01F7: E83800        CALL	0232
SYS01FA: 43            INC	BX
SYS01FB: EBF3          JMP	01F0
SYS01FD: C3            RET
```

## Print character/digit directly to console
```nasm
SYS01FE: B164          MOV	CL,64
SYS0200: E80700        CALL	020A
SYS0203: B10A          MOV	CL,0A
SYS0205: E80200        CALL	020A
SYS0208: EB04          JMP	020E
SYS020A: 32E4          XOR	AH,AH
SYS020C: F6F1          DIV	CL
SYS020E: 0430          ADD	AL,30
SYS0210: 50            PUSH	AX
SYS0211: E81E00        CALL	0232
SYS0214: 58            POP	AX
SYS0215: 8AC4          MOV	AL,AH
SYS0217: C3            RET
SYS0218: 50            PUSH	AX
SYS0219: 8AC4          MOV	AL,AH
SYS021B: E80100        CALL	021F
SYS021E: 58            POP	AX
SYS021F: 50            PUSH	AX
SYS0220: B104          MOV	CL,04
SYS0222: D2E8          SHR	AL,CL
SYS0224: E80300        CALL	022A
SYS0227: 58            POP	AX
SYS0228: 240F          AND	AL,0F
SYS022A: 0430          ADD	AL,30
SYS022C: 3C3A          CMP	AL,3A
SYS022E: 7202          JB	0232
SYS0230: 0407          ADD	AL,07
SYS0232: 8AD0          MOV	DL,AL
SYS0234: B406          MOV	AH,06
SYS0236: CD21          INT	21
SYS0238: C3            RET
```

## SYS:0239 Data Block (Interrupt list)
```
SYS0230:                             00 02 1B 21 23 24 34
SYS0240:  35 36 37 38 39 3A 3B 3C-3D 3E 3F 75
```

## SYS:024C Runtime error strings
```nasm
SYS024C:                                      52 75 6E 74               Runt
SYS0250:  69 6D 65 20 65 72 72 6F-72 20 00                  ime error
SYS025B:                                   20 61 74 20 00               at
SYS0260:  2E
SYS0261:     0D 0A
```

## SYS:0263 Empty string
```
SYS0263:           00
```

## SYS:0264 Unused copyright string
```
SYS0264:              50 6F 72 74-69 6F 6E 73 20 43 6F 70       Portions Cop
SYS0270:  79 72 69 67 68 74 20 28-63 29 20 31 39 38 33 2C   yright (c) 1983,
SYS0280:  39 32 20 42 6F 72 6C 61-6E 64                     92 Borland
```

## Clear I/O Result
```nasm
SYS028A: 33C0          XOR	AX,AX
SYS028C: 87063C00      XCHG	AX,[InOutRes]
SYS0290: CB            RETF
```

## Check I/O Result
```nasm
SYS0291: 833E3C0000    CMP	WORD PTR [InOutRes],+00
SYS0296: 7501          JNZ	0299
SYS0298: CB            RETF
SYS0299: A13C00        MOV	AX,[InOutRes]
SYS029C: E970FE        JMP	010F
```

## Range check
```nasm
SYS029F: 8BF4          MOV	SI,SP
SYS02A1: 36            SS:
SYS02A2: 8E4402        MOV	ES,[SI+02]
SYS02A5: 26            ES:
SYS02A6: 3B5502        CMP	DX,[DI+02]
SYS02A9: 7F07          JG	02B2
SYS02AB: 7C14          JL	02C1
SYS02AD: 26            ES:
SYS02AE: 3B05          CMP	AX,[DI]
SYS02B0: 720F          JB	02C1
SYS02B2: 26            ES:
SYS02B3: 3B5506        CMP	DX,[DI+06]
SYS02B6: 7C08          JL	02C0
SYS02B8: 7F07          JG	02C1
SYS02BA: 26            ES:
SYS02BB: 3B4504        CMP	AX,[DI+04]
SYS02BE: 7701          JA	02C1
SYS02C0: CB            RETF
```

## Range Error
```nasm
SYS02C1: B8C900        MOV	AX,00C9
SYS02C4: E948FE        JMP	010F
```

## Arithmetic Overflow Error
```nasm
SYS02C7: B8D700        MOV	AX,00D7
SYS02CA: E942FE        JMP	010F
```

## Check Stack
```nasm
SYS02CD: 050002        ADD	AX,0200
SYS02D0: 720D          JB	02DF
SYS02D2: 2BC4          SUB	AX,SP
SYS02D4: 7309          JNB	02DF
SYS02D6: F7D8          NEG	AX
SYS02D8: 3B063A00      CMP	AX,[StackLimit]
SYS02DC: 7201          JB	02DF
SYS02DE: CB            RETF
SYS02DF: B8CA00        MOV	AX,00CA
SYS02E2: E92AFE        JMP	010F
```

## Unused byte
```
SYS02E5:  BA
```

## Open File Function
```nasm
SYS02E6: 33D2          XOR	DX,DX
SYS02E8: 8BDC          MOV	BX,SP
SYS02EA: 1E            PUSH	DS
SYS02EB: 36            SS:
SYS02EC: C47F08        LES	DI,[BX+08]
SYS02EF: 36            SS:
SYS02F0: C57704        LDS	SI,[BX+04]
SYS02F3: FC            CLD
SYS02F4: 33C0          XOR	AX,AX
SYS02F6: AB            STOSW
SYS02F7: B8B0D7        MOV	AX,fmClosed
SYS02FA: AB            STOSW
SYS02FB: B88000        MOV	AX,0080
SYS02FE: AB            STOSW
SYS02FF: 33C0          XOR	AX,AX
SYS0301: AB            STOSW
SYS0302: AB            STOSW
SYS0303: AB            STOSW
SYS0304: 8D4574        LEA	AX,[DI+74]
SYS0307: AB            STOSW
SYS0308: 8CC0          MOV	AX,ES
SYS030A: AB            STOSW
SYS030B: B89904        MOV	AX,0499
SYS030E: AB            STOSW
SYS030F: B87507        MOV	AX,SYS
SYS0312: AB            STOSW
SYS0313: 33C0          XOR	AX,AX
SYS0315: B90E00        MOV	CX,000E
SYS0318: F3            REPZ
SYS0319: AB            STOSW
SYS031A: B94F00        MOV	CX,004F
SYS031D: 0BD2          OR	DX,DX
SYS031F: 7509          JNZ	032A
SYS0321: AC            LODSB
SYS0322: 3AC8          CMP	CL,AL
SYS0324: 7604          JBE	032A
SYS0326: 8AC8          MOV	CL,AL
SYS0328: E308          JCXZ	0332
SYS032A: AC            LODSB
SYS032B: 0AC0          OR	AL,AL
SYS032D: 7403          JZ	0332
SYS032F: AA            STOSB
SYS0330: E2F8          LOOP	032A
SYS0332: 32C0          XOR	AL,AL
SYS0334: AA            STOSB
SYS0335: 1F            POP	DS
SYS0336: CA0800        RETF	0008
```

## Unknown code block
```nasm
SYS0339: 8BDC          MOV	BX,SP
SYS033B: 36            SS:
SYS033C: C47F0A        LES	DI,[BX+0A]
SYS033F: 36            SS:
SYS0340: 8B4704        MOV	AX,[BX+04]
SYS0343: 26            ES:
SYS0344: 894504        MOV	[DI+04],AX
SYS0347: 36            SS:
SYS0348: 8B4706        MOV	AX,[BX+06]
SYS034B: 26            ES:
SYS034C: 89450C        MOV	[DI+0C],AX
SYS034F: 36            SS:
SYS0350: 8B4708        MOV	AX,[BX+08]
SYS0353: 26            ES:
SYS0354: 89450E        MOV	[DI+0E],AX
SYS0357: 33C0          XOR	AX,AX
SYS0359: 26            ES:
SYS035A: 894508        MOV	[DI+08],AX
SYS035D: 26            ES:
SYS035E: 89450A        MOV	[DI+0A],AX
SYS0361: CA0A00        RETF	000A
```

## Entry point for Reset
```nasm
SYS0364: BAB1D7        MOV	DX,fmInput
SYS0367: EB08          JMP	0371
```

## Entry point for Rewrite
```nasm
SYS0369: BAB2D7        MOV	DX,fmOutput
SYS036C: EB03          JMP	0371
```

## Common code for Reset/Rewrite
```nasm
SYS036E: BAB3D7        MOV	DX,fmInOut
SYS0371: 55            PUSH	BP
SYS0372: 8BEC          MOV	BP,SP
SYS0374: C47E06        LES	DI,[BP+06]
SYS0377: 26            ES:
SYS0378: 8B4502        MOV	AX,[DI:Mode]
SYS037B: 3DB1D7        CMP	AX,fmInput
SYS037E: 7412          JZ	0392
SYS0380: 3DB2D7        CMP	AX,fmOutput
SYS0383: 740D          JZ	0392
SYS0385: 3DB0D7        CMP	AX,fmClosed
SYS0388: 7410          JZ	039A
SYS038A: C7063C006600  MOV	WORD PTR [InOutRes],0066
SYS0390: EB24          JMP	03B6
SYS0392: 52            PUSH	DX
SYS0393: 06            PUSH	ES
SYS0394: 57            PUSH	DI
SYS0395: 0E            PUSH	CS
SYS0396: E82500        CALL	03BE
SYS0399: 5A            POP	DX
SYS039A: 33C0          XOR	AX,AX
SYS039C: 26            ES:
SYS039D: 895502        MOV	[DI:Mode],DX
SYS03A0: 26            ES:
SYS03A1: 894508        MOV	[DI:BufPos],AX
SYS03A4: 26            ES:
SYS03A5: 89450A        MOV	[DI:BufEnd],AX
```

## Open File
```nasm
SYS03A8: BB1000        MOV	BX,0010
SYS03AB: E84C00        CALL	03FA
SYS03AE: 7406          JZ	03B6
SYS03B0: 26            ES:
SYS03B1: C74502B0D7    MOV	WORD PTR [DI:Mode],fmClosed
SYS03B6: 5D            POP	BP
SYS03B7: CA0400        RETF	0004
```

## Entry point for I/O Function Call
```nasm
SYS03BA: B000          MOV	AL,00
SYS03BC: EB02          JMP	03C0
```

## Entry point for I/O Function Call
```nasm
SYS03BE: B001          MOV	AL,01
SYS03C0: 55            PUSH	BP
SYS03C1: 8BEC          MOV	BP,SP
SYS03C3: C47E06        LES	DI,[BP+06]
SYS03C6: 26            ES:
SYS03C7: 817D02B1D7    CMP	WORD PTR [DI:Mode],fmInput
SYS03CC: 7418          JZ	03E6
SYS03CE: 26            ES:
SYS03CF: 817D02B2D7    CMP	WORD PTR [DI:Mode],fmOutput
SYS03D4: 7408          JZ	03DE
SYS03D6: C7063C006700  MOV	WORD PTR [InOutRes],0067
SYS03DC: EB18          JMP	03F6
```

## Perform I/O
```nasm
SYS03DE: 50            PUSH	AX
SYS03DF: BB1400        MOV	BX,0014
SYS03E2: E81500        CALL	03FA
SYS03E5: 58            POP	AX
SYS03E6: 0AC0          OR	AL,AL
SYS03E8: 740C          JZ	03F6
```

## Close File
```nasm
SYS03EA: BB1C00        MOV	BX,001C
SYS03ED: E80A00        CALL	03FA
SYS03F0: 26            ES:
SYS03F1: C74502B0D7    MOV	WORD PTR [DI:Mode],fmClosed
SYS03F6: 5D            POP	BP
SYS03F7: CA0400        RETF	0004
```

## I/O Function Call Dispatcher
```nasm
SYS03FA: 06            PUSH	ES
SYS03FB: 57            PUSH	DI
SYS03FC: 06            PUSH	ES
SYS03FD: 57            PUSH	DI
SYS03FE: 26            ES:
SYS03FF: FF19          CALL	FAR [BX+DI]
SYS0401: 0BC0          OR	AX,AX
SYS0403: 7403          JZ	0408
SYS0405: A33C00        MOV	[InOutRes],AX
SYS0408: 5F            POP	DI
SYS0409: 07            POP	ES
SYS040A: C3            RET
```

## InOutFunc Handler (I)
```nasm
SYS040B: 8BDC          MOV	BX,SP
SYS040D: 1E            PUSH	DS
SYS040E: 36            SS:
SYS040F: C47F04        LES	DI,[BX+04]
SYS0412: 26            ES:
SYS0413: C5550C        LDS	DX,[DI:BufPtr]
SYS0416: 26            ES:
SYS0417: 8B4D04        MOV	CX,[DI:BufSize]
SYS041A: 26            ES:
SYS041B: 8B1D          MOV	BX,[DI:Handle]
SYS041D: B43F          MOV	AH,3F
SYS041F: CD21          INT	21
SYS0421: 7210          JB	0433
SYS0423: 26            ES:
SYS0424: 89450A        MOV	[DI:BufEnd],AX
SYS0427: 33C0          XOR	AX,AX
SYS0429: 26            ES:
SYS042A: C745080000    MOV	WORD PTR [DI:BufPos],0000
SYS042F: 1F            POP	DS
SYS0430: CA0400        RETF	0004
SYS0433: 26            ES:
SYS0434: C7450A0000    MOV	WORD PTR [DI:BufEnd],0000
SYS0439: EBEE          JMP	0429
```

## InOutFunc Handler (II)
```nasm
SYS043B: 8BDC          MOV	BX,SP
SYS043D: 1E            PUSH	DS
SYS043E: 36            SS:
SYS043F: C47F04        LES	DI,[BX+04]
SYS0442: 26            ES:
SYS0443: C5550C        LDS	DX,[DI:BufPtr]
SYS0446: 33C9          XOR	CX,CX
SYS0448: 26            ES:
SYS0449: 874D08        XCHG	CX,[DI:BufPos]
SYS044C: 26            ES:
SYS044D: 8B1D          MOV	BX,[DI:Handle]
SYS044F: B440          MOV	AH,40
SYS0451: CD21          INT	21
SYS0453: 7207          JB	045C
SYS0455: 2BC1          SUB	AX,CX
SYS0457: 7403          JZ	045C
SYS0459: B86500        MOV	AX,0065
SYS045C: 1F            POP	DS
SYS045D: CA0400        RETF	0004
```

## InOutFunc Handler (III)
```nasm
SYS0460: 8BDC          MOV	BX,SP
SYS0462: 1E            PUSH	DS
SYS0463: 36            SS:
SYS0464: C47F04        LES	DI,[BX+04]
SYS0467: 26            ES:
SYS0468: C5550C        LDS	DX,[DI:BufPtr]
SYS046B: 33C9          XOR	CX,CX
SYS046D: 26            ES:
SYS046E: 874D08        XCHG	CX,[DI:BufPos]
SYS0471: 26            ES:
SYS0472: 8B1D          MOV	BX,[DI:Handle]
SYS0474: B440          MOV	AH,40
SYS0476: CD21          INT	21
SYS0478: 7202          JB	047C
SYS047A: 33C0          XOR	AX,AX
SYS047C: 1F            POP	DS
SYS047D: CA0400        RETF	0004
```

## CloseFunc Handler
```nasm
SYS0480: 8BDC          MOV	BX,SP
SYS0482: 36            SS:
SYS0483: C47F04        LES	DI,[BX+04]
SYS0486: 26            ES:
SYS0487: 8B1D          MOV	BX,[DI:Handle]
SYS0489: 83FB04        CMP	BX,+04
SYS048C: 7606          JBE	0494
SYS048E: B43E          MOV	AH,3E
SYS0490: CD21          INT	21
SYS0492: 7202          JB	0496
SYS0494: 33C0          XOR	AX,AX
SYS0496: CA0400        RETF	0004
```

## OpenFunc Handler
```nasm
SYS0499: 8BDC          MOV	BX,SP
SYS049B: 1E            PUSH	DS
SYS049C: 36            SS:
SYS049D: C57F04        LDS	DI,[BX+04]
SYS04A0: 33C9          XOR	CX,CX
SYS04A2: 890D          MOV	[DI:Handle],CX
SYS04A4: B8003D        MOV	AX,3D00
SYS04A7: 817D02B1D7    CMP	WORD PTR [DI:Mode],fmInput
SYS04AC: 740D          JZ	04BB
SYS04AE: B002          MOV	AL,02
SYS04B0: FF05          INC	WORD PTR [DI:Handle]
SYS04B2: 817D02B3D7    CMP	WORD PTR [DI:Mode],fmInOut
SYS04B7: 7402          JZ	04BB
SYS04B9: B43C          MOV	AH,3C
SYS04BB: 807D3000      CMP	BYTE PTR [DI:Name],00
SYS04BF: 7409          JZ	04CA
SYS04C1: 8D5530        LEA	DX,[DI:Name]
SYS04C4: CD21          INT	21
SYS04C6: 725A          JB	0522
SYS04C8: 8905          MOV	[DI:Handle],AX
SYS04CA: B80B04        MOV	AX,040B
SYS04CD: BA7507        MOV	DX,SYS
SYS04D0: 33C9          XOR	CX,CX
SYS04D2: 33DB          XOR	BX,BX
SYS04D4: 817D02B1D7    CMP	WORD PTR [DI:Mode],fmInput
SYS04D9: 742F          JZ	050A
SYS04DB: 8B1D          MOV	BX,[DI:Handle]
SYS04DD: B80044        MOV	AX,4400
SYS04E0: CD21          INT	21
SYS04E2: F6C280        TEST	DL,80
SYS04E5: B86004        MOV	AX,0460
SYS04E8: BA7507        MOV	DX,SYS
SYS04EB: 8BC8          MOV	CX,AX
SYS04ED: 8BDA          MOV	BX,DX
SYS04EF: 7514          JNZ	0505
SYS04F1: 817D02B3D7    CMP	WORD PTR [DI:Mode],fmInOut
SYS04F6: 7503          JNZ	04FB
SYS04F8: E82B00        CALL	0526
SYS04FB: B83B04        MOV	AX,043B
SYS04FE: BA7507        MOV	DX,SYS
SYS0501: 33C9          XOR	CX,CX
SYS0503: 33DB          XOR	BX,BX
SYS0505: C74502B2D7    MOV	WORD PTR [DI:Mode],fmOutput
SYS050A: 894514        MOV	[DI:InOutFunc.Offset],AX
SYS050D: 895516        MOV	[DI:InOutFunc.Segment],DX
SYS0510: 894D18        MOV	[DI:FlushFunc.Offset],CX
SYS0513: 895D1A        MOV	[DI:FlushFunc.Segment],BX
SYS0516: C7451C8004    MOV	WORD PTR [DI:CloseFunc.Offset],0480
SYS051B: C7451E7507    MOV	WORD PTR [DI:CloseFunc.Segment],SYS
SYS0520: 33C0          XOR	AX,AX
SYS0522: 1F            POP	DS
SYS0523: CA0400        RETF	0004
```

## OpenFunc Handler: Seek
```nasm
SYS0526: 33D2          XOR	DX,DX
SYS0528: 33C9          XOR	CX,CX
SYS052A: 8B1D          MOV	BX,[DI:Handle]
SYS052C: B80242        MOV	AX,4202
SYS052F: CD21          INT	21
SYS0531: 2D8000        SUB	AX,0080
SYS0534: 83DA00        SBB	DX,+00
SYS0537: 7304          JNB	053D
SYS0539: 33C0          XOR	AX,AX
SYS053B: 33D2          XOR	DX,DX
SYS053D: 8BCA          MOV	CX,DX
SYS053F: 8BD0          MOV	DX,AX
SYS0541: 8B1D          MOV	BX,[DI:Handle]
SYS0543: B80042        MOV	AX,4200
SYS0546: CD21          INT	21
SYS0548: 8D958000      LEA	DX,[DI:TextBuf]
SYS054C: B98000        MOV	CX,0080
SYS054F: 8B1D          MOV	BX,[DI:Handle]
SYS0551: B43F          MOV	AH,3F
SYS0553: CD21          INT	21
SYS0555: 7302          JNB	0559
SYS0557: 33C0          XOR	AX,AX
SYS0559: 33DB          XOR	BX,BX
SYS055B: 3BD8          CMP	BX,AX
SYS055D: 7420          JZ	057F
SYS055F: 80B980001A    CMP	BYTE PTR [BX+DI:TextBuf],1A
SYS0564: 7403          JZ	0569
SYS0566: 43            INC	BX
SYS0567: EBF2          JMP	055B
SYS0569: 8BD3          MOV	DX,BX
SYS056B: 2BD0          SUB	DX,AX
SYS056D: B9FFFF        MOV	CX,FFFF
SYS0570: 8B1D          MOV	BX,[DI:Handle]
SYS0572: B80242        MOV	AX,4202
SYS0575: CD21          INT	21
SYS0577: 33C9          XOR	CX,CX
SYS0579: 8B1D          MOV	BX,[DI:Handle]
SYS057B: B440          MOV	AH,40
SYS057D: CD21          INT	21
SYS057F: C3            RET
```

```nasm
SYS0580: 81F70080      XOR	DI,8000
SYS0584: 0AC9          OR	CL,CL
SYS0586: 747B          JZ	0603
SYS0588: 0AC0          OR	AL,AL
SYS058A: 7478          JZ	0604
SYS058C: 3AC1          CMP	AL,CL
SYS058E: 7605          JBE	0595
SYS0590: 91            XCHG	CX,AX
SYS0591: 87DE          XCHG	BX,SI
SYS0593: 87D7          XCHG	DX,DI
SYS0595: 2AC1          SUB	AL,CL
SYS0597: F6D8          NEG	AL
SYS0599: 3C29          CMP	AL,29
SYS059B: 7367          JNB	0604
SYS059D: 86C1          XCHG	AL,CL
```

```nasm
SYS059F: 55            PUSH	BP
SYS05A0: 50            PUSH	AX
```

Save **BP** and **AX**.

```nasm
SYS05A1: 8AE6          MOV	AH,DH
SYS05A3: 80E480        AND	AH,80
SYS05A6: 8BE8          MOV	BP,AX
SYS05A8: 33C7          XOR	AX,DI
```

```nasm
SYS05AA: 58            POP	AX
```

Restore **AX** (saved in **SYS:05A0**).

```nasm
SYS05AB: 9C            PUSHF
SYS05AC: B000          MOV	AL,00
SYS05AE: 80CE80        OR	DH,80
SYS05B1: 81CF0080      OR	DI,8000
SYS05B5: 80F908        CMP	CL,08
SYS05B8: 7211          JB	05CB
SYS05BA: 8AC4          MOV	AL,AH
SYS05BC: 8AE3          MOV	AH,BL
SYS05BE: 8ADF          MOV	BL,BH
SYS05C0: 8AFA          MOV	BH,DL
SYS05C2: 8AD6          MOV	DL,DH
SYS05C4: 32F6          XOR	DH,DH
SYS05C6: 80E908        SUB	CL,08
SYS05C9: EBEA          JMP	05B5
```

```nasm
SYS05CB: 0AC9          OR	CL,CL
SYS05CD: 740A          JZ	05D9
```

## Adjust Number and Exponent
```nasm
SYS05CF: D1EA          SHR	DX,1
SYS05D1: D1DB          RCR	BX,1
SYS05D3: D1D8          RCR	AX,1
SYS05D5: FEC9          DEC	CL
```

This shifts the **MSW** in **DX** by one power of two (to the right) and adjust the exponent **CL**, i.e **DX**:**BX**:**AX** * 2^(**-1**), **CL** = **CL - 1**.

```nasm
SYS05D7: 75F6          JNZ	05CF
SYS05D9: 9D            POPF
SYS05DA: 7837          JS	0613
```

```nasm
SYS05DC: 03C1          ADD	AX,CX
SYS05DE: 13DE          ADC	BX,SI
SYS05E0: 13D7          ADC	DX,DI
```

Add **DI**:**SI**:**CX** to **DX**:**BX**:**AX** (handle all carries).

```nasm
SYS05E2: 8BCD          MOV	CX,BP
SYS05E4: 5D            POP	BP
```

Transfer **BP** to **CX** then restore **BP** (saved in **SYS:059F**).

```nasm
SYS05E5: 730A          JNB	05F1
```

Check if there was an overflow in **SYS:05E0** then adjust.

```nasm
SYS05E7: D1DA          RCR	DX,1
SYS05E9: D1DB          RCR	BX,1
SYS05EB: D1D8          RCR	AX,1
SYS05ED: FEC1          INC	CL
```

Shifts **DX**:**BX**:**AX** to the right once but compensate for it by increasing **CL**.

```nasm
SYS05EF: 7420          JZ	0611
```

If adjustment in **CL** causes the value to wrap around to 0, then return immediately with carry flag set (**SYS:0611**).

```nasm
SYS05F1: 058000        ADD	AX,0080
SYS05F4: 83D300        ADC	BX,+00
SYS05F7: 83D200        ADC	DX,+00
SYS05FA: 720F          JB	060B
```

Test if exponent in **AX** causes an overflow.

```nasm
SYS05FC: 8AC1          MOV	AL,CL
SYS05FE: 80E67F        AND	DH,7F
SYS0601: 0AF5          OR	DH,CH
SYS0603: C3            RET
```

Copy the adjusted exponent in **CL** to **AL**. Clear the sign bit in **DH** then copy over the adjusted **MSB** in **CH** to **DH**. Return.

```nasm
SYS0604: 8BC1          MOV	AX,CX
SYS0606: 8BDE          MOV	BX,SI
SYS0608: 8BD7          MOV	DX,DI
SYS060A: C3            RET
```

Copy the adjusted number in DI:SI:CX to **DX**:**BX**:**AX** then return.

## Adjust MSW and Exponent
```nasm
SYS060B: D1DA          RCR	DX,1
SYS060D: FEC1          INC	CL
```

This shifts the **MSW** in **DX** by one power of two (to the right) but compensates for it in **CL**, i.e **DX** * 2^(**CL - 1**) * 2^(**CL + 1**).

```nasm
SYS060F: 75EB          JNZ	05FC
SYS0611: F9            STC
SYS0612: C3            RET
```

If the result wraps **CL** back to **0**, then return with CF set (**SYS:0611**) otherwise continue to **SYS:05FC**.

```nasm
SYS0613: 2BC1          SUB	AX,CX
SYS0615: 1BDE          SBB	BX,SI
SYS0617: 1BD7          SBB	DX,DI
```

Subtract **DI**:**SI**:**CX** from **DX**:**BX**:**AX**.

```nasm
SYS0619: 8BCD          MOV	CX,BP
SYS061B: 5D            POP	BP
SYS061C: 7310          JNB	062E
SYS061E: F7D2          NOT	DX
SYS0620: F7D3          NOT	BX
SYS0622: F7D8          NEG	AX
SYS0624: F5            CMC
SYS0625: 83D300        ADC	BX,+00
SYS0628: 83D200        ADC	DX,+00
SYS062B: 80F580        XOR	CH,80
SYS062E: 8BFA          MOV	DI,DX
SYS0630: 0BFB          OR	DI,BX
SYS0632: 0BF8          OR	DI,AX
SYS0634: 74CD          JZ	0603
SYS0636: 0AF6          OR	DH,DH
SYS0638: 78B7          JS	05F1
```

```nasm
SYS063A: D1E0          SHL	AX,1
SYS063C: D1D3          RCL	BX,1
SYS063E: D1D2          RCL	DX,1
SYS0640: FEC9          DEC	CL
SYS0642: 75F2          JNZ	0636
```

Multiply **DX**:**BX**:**AX** 2 then decrease the exponent in **CL**. This has the same effect just shifting the significand but preserving the overall magnitiude of the number, i.e. ```(A * 2) * 2^-1```. If CL drops to zero, then set **DX**:**BX**:**AX** to 0 instead.

## Jump point en route to setting **DX**:**BX**:**AX** to zero

```nasm
SYS0644: E9FC00        JMP	0743
```

## Step 7b
This is step 7b of the conversion. At this point:
- **DI**:**SI**:**CX** holds the adjusted ***Real*** number obtained from **SYS:0C35-0C70**.
- **DX**:**BX**:**AX** holds the ***Real*** number being converted.

For the number we are trying to convert, following number is loaded into **DI**:**SI**:**CX** = **000000000081h**.

```nasm
SYS0647: 0AC0          OR	AL,AL
SYS0649: 74F9          JZ	0644
```

Return with **DX**:**BX**:**AX** = ***0*** if the **exponent** in **AL** is 0.

```nasm
SYS064B: 0AC9          OR	CL,CL
SYS064D: 74F5          JZ	0644
```

Return with **DX**:**BX**:**AX** = ***0*** if the **exponent** in **CL** is 0.

```nasm
SYS064F: 55            PUSH	BP
```

Save **BP** which keeps track of the parameters and immediate results as well as locations to the temporary buffers.

```nasm
SYS0650: 8BEA          MOV	BP,DX
```

Save **DX** into **BP**.

```nasm
SYS0652: 33D7          XOR	DX,DI
SYS0654: 81E20080      AND	DX,8000
```

Complement the sign bit in **DI** into **DX**. Clear all the bits except for the sign bit in **DX**.

```nasm
SYS0658: 86D0          XCHG	DL,AL
SYS065A: 02D1          ADD	DL,CL
SYS065C: 12F0          ADC	DH,AL
SYS065E: 8AC8          MOV	CL,AL
```

Copy the exponent in **AL** into **DL**. Add the exponent in **CL** to **DL**. Add the overflow in **SYS**:**065A** to **DH**. Clear **CL**.

```nasm
SYS0660: 81CD0080      OR	BP,8000
SYS0664: 81CF0080      OR	DI,8000
```

Set the sign bits of **BP** and **DI**.

```nasm
SYS0668: 52            PUSH	DX
```

Save **DX**.

```nasm
SYS0669: 0AE4          OR	AH,AH
SYS066B: 7504          JNZ	0671
SYS066D: 0BDB          OR	BX,BX
SYS066F: 740D          JZ	067E
SYS0671: 0AED          OR	CH,CH
SYS0673: 7526          JNZ	069B
SYS0675: 0BF6          OR	SI,SI
SYS0677: 7522          JNZ	069B
SYS0679: 91            XCHG	CX,AX
SYS067A: 87DE          XCHG	BX,SI
SYS067C: 87EF          XCHG	BP,DI
SYS067E: 8BC1          MOV	AX,CX
SYS0680: F7E5          MUL	BP
SYS0682: 8BDA          MOV	BX,DX
SYS0684: 8BC6          MOV	AX,SI
SYS0686: F7E5          MUL	BP
SYS0688: 03D8          ADD	BX,AX
SYS068A: 83D200        ADC	DX,+00
SYS068D: 8BCA          MOV	CX,DX
SYS068F: 8BC7          MOV	AX,DI
SYS0691: F7E5          MUL	BP
SYS0693: 03C1          ADD	AX,CX
SYS0695: 83D200        ADC	DX,+00
SYS0698: EB7C          JMP	0716
```

```nasm
SYS069A: 90            NOP
```

```nasm
SYS069B: 57            PUSH	DI
SYS069C: 56            PUSH	SI
SYS069D: 51            PUSH	CX
SYS069E: 55            PUSH	BP
SYS069F: 53            PUSH	BX
SYS06A0: 50            PUSH	AX
```

Save both numbers **DI**:**SI**:**CX** and **BP**:**BX**:**AX**.

### Stack after SYS:06A0
|Index|Contents|
|-----|--------|
|BP   |AX      |
|BP+02|BX      |
|BP+04|BP      |
|BP+06|CX      |
|BP+08|SI      |
|BP+0A|DI      |
|BP+0C|DI      |

```nasm
SYS06A1: 8BEC          MOV	BP,SP
```

Point **BP** to top of stack (**SP**).

```nasm
SYS06A3: 33C9          XOR	CX,CX
```

Clear **CX**.

```nasm
SYS06A5: 8A4601        MOV	AL,[BP+01]
```

Load **AH** (from the stack) into **AL**.

```nasm
SYS06A8: F66607        MUL	BYTE PTR [BP+07]
```

Multiply **CH** (from stack) to **AL** and store in **AL**.

```nasm
SYS06AB: 8BF0          MOV	SI,AX
SYS06AD: 8BF9          MOV	DI,CX
SYS06AF: 8BD9          MOV	BX,CX
```

Clear **BX**, **SI**, **DI** (**CX** = 0 from **SYS:06A3**).

```nasm
SYS06B1: 8B4600        MOV	AX,[BP+00]
SYS06B4: F76608        MUL	WORD PTR [BP+08]
SYS06B7: 03F0          ADD	SI,AX
SYS06B9: 13FA          ADC	DI,DX
SYS06BB: 13D9          ADC	BX,CX
SYS06BD: 8B4602        MOV	AX,[BP+02]
SYS06C0: F76606        MUL	WORD PTR [BP+06]
SYS06C3: 03F0          ADD	SI,AX
SYS06C5: 13FA          ADC	DI,DX
SYS06C7: 13D9          ADC	BX,CX
SYS06C9: 8BF1          MOV	SI,CX
SYS06CB: 8B4600        MOV	AX,[BP+00]
SYS06CE: F7660A        MUL	WORD PTR [BP+0A]
SYS06D1: 03F8          ADD	DI,AX
SYS06D3: 13DA          ADC	BX,DX
SYS06D5: 13F1          ADC	SI,CX
SYS06D7: 8B4602        MOV	AX,[BP+02]
SYS06DA: F76608        MUL	WORD PTR [BP+08]
SYS06DD: 03F8          ADD	DI,AX
SYS06DF: 13DA          ADC	BX,DX
SYS06E1: 13F1          ADC	SI,CX
SYS06E3: 8B4604        MOV	AX,[BP+04]
SYS06E6: F76606        MUL	WORD PTR [BP+06]
SYS06E9: 03F8          ADD	DI,AX
SYS06EB: 13DA          ADC	BX,DX
SYS06ED: 13F1          ADC	SI,CX
SYS06EF: 8BF9          MOV	DI,CX
SYS06F1: 8B4602        MOV	AX,[BP+02]
SYS06F4: F7660A        MUL	WORD PTR [BP+0A]
SYS06F7: 03D8          ADD	BX,AX
SYS06F9: 13F2          ADC	SI,DX
SYS06FB: 13F9          ADC	DI,CX
SYS06FD: 8B4604        MOV	AX,[BP+04]
SYS0700: F76608        MUL	WORD PTR [BP+08]
SYS0703: 03D8          ADD	BX,AX
SYS0705: 13F2          ADC	SI,DX
SYS0707: 13F9          ADC	DI,CX
SYS0709: 8B4604        MOV	AX,[BP+04]
SYS070C: F7660A        MUL	WORD PTR [BP+0A]
SYS070F: 03C6          ADD	AX,SI
SYS0711: 13D7          ADC	DX,DI
SYS0713: 83C40C        ADD	SP,+0C
SYS0716: 93            XCHG	BX,AX
SYS0717: 59            POP	CX
SYS0718: 5D            POP	BP
SYS0719: 0AF6          OR	DH,DH
SYS071B: 7807          JS	0724
SYS071D: D1E0          SHL	AX,1
SYS071F: D1D3          RCL	BX,1
SYS0721: D1D2          RCL	DX,1
SYS0723: 49            DEC	CX
SYS0724: 81E98180      SUB	CX,8081
SYS0728: 058000        ADD	AX,0080
SYS072B: 83D300        ADC	BX,+00
SYS072E: 83D200        ADC	DX,+00
SYS0731: 7303          JNB	0736
SYS0733: D1DA          RCR	DX,1
SYS0735: 41            INC	CX
SYS0736: F6C540        TEST	CH,40
SYS0739: 7508          JNZ	0743
SYS073B: 41            INC	CX
SYS073C: 8AC1          MOV	AL,CL
SYS073E: 32F5          XOR	DH,CH
SYS0740: D0ED          SHR	CH,1
SYS0742: C3            RET
```

## Clear number

```nasm
SYS0743: 33C0          XOR	AX,AX
SYS0745: 8BD8          MOV	BX,AX
SYS0747: 8BD0          MOV	DX,AX
SYS0749: C3            RET
```

## Step 7a

This is step 7a of the conversion. At this point:
- **DI**:**SI**:**CX** holds the adjusted ***Real*** number obtained from **SYS:0C35-0C70**.
- **DX**:**BX**:**AX** holds the ***Real*** number being converted.

```nasm
SYS074A: 0AC0          OR	AL,AL
SYS074C: 74F5          JZ	0743
```

Return with the number **DX**:**BX**:**AX** set to 0 if the exponent in **AL** is **0**.

```nasm
SYS074E: 55            PUSH	BP
SYS074F: 8BEA          MOV	BP,DX
SYS0751: 33D7          XOR	DX,DI
SYS0753: 81CF0080      OR	DI,8000
SYS0757: 81CD0080      OR	BP,8000
SYS075B: 81E20080      AND	DX,8000
SYS075F: 86C2          XCHG	AL,DL
SYS0761: 2AD1          SUB	DL,CL
SYS0763: 1AF0          SBB	DH,AL
SYS0765: 52            PUSH	DX
SYS0766: B002          MOV	AL,02
SYS0768: BA0100        MOV	DX,0001
SYS076B: 3BEF          CMP	BP,DI
SYS076D: 7506          JNZ	0775
SYS076F: 3BDE          CMP	BX,SI
SYS0771: 7502          JNZ	0775
SYS0773: 3AE5          CMP	AH,CH
SYS0775: 7206          JB	077D
SYS0777: 2AE5          SUB	AH,CH
SYS0779: 1BDE          SBB	BX,SI
SYS077B: 1BEF          SBB	BP,DI
SYS077D: D1D2          RCL	DX,1
SYS077F: 7211          JB	0792
SYS0781: D0E4          SHL	AH,1
SYS0783: D1D3          RCL	BX,1
SYS0785: D1D5          RCL	BP,1
SYS0787: 73E2          JNB	076B
SYS0789: 2AE5          SUB	AH,CH
SYS078B: 1BDE          SBB	BX,SI
SYS078D: 1BEF          SBB	BP,DI
SYS078F: F8            CLC
SYS0790: EBEB          JMP	077D
SYS0792: FEC8          DEC	AL
SYS0794: 780A          JS	07A0
SYS0796: 52            PUSH	DX
SYS0797: BA0100        MOV	DX,0001
SYS079A: 75E5          JNZ	0781
SYS079C: B240          MOV	DL,40
SYS079E: EBE1          JMP	0781
SYS07A0: 8BC2          MOV	AX,DX
SYS07A2: B106          MOV	CL,06
SYS07A4: D3E0          SHL	AX,CL
SYS07A6: 5B            POP	BX
SYS07A7: 5A            POP	DX
SYS07A8: 59            POP	CX
SYS07A9: 5D            POP	BP
SYS07AA: F7D0          NOT	AX
SYS07AC: F7D3          NOT	BX
SYS07AE: 83F2FF        XOR	DX,-01
SYS07B1: 7807          JS	07BA
SYS07B3: D1D0          RCL	AX,1
SYS07B5: D1D3          RCL	BX,1
SYS07B7: D1D2          RCL	DX,1
SYS07B9: 49            DEC	CX
SYS07BA: 81C18080      ADD	CX,8080
SYS07BE: E967FF        JMP	0728
SYS07C1: 52            PUSH	DX
SYS07C2: 33D7          XOR	DX,DI
SYS07C4: 5A            POP	DX
SYS07C5: 7905          JNS	07CC
SYS07C7: 52            PUSH	DX
SYS07C8: D1D2          RCL	DX,1
SYS07CA: 5A            POP	DX
SYS07CB: C3            RET
```

```nasm
SYS07CC: F6C680        TEST	DH,80
SYS07CF: 7407          JZ	07D8
SYS07D1: E80400        CALL	07D8
SYS07D4: 7414          JZ	07EA
SYS07D6: F5            CMC
SYS07D7: C3            RET
```

```nasm
SYS07D8: 3AC1          CMP	AL,CL
SYS07DA: 750E          JNZ	07EA
SYS07DC: 0AC0          OR	AL,AL
SYS07DE: 740A          JZ	07EA
SYS07E0: 3BD7          CMP	DX,DI
SYS07E2: 7506          JNZ	07EA
SYS07E4: 3BDE          CMP	BX,SI
SYS07E6: 7502          JNZ	07EA
SYS07E8: 3AE5          CMP	AH,CH
SYS07EA: C3            RET
```

```nasm
SYS07EB: 8BD8          MOV	BX,AX
SYS07ED: 0BDA          OR	BX,DX
SYS07EF: 7435          JZ	0826
SYS07F1: 8AEE          MOV	CH,DH
SYS07F3: 0BD2          OR	DX,DX
SYS07F5: 7907          JNS	07FE
SYS07F7: F7DA          NEG	DX
SYS07F9: F7D8          NEG	AX
SYS07FB: 83DA00        SBB	DX,+00
SYS07FE: 8BD8          MOV	BX,AX
SYS0800: B8A000        MOV	AX,00A0
SYS0803: 0BD2          OR	DX,DX
SYS0805: 750C          JNZ	0813
SYS0807: 87D3          XCHG	DX,BX
SYS0809: B090          MOV	AL,90
SYS080B: 0AF6          OR	DH,DH
SYS080D: 7504          JNZ	0813
SYS080F: 86F2          XCHG	DH,DL
SYS0811: B088          MOV	AL,88
SYS0813: 0BD2          OR	DX,DX
SYS0815: 7808          JS	081F
SYS0817: FEC8          DEC	AL
SYS0819: 03DB          ADD	BX,BX
SYS081B: 13D2          ADC	DX,DX
SYS081D: 79F8          JNS	0817
SYS081F: 0AED          OR	CH,CH
SYS0821: 7803          JS	0826
SYS0823: 80E67F        AND	DH,7F
SYS0826: C3            RET
```

```nasm
SYS0827: 93            XCHG	BX,AX
SYS0828: B1A0          MOV	CL,A0
SYS082A: 2ACB          SUB	CL,BL
SYS082C: 725B          JB	0889
SYS082E: 8ADE          MOV	BL,DH
SYS0830: 80CE80        OR	DH,80
SYS0833: 80F920        CMP	CL,20
SYS0836: 7352          JNB	088A
SYS0838: 80F910        CMP	CL,10
SYS083B: 7209          JB	0846
SYS083D: 8AFC          MOV	BH,AH
SYS083F: 8BC2          MOV	AX,DX
SYS0841: 33D2          XOR	DX,DX
SYS0843: 80E910        SUB	CL,10
SYS0846: 80F908        CMP	CL,08
SYS0849: 720D          JB	0858
SYS084B: 8AF8          MOV	BH,AL
SYS084D: 8AC4          MOV	AL,AH
SYS084F: 8AE2          MOV	AH,DL
SYS0851: 8AD6          MOV	DL,DH
SYS0853: 32F6          XOR	DH,DH
SYS0855: 80E908        SUB	CL,08
SYS0858: 0AC9          OR	CL,CL
SYS085A: 740A          JZ	0866
SYS085C: D1EA          SHR	DX,1
SYS085E: D1D8          RCR	AX,1
SYS0860: D0DF          RCR	BH,1
SYS0862: FEC9          DEC	CL
SYS0864: 75F6          JNZ	085C
SYS0866: 0AED          OR	CH,CH
SYS0868: 740A          JZ	0874
SYS086A: 02FF          ADD	BH,BH
SYS086C: 150000        ADC	AX,0000
SYS086F: 83D200        ADC	DX,+00
SYS0872: 7215          JB	0889
SYS0874: 8BC8          MOV	CX,AX
SYS0876: 0BCA          OR	CX,DX
SYS0878: 740F          JZ	0889
SYS087A: 0ADB          OR	BL,BL
SYS087C: 7907          JNS	0885
SYS087E: F7DA          NEG	DX
SYS0880: F7D8          NEG	AX
SYS0882: 83DA00        SBB	DX,+00
SYS0885: 32DE          XOR	BL,DH
SYS0887: 02DB          ADD	BL,BL
SYS0889: C3            RET
```

```nasm
SYS088A: 8AFE          MOV	BH,DH
SYS088C: B80000        MOV	AX,0000
SYS088F: BA0000        MOV	DX,0000
SYS0892: 74D2          JZ	0866
SYS0894: C3            RET
```

```nasm
SYS0895: 8BCF          MOV	CX,DI
SYS0897: BE0A00        MOV	SI,000A
SYS089A: 8BDA          MOV	BX,DX
SYS089C: 0BDB          OR	BX,BX
SYS089E: 7911          JNS	08B1
SYS08A0: F7DB          NEG	BX
SYS08A2: F7D8          NEG	AX
SYS08A4: 83DB00        SBB	BX,+00
SYS08A7: E80700        CALL	08B1
SYS08AA: 4F            DEC	DI
SYS08AB: 26            ES:
SYS08AC: C6052D        MOV	BYTE PTR [DI],2D
SYS08AF: 41            INC	CX
SYS08B0: C3            RET
```

```nasm
SYS08B1: 33D2          XOR	DX,DX
SYS08B3: 93            XCHG	BX,AX
SYS08B4: F7F6          DIV	SI
SYS08B6: 93            XCHG	BX,AX
SYS08B7: F7F6          DIV	SI
SYS08B9: 80C230        ADD	DL,30
SYS08BC: 80FA3A        CMP	DL,3A
SYS08BF: 7203          JB	08C4
SYS08C1: 80C207        ADD	DL,07
SYS08C4: 4F            DEC	DI
SYS08C5: 26            ES:
SYS08C6: 8815          MOV	[DI],DL
SYS08C8: 8BD0          MOV	DX,AX
SYS08CA: 0BD3          OR	DX,BX
SYS08CC: 75E3          JNZ	08B1
SYS08CE: 2BCF          SUB	CX,DI
SYS08D0: C3            RET
```

```nasm
SYS08D1: 33C0          XOR	AX,AX
SYS08D3: 33D2          XOR	DX,DX
SYS08D5: 33F6          XOR	SI,SI
SYS08D7: E35D          JCXZ	0936
SYS08D9: 26            ES:
SYS08DA: 803D2B        CMP	BYTE PTR [DI],2B
SYS08DD: 7407          JZ	08E6
SYS08DF: 26            ES:
SYS08E0: 803D2D        CMP	BYTE PTR [DI],2D
SYS08E3: 7505          JNZ	08EA
SYS08E5: 4E            DEC	SI
SYS08E6: 47            INC	DI
SYS08E7: 49            DEC	CX
SYS08E8: 744C          JZ	0936
SYS08EA: 26            ES:
SYS08EB: 803D24        CMP	BYTE PTR [DI],24
SYS08EE: 7448          JZ	0938
SYS08F0: 26            ES:
SYS08F1: 8A1D          MOV	BL,[DI]
SYS08F3: 80EB3A        SUB	BL,3A
SYS08F6: 80C30A        ADD	BL,0A
SYS08F9: 7325          JNB	0920
SYS08FB: F6C6F0        TEST	DH,F0
SYS08FE: 7536          JNZ	0936
SYS0900: 53            PUSH	BX
SYS0901: D1E0          SHL	AX,1
SYS0903: D1D2          RCL	DX,1
SYS0905: 52            PUSH	DX
SYS0906: 50            PUSH	AX
SYS0907: D1E0          SHL	AX,1
SYS0909: D1D2          RCL	DX,1
SYS090B: D1E0          SHL	AX,1
SYS090D: D1D2          RCL	DX,1
SYS090F: 5B            POP	BX
SYS0910: 03C3          ADD	AX,BX
SYS0912: 5B            POP	BX
SYS0913: 13D3          ADC	DX,BX
SYS0915: 5B            POP	BX
SYS0916: 32FF          XOR	BH,BH
SYS0918: 03C3          ADD	AX,BX
SYS091A: 83D200        ADC	DX,+00
SYS091D: 47            INC	DI
SYS091E: E2D0          LOOP	08F0
SYS0920: 8BD8          MOV	BX,AX
SYS0922: 0BDA          OR	BX,DX
SYS0924: 740F          JZ	0935
SYS0926: 0BF6          OR	SI,SI
SYS0928: 7907          JNS	0931
SYS092A: F7DA          NEG	DX
SYS092C: F7D8          NEG	AX
SYS092E: 83DA00        SBB	DX,+00
SYS0931: 33F2          XOR	SI,DX
SYS0933: 7801          JS	0936
SYS0935: C3            RET
```

```nasm
SYS0936: F9            STC
SYS0937: C3            RET
```

```nasm
SYS0938: 47            INC	DI
SYS0939: 49            DEC	CX
SYS093A: 74FA          JZ	0936
SYS093C: 26            ES:
SYS093D: 8A1D          MOV	BL,[DI]
SYS093F: 80FB61        CMP	BL,61
SYS0942: 7203          JB	0947
SYS0944: 80EB20        SUB	BL,20
SYS0947: 80EB3A        SUB	BL,3A
SYS094A: 80C30A        ADD	BL,0A
SYS094D: 720B          JB	095A
SYS094F: 80EB17        SUB	BL,17
SYS0952: 80C306        ADD	BL,06
SYS0955: 73C9          JNB	0920
SYS0957: 80C30A        ADD	BL,0A
SYS095A: B704          MOV	BH,04
SYS095C: D1E0          SHL	AX,1
SYS095E: D1D2          RCL	DX,1
SYS0960: 72D4          JB	0936
SYS0962: FECF          DEC	BH
SYS0964: 75F6          JNZ	095C
SYS0966: 0AC3          OR	AL,BL
SYS0968: 47            INC	DI
SYS0969: E2D1          LOOP	093C
SYS096B: 0BF6          OR	SI,SI
SYS096D: 7907          JNS	0976
SYS096F: F7DA          NEG	DX
SYS0971: F7D8          NEG	AX
SYS0973: 83DA00        SBB	DX,+00
SYS0976: F8            CLC
SYS0977: C3            RET
```

## Conversion - Step 2

This is step 2 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0978: 55            PUSH	BP
SYS0979: 8BEC          MOV	BP,SP
SYS097B: 83EC14        SUB	SP,+14
```

Reserve **14h/20** bytes in the stack and setup BP as index to the items on the stack.

### Stack after SYS:097B
|Index|Contents                   |
| :-: |---------------------------|
|BP-14|(SP Points here)           |
|BP-06|Adjusted Precision/Exponent|
|BP-04|MSB                        |
|BP-02|Precision/Exponent         |
|BP+00|Old BP                     |

```nasm
SYS097E: 57            PUSH	DI
```

Preserve **DI**.

```nasm
SYS097F: 83F90B        CMP	CX,+0B
SYS0982: 7E03          JLE	0987
SYS0984: B90B00        MOV	CX,000B
SYS0987: 83F9F5        CMP	CX,-0B
SYS098A: 7D03          JGE	098F
SYS098C: B9F5FF        MOV	CX,FFF5
```

Limits the precision to **000Bh**/**12** (or **FFF5h**/**-12**).

```nasm
SYS098F: 894EFE        MOV	[BP-02],CX
SYS0992: 8876FC        MOV	[BP-04],DH
```

Record the final **precision** in **SS**:[**BP-02**] and the numbers' **MSB** (most significant byte) in **SS**:[**BP-04**].

```nasm
SYS0995: 06            PUSH	ES
SYS0996: 57            PUSH	DI
```

Once more, **ES**:**DI** onto the stack.

```nasm
SYS0997: 8D7EEC        LEA	DI,[BP-14]
SYS099A: 16            PUSH	SS
SYS099B: 07            POP	ES
```

Point **ES**:**DI** to the 20 bytes reserved earlier (at **SS**:[**BP-14**]).

```nasm
SYS099C: E8D100        CALL	0A70
```

Proceed to the next step of the conversion (at **SYS:0A70**).

```nasm
SYS099F: 5F            POP	DI
SYS09A0: 07            POP	ES
```

Restore **ES**:**DI** (saved in **SYS**:**0995**)

## Step 9 - Round off

This routine will perform any rounding-off on the converted digits stored **SS**:[**BP-14**]

```nasm
SYS09A1: 894EFA        MOV	[BP-06],CX
```

Save adjusted precision in **CX** to stack without modifying **BP**.

```nasm
SYS09A4: 8B76FE        MOV	SI,[BP-02]
```

Retrieve original **precision** stored previously on the stack (saved in **SYS:098F**).

```nasm
SYS09A7: 0BF6          OR	SI,SI
SYS09A9: 780C          JS	09B7
```

Check if **SI** is signed / negative.

```nasm
SYS09AB: 0376FA        ADD	SI,[BP-06]
SYS09AE: 46            INC	SI
SYS09AF: 7908          JNS	09B9
SYS09B1: C646EC00      MOV	BYTE PTR [BP-14],00
SYS09B5: EB2E          JMP	09E5
SYS09B7: F7DE          NEG	SI
SYS09B9: 83FE0C        CMP	SI,+0C
SYS09BC: 7203          JB	09C1
```

### Round off up to **0Bh**/**11** digits
```nasm
SYS09BE: BE0B00        MOV	SI,000B
```

Move pointer (in **SI**) to end of the string.

```nasm
SYS09C1: 807AEC35      CMP	BYTE PTR [BP+SI-14],35
SYS09C5: C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS09C9: 721A          JB	09E5
```

Check if digit is below **'5'**/**35h**.

```nasm
SYS09CB: 4E            DEC	SI
```

Move pointer **SI** one digit to one byte to the left (of the string).

```nasm
SYS09CC: 780F          JS	09DD
```

Check if the sign (of **SI**) changes, i.e. if it is already on the first digit (after the decimal point).

```nasm
SYS09CE: FE42EC        INC	BYTE PTR [BP+SI-14]
```

Round up the digit at this location.

```nasm
SYS09D1: 807AEC39      CMP	BYTE PTR [BP+SI-14],39
SYS09D5: 760E          JBE	09E5
SYS09D7: C642EC00      MOV	BYTE PTR [BP+SI-14],00
SYS09DB: EBEE          JMP	09CB
```

Check if the digit is below or equal to **'9'**/**39h**. If the value of the byte at this location is greater than **'9'** mark it as **NULL** then repeat the operation. Otherwise, the round up operation is complete.

```nasm
SYS09DD: C746EC3100    MOV	WORD PTR [BP-14],0031
SYS09E2: FF46FA        INC	WORD PTR [BP-06]
```

Round up the number to **1**. Increase the precision/exponent in **SS**:[**BP-06**].

```nasm
SYS09E5: 33F6          XOR	SI,SI
SYS09E7: FC            CLD
```

Reset the index (in **SI**) to 0.

```nasm
SYS09E8: 8B56FE        MOV	DX,[BP-02]
SYS09EB: 0BD2          OR	DX,DX
SYS09ED: 7835          JS	0A24
```

Retrieve the exponent from **SS**:[**BP-02**] and check if it is signed.

```nasm
SYS09EF: F646FC80      TEST	BYTE PTR [BP-04],80
SYS09F3: 7403          JZ	09F8
SYS09F5: B02D          MOV	AL,2D
SYS09F7: AA            STOSB
SYS09F8: 8B4EFA        MOV	CX,[BP-06]
SYS09FB: 0BC9          OR	CX,CX
SYS09FD: 7905          JNS	0A04
SYS09FF: B030          MOV	AL,30
SYS0A01: AA            STOSB
SYS0A02: EB07          JMP	0A0B
SYS0A04: E85D00        CALL	0A64
SYS0A07: AA            STOSB
SYS0A08: 49            DEC	CX
SYS0A09: 79F9          JNS	0A04
SYS0A0B: 0BD2          OR	DX,DX
SYS0A0D: 744C          JZ	0A5B
SYS0A0F: B02E          MOV	AL,2E
SYS0A11: AA            STOSB
SYS0A12: 41            INC	CX
SYS0A13: 7406          JZ	0A1B
SYS0A15: B030          MOV	AL,30
SYS0A17: AA            STOSB
SYS0A18: 4A            DEC	DX
SYS0A19: 75F7          JNZ	0A12
SYS0A1B: 4A            DEC	DX
SYS0A1C: 783D          JS	0A5B
SYS0A1E: E84300        CALL	0A64
SYS0A21: AA            STOSB
SYS0A22: EBF7          JMP	0A1B
```

```nasm
SYS0A24: B020          MOV	AL,20
SYS0A26: F646FC80      TEST	BYTE PTR [BP-04],80
SYS0A2A: 7402          JZ	0A2E
```

Check if the number is negative (using the **MSB** stored at **SS**:[**BP-04**]). If it is positive, set a whitespace (20h) character in **AL**.

```nasm
SYS0A2C: B02D          MOV	AL,2D
```

The number is negative, set a **'-'**/**2Dh** character in **AL** instead.

```nasm
SYS0A2E: AA            STOSB
```

Store the sign in output buffer buffer.

```nasm
SYS0A2F: E83200        CALL	0A64
SYS0A32: AA            STOSB
```

Retrieve next digit and store in buffer (on the call to **SYS:0A64**).

```nasm
SYS0A33: 42            INC	DX
SYS0A34: 740A          JZ	0A40
```

Increase character count **DX** and check if all the digits (before the decimal point) have been stored in **ES**:**DI**.

```nasm
SYS0A36: B02E          MOV	AL,2E
SYS0A38: AA            STOSB
```

Store the decimal point **'.'**/**2Eh**.

```nasm
SYS0A39: E82800        CALL	0A64
SYS0A3C: AA            STOSB
SYS0A3D: 42            INC	DX
SYS0A3E: 75F9          JNZ	0A39
```

Retrieve the next digits then store it.

```nasm
SYS0A40: B045          MOV	AL,45
SYS0A42: AA            STOSB
```

Store the **'E'**/**45h** or character (for the scientific notation).

```nasm
SYS0A43: B02B          MOV	AL,2B
SYS0A45: 8B56FA        MOV	DX,[BP-06]
SYS0A48: 0BD2          OR	DX,DX
SYS0A4A: 7904          JNS	0A50
```

Prepare the **'+'**/**2B** character. Retrieve the exponent in **DX** from **SS**:[**BP-06**]. Test whetherthe exponent is positive.

```nasm
SYS0A4C: B02D          MOV	AL,2D
SYS0A4E: F7DA          NEG	DX
```

Preepare **'+'**/**2D** character and negate the exponent in **DX** because the exponent was negative.

```nasm
SYS0A50: AA            STOSB
```

Store this sign at the oputput buffer.

```nasm
SYS0A51: 8BC2          MOV	AX,DX
SYS0A53: B20A          MOV	DL,0A
SYS0A55: F6FA          IDIV	DL
```

Move the exponent to **AX**. Convert the exponent in **AL** by doing a signed division of **AL** by **DL**. The quotient will be stored in **AL** while the remainder is stored in **AH**.

```nasm
SYS0A57: 053030        ADD	AX,3030
```

Convert both bytes of the result in **AX** into ASCII.

```nasm
SYS0A5A: AB            STOSW
```

Store **AX** into the output buffer. Because the remainder is in **AX**, storing the word will actually store **AL** first then followed by **AH** next.

```nasm
SYS0A5B: 8BCF          MOV	CX,DI
SYS0A5D: 5F            POP	DI
SYS0A5E: 2BCF          SUB	CX,DI
```

Compute for the number of characters rendered in **CX** by computing for **CX-DI**.

```nasm
SYS0A60: 8BE5          MOV	SP,BP
```

Restore SP (modified in **SYS:097B**).

```nasm
SYS0A62: 5D            POP	BP
SYS0A63: C3            RET
```

Restore **BP** then return.

### Retrieve next character from buffer
```nasm
SYS0A64: 8A42EC        MOV	AL,[BP+SI-14]
SYS0A67: 46            INC	SI
SYS0A68: 0AC0          OR	AL,AL
SYS0A6A: 7503          JNZ	0A6F
SYS0A6C: B030          MOV	AL,30
SYS0A6E: 4E            DEC	SI
SYS0A6F: C3            RET
```

This subroutine retrieves one byte from the buffer and store it into **AL**. If it is empty, it store **'0'**/**30h** into **AL** instead. The index to the buffer is moved to the next location.

## Conversion - Step 3

This is step 3 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0A70: 0AC0          OR	AL,AL
SYS0A72: 750D          JNZ	0A81
```

Proceed to the next step (at **SYS:0A81**) if the exponent is non-zero.

```nasm
SYS0A74: B90600        MOV	CX,0006
SYS0A77: B83030        MOV	AX,3030
SYS0A7A: FC            CLD
SYS0A7B: F3            REPZ
SYS0A7C: AB            STOSW
SYS0A7D: 32C0          XOR	AL,AL
SYS0A7F: AA            STOSB
SYS0A80: C3            RET
```

Since the exponent is zero, as per the rules of the [***Real***-data type](REAL-TYPE.md), the value is zero. This entire section of the code fills the buffer with 12 **0**'s (ASCII **30h**).

## Conversion - Step 4

This is step 4 the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0A81: 80E67F        AND	DH,7F
```

Clear the sign flag (bit 7 of the number's **MSB**).

```nasm
SYS0A84: 50            PUSH	AX
```

Save the exponent in **AX** onto the stack.

```nasm
SYS0A85: 2C80          SUB	AL,80
SYS0A87: B44D          MOV	AH,4D
SYS0A89: F6EC          IMUL	AH
SYS0A8B: 050500        ADD	AX,0005
SYS0A8E: 8AC4          MOV	AL,AH
SYS0A90: 98            CBW
SYS0A91: 8BC8          MOV	CX,AX
```

Calculate needed adjustments to the exponent into **CX**.

```nasm
SYS0A93: 58            POP	AX
```

Restore **AX** to the saved value (**exponent**)  in the stack.

```nasm
SYS0A94: 83F9D9        CMP	CX,-27
SYS0A97: 7501          JNZ	0A9A
SYS0A99: 41            INC	CX
```

Check if CX needs further adjustment (if equal to **FFD9h**/**-27**).

```nasm
SYS0A9A: 51            PUSH	CX
SYS0A9B: 57            PUSH	DI
```

Save **CX** and **DI**.

```nasm
SYS0A9C: F7D9          NEG	CX
```

Reverse the sign of **CX**.

```nasm
SYS0A9E: E84401        CALL	0BE5
```

Proceed to the next step in the conversion (at **SYS:0BE5**).

```nasm
SYS0AA1: 5F            POP	DI
SYS0AA2: 59            POP	CX
```

Restore **CX** and **DI** (saved in **SYS**:**0A9A**).

```nasm
SYS0AA3: 3C81          CMP	AL,81
SYS0AA5: 7304          JNB	0AAB
SYS0AA7: E8C701        CALL	0C71
SYS0AAA: 49            DEC	CX
```

If the number needs further adjustment call **SYS**:**0C71** then decrease the **precsion**.

```nasm
SYS0AAB: 51            PUSH	CX
```

Save **CX** (precision).

## Step 8 - Conversion to ASCII

### Conversion to ASCII (I)

This routine is the entry point for the actual conversion to ASCII. At this point, **DX**:**BX**:**AX** is in the normalized form after undergoing previous adjustments.

```nasm
SYS0AAC: 80CE80        OR	DH,80
```

Set the sign-bit (**bit 7**) in **DH**.

```nasm
SYS0AAF: B184          MOV	CL,84
SYS0AB1: 2AC8          SUB	CL,AL
SYS0AB3: B000          MOV	AL,00
SYS0AB5: 740A          JZ	0AC1
```

Check if **DX**:**BX**:**AX** needs to be shifted right based on the exponent in **AL**. The amount of shifts are computed and stored in **CL**. Shifting will only be done on the significand (excluding **AL**, hence it is cleared prior).

```nasm
SYS0AB7: D1EA          SHR	DX,1
SYS0AB9: D1DB          RCR	BX,1
SYS0ABB: D1D8          RCR	AX,1
SYS0ABD: FEC9          DEC	CL
SYS0ABF: 75F6          JNZ	0AB7
```

This shifts the number **DX**:**BX**:**AH**:**00h** to the right one bit at a time.

### Conversion to ASCII (II)

This routine does the actual conversion to ASCII. At this point **DX**:**BX**:**AH:00h** contains the final normalized form of the the number (without the exponent). In the final normalized form, the upper bits (bit **4-7**) of **DH** should already contain the first 'digit' in base 10.

```nasm
SYS0AC1: BE0C00        MOV	SI,000C
```

Convert up to **0Ch**/**12** digits.

```nasm
SYS0AC4: 8AEE          MOV	CH,DH
SYS0AC6: B104          MOV	CL,04
SYS0AC8: D2ED          SHR	CH,CL
```

Get the upper bits of the number (in **DH**) into **CH**.

```nasm
SYS0ACA: 80C530        ADD	CH,30
SYS0ACD: 26            ES:
SYS0ACE: 882D          MOV	[DI],CH
```

Convert the base digit in **CH** to ASCII by adding it to '**0**'/**30h**.

```nasm
SYS0AD0: 80E60F        AND	DH,0F
```

Clear the upper bits of the number.

```nasm
SYS0AD3: 52            PUSH	DX
SYS0AD4: 53            PUSH	BX
SYS0AD5: 50            PUSH	AX
SYS0AD6: D1E0          SHL	AX,1
SYS0AD8: D1D3          RCL	BX,1
SYS0ADA: D1D2          RCL	DX,1
SYS0ADC: D1E0          SHL	AX,1
SYS0ADE: D1D3          RCL	BX,1
SYS0AE0: D1D2          RCL	DX,1
SYS0AE2: 59            POP	CX
SYS0AE3: 03C1          ADD	AX,CX
SYS0AE5: 59            POP	CX
SYS0AE6: 13D9          ADC	BX,CX
SYS0AE8: 59            POP	CX
SYS0AE9: 13D1          ADC	DX,CX
SYS0AEB: D1E0          SHL	AX,1
SYS0AED: D1D3          RCL	BX,1
SYS0AEF: D1D2          RCL	DX,1
```

This entire sequence multiplies the number by **10**. It does this first by pushing all the current number on to the stack. In detail:

|Step         |Effect                                                                               |
|-------------|-------------------------------------------------------------------------------------|
|```A' = A``` |Save number on the stack                                                             |
|```A *= 4``` |Multiply by 4 (shift left x 2 with **SHL/RCL** to carry over bits), i.e. ```A = 4A```|
|```A += A'```|Add the saved number using **ADD/ADC** to carry over the bits, i.e. ```A = 4A + A``` |
|```A *= 2``` |Multiply by 2 shifting to the left once, i.e. ```A = 2(4A + A) = 10A```              |

This is done in this manner so that the multiplication is done in place instead of using **MUL**/**IMUL** then preserving and swapping in and out of **DX** and **AX** regulary. It is more efficient this way.

```nasm
SYS0AF1: 47            INC	DI
SYS0AF2: 4E            DEC	SI
SYS0AF3: 75CF          JNZ	0AC4
```

Move the buffer pointer (**DI**) then decrease the count (**SI**). Repeat the process until all digits have been converted.

```nasm
SYS0AF5: 26            ES:
SYS0AF6: C60500        MOV	BYTE PTR [DI],00
```

**NULL** (**00h**) terminate the ASCII string conversion.

```nasm
SYS0AF9: 59            POP	CX
```

Restore **CX** (saved in **SYS**:**0AAB**).

```nasm
SYS0AFA: C3            RET
```

Return.

## String Operation (I)
```nasm
SYS0AFB: 55            PUSH	BP
SYS0AFC: 8BEC          MOV	BP,SP
SYS0AFE: 83EC08        SUB	SP,+08
SYS0B01: 33C0          XOR	AX,AX
SYS0B03: 8946F8        MOV	[BP-08],AX
SYS0B06: 8946FA        MOV	[BP-06],AX
SYS0B09: 8946FC        MOV	[BP-04],AX
SYS0B0C: E361          JCXZ	0B6F
SYS0B0E: 26            ES:
SYS0B0F: 8A05          MOV	AL,[DI]
SYS0B11: 8846FE        MOV	[BP-02],AL
SYS0B14: 3C20          CMP	AL,20
SYS0B16: 7408          JZ	0B20
SYS0B18: 3C2B          CMP	AL,2B
SYS0B1A: 7404          JZ	0B20
SYS0B1C: 3C2D          CMP	AL,2D
SYS0B1E: 7502          JNZ	0B22
SYS0B20: 47            INC	DI
SYS0B21: 49            DEC	CX
SYS0B22: E88200        CALL	0BA7
SYS0B25: 7248          JB	0B6F
SYS0B27: 0BDB          OR	BX,BX
SYS0B29: 740A          JZ	0B35
SYS0B2B: 33DB          XOR	BX,BX
SYS0B2D: E317          JCXZ	0B46
SYS0B2F: 26            ES:
SYS0B30: 803D2E        CMP	BYTE PTR [DI],2E
SYS0B33: 7511          JNZ	0B46
SYS0B35: E338          JCXZ	0B6F
SYS0B37: 26            ES:
SYS0B38: 803D2E        CMP	BYTE PTR [DI],2E
SYS0B3B: 7532          JNZ	0B6F
SYS0B3D: 47            INC	DI
SYS0B3E: 49            DEC	CX
SYS0B3F: E86500        CALL	0BA7
SYS0B42: 722B          JB	0B6F
SYS0B44: F7DB          NEG	BX
SYS0B46: E32A          JCXZ	0B72
SYS0B48: 26            ES:
SYS0B49: 8A05          MOV	AL,[DI]
SYS0B4B: 3C45          CMP	AL,45
SYS0B4D: 7404          JZ	0B53
SYS0B4F: 3C65          CMP	AL,65
SYS0B51: 751F          JNZ	0B72
SYS0B53: 47            INC	DI
SYS0B54: 49            DEC	CX
SYS0B55: 53            PUSH	BX
SYS0B56: E878FD        CALL	08D1
SYS0B59: 5B            POP	BX
SYS0B5A: 7213          JB	0B6F
SYS0B5C: 03D8          ADD	BX,AX
SYS0B5E: 8BF2          MOV	SI,DX
SYS0B60: 99            CWD
SYS0B61: 3BF2          CMP	SI,DX
SYS0B63: 750A          JNZ	0B6F
SYS0B65: 3D4000        CMP	AX,0040
SYS0B68: 7D05          JGE	0B6F
SYS0B6A: 3DC0FF        CMP	AX,FFC0
SYS0B6D: 7F03          JG	0B72
SYS0B6F: F9            STC
SYS0B70: EB31          JMP	0BA3
SYS0B72: 51            PUSH	CX
SYS0B73: 57            PUSH	DI
SYS0B74: 8ACB          MOV	CL,BL
SYS0B76: 8B46F8        MOV	AX,[BP-08]
SYS0B79: 8B5EFA        MOV	BX,[BP-06]
SYS0B7C: 8B56FC        MOV	DX,[BP-04]
SYS0B7F: 80F9DC        CMP	CL,DC
SYS0B82: 7D0A          JGE	0B8E
SYS0B84: 51            PUSH	CX
SYS0B85: B1DC          MOV	CL,DC
SYS0B87: E85B00        CALL	0BE5
SYS0B8A: 59            POP	CX
SYS0B8B: 80C124        ADD	CL,24
SYS0B8E: E85400        CALL	0BE5
SYS0B91: 5F            POP	DI
SYS0B92: 59            POP	CX
SYS0B93: 720E          JB	0BA3
SYS0B95: 0AC0          OR	AL,AL
SYS0B97: 740A          JZ	0BA3
SYS0B99: 807EFE2D      CMP	BYTE PTR [BP-02],2D
SYS0B9D: F8            CLC
SYS0B9E: 7503          JNZ	0BA3
SYS0BA0: 80CE80        OR	DH,80
SYS0BA3: 8BE5          MOV	SP,BP
SYS0BA5: 5D            POP	BP
SYS0BA6: C3            RET
```

```nasm
SYS0BA7: 33DB          XOR	BX,BX
SYS0BA9: E339          JCXZ	0BE4
SYS0BAB: 26            ES:
SYS0BAC: 8A05          MOV	AL,[DI]
SYS0BAE: 2C3A          SUB	AL,3A
SYS0BB0: 040A          ADD	AL,0A
SYS0BB2: 7330          JNB	0BE4
SYS0BB4: 43            INC	BX
SYS0BB5: 47            INC	DI
SYS0BB6: 49            DEC	CX
SYS0BB7: 53            PUSH	BX
SYS0BB8: 51            PUSH	CX
SYS0BB9: 57            PUSH	DI
SYS0BBA: 98            CBW
SYS0BBB: 99            CWD
SYS0BBC: E82CFC        CALL	07EB
SYS0BBF: 8BC8          MOV	CX,AX
SYS0BC1: 8BF3          MOV	SI,BX
SYS0BC3: 8BFA          MOV	DI,DX
SYS0BC5: 8B46F8        MOV	AX,[BP-08]
SYS0BC8: 8B5EFA        MOV	BX,[BP-06]
SYS0BCB: 8B56FC        MOV	DX,[BP-04]
SYS0BCE: E8A000        CALL	0C71
SYS0BD1: 720C          JB	0BDF
SYS0BD3: E8AEF9        CALL	0584
SYS0BD6: 8946F8        MOV	[BP-08],AX
SYS0BD9: 895EFA        MOV	[BP-06],BX
SYS0BDC: 8956FC        MOV	[BP-04],DX
SYS0BDF: 5F            POP	DI
SYS0BE0: 59            POP	CX
SYS0BE1: 5B            POP	BX
SYS0BE2: 73C5          JNB	0BA9
SYS0BE4: C3            RET
```

## Conversion - Step 5

This is step 5 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** adjusted exponent.

```nasm
SYS0BE5: 80F9DA        CMP	CL,DA
SYS0BE8: 7C49          JL	0C33
SYS0BEA: 80F926        CMP	CL,26
SYS0BED: 7F44          JG	0C33
```

Return immediately if adjusted exponent is not in the range **DAh**/**-38** <= **CX** <= **26h**/**38**.

```nasm
SYS0BEF: 52            PUSH	DX
SYS0BF0: 53            PUSH	BX
SYS0BF1: 50            PUSH	AX
```

Save the number.

```nasm
SYS0BF2: 0AC9          OR	CL,CL
SYS0BF4: 9C            PUSHF
```

Check exponent (**CL**) then save the results (in **Flags**) to stack.

```nasm
SYS0BF5: 7902          JNS	0BF9
SYS0BF7: F6D9          NEG	CL
```

If exponent is negative then make it positive (reverse the sign).

```nasm
SYS0BF9: 8AD9          MOV	BL,CL
SYS0BFB: 80E3FC        AND	BL,FC
```

Copy the exponent in **BL** and clear bits 0-1.

```nasm
SYS0BFE: 8AFB          MOV	BH,BL
SYS0C00: D0EB          SHR	BL,1
```

Copy **BL** to **BH** then shift **BL** to the right once.

```nasm
SYS0C02: 02DF          ADD	BL,BH
SYS0C04: 32FF          XOR	BH,BH
SYS0C06: 8DBF350C      LEA	DI,[BX+0C35]
```

Calculate lookup index to number table in **SYS:0C35**.

```nasm
SYS0C0A: 2E            CS:
SYS0C0B: 8B05          MOV	AX,[DI]
SYS0C0D: 2E            CS:
SYS0C0E: 8B5D02        MOV	BX,[DI+02]
SYS0C11: 2E            CS:
SYS0C12: 8B5504        MOV	DX,[DI+04]
```

Load ***Real*** number in **ES**:[**DI**] to **DX**:**BX**:**AX**.

```nasm
SYS0C15: 80E103        AND	CL,03
SYS0C18: 7407          JZ	0C21
```

Clear top bits (2-7) of the exponent in **CL**. Check if **DX**:**BX**:**AX** requires adjustment (based on the exponent in **CL**).

```nasm
SYS0C1A: E85400        CALL	0C71
```

If adjustments are needed go to step 6 (**SYS:0C71**) and do so.

```nasm
SYS0C1D: FEC9          DEC	CL
SYS0C1F: 75F9          JNZ	0C1A
```

Keep adjusting while **CL** is non-zero.

```nasm
SYS0C21: 8BC8          MOV	CX,AX
SYS0C23: 8BF3          MOV	SI,BX
SYS0C25: 8BFA          MOV	DI,DX
```

Copy the number in **DX**:**BX**:**AX** to **DI**:**SI**:**CX**.

```nasm
SYS0C27: 9D            POPF
```

Restore flags.

```nasm
SYS0C28: 58            POP	AX
SYS0C29: 5B            POP	BX
SYS0C2A: 5A            POP	DX
```

Restore the number **DX**:**BX**:**AX**

```nasm
SYS0C2B: 7803          JS	0C30
```

Check the result (that was saved in **SYS:0BF4**). If the exponent is a negative, proceed to step 7a on **SYS:074A**

```nasm
SYS0C2D: E917FA        JMP	0647
```

Otherwise proceed to step 7b in **SYS:0647**.

```nasm
SYS0C30: E917FB        JMP	074A
```

Jump to step 7a in **SYS:074A**.

## Number out of range
```nasm
SYS0C33: F9            STC
SYS0C34: C3            RET
```

## Data Block (SYS:0C35-0C70)

Table of numbers
```
SYS0C35:  81 00 00 00 00 00
SYS0C3B:  8E 00 00 00 40 1C
SYS0C41:  9B 00 00 20 BC 3E
SYS0C47:  A8 00 10 A5 D4 68
SYS0C4D:  B6 04 BF C9 1B 0E
SYS0C53:  C3 AC C5 EB 78 2D
SYS0C59:  D0 CD CE 1B C2 53
SYS0C5F:  DE F9 78 39 3F 01
SYS0C65:  EB 2B A8 AD C5 1D
SYS0C6B:  F8 C9 7B CE 97 40
```

## Conversion - Step 6 (Conversion Adjustments)

This is step 6 of the conversion. At this point:
- **DX**:**BX**:**AX** holds the ***Real*** number being converted

```nasm
SYS0C71: 0AC0          OR	AL,AL
SYS0C73: 7449          JZ	0CBE
```

Return immediately if exponent (in **AL**) is ***0***.

```nasm
SYS0C75: 51            PUSH	CX
SYS0C76: 56            PUSH	SI
```

Save **CX** and **SI**.

```nasm
SYS0C77: 80CE80        OR	DH,80
```

Set the sign bit of the **MSB** (bit 7 in **DH**).

```nasm
SYS0C7A: 8AC8          MOV	CL,AL
```

Copy the exponent in **AL** to **CL**.

```nasm
SYS0C7C: 32C0          XOR	AL,AL
```

Clear exponent in **AL**.

```nasm
SYS0C7E: 52            PUSH	DX
SYS0C7F: 53            PUSH	BX
SYS0C80: 50            PUSH	AX
SYS0C81: D1EA          SHR	DX,1
SYS0C83: D1DB          RCR	BX,1
SYS0C85: D1D8          RCR	AX,1
SYS0C87: D1EA          SHR	DX,1
SYS0C89: D1DB          RCR	BX,1
SYS0C8B: D1D8          RCR	AX,1
SYS0C8D: 5E            POP	SI
SYS0C8E: 03C6          ADD	AX,SI
SYS0C90: 5E            POP	SI
SYS0C91: 13DE          ADC	BX,SI
SYS0C93: 5E            POP	SI
SYS0C94: 13D6          ADC	DX,SI
```

This entire sequence adjusts the number by scaling **DX**:**BX**:**AX** by **5/4** or (**1.25**).

```nasm
SYS0C96: 730B          JNB	0CA3
```

Check if further adjustments are needed on an overflow.

```nasm
SYS0C98: D1DA          RCR	DX,1
SYS0C9A: D1DB          RCR	BX,1
SYS0C9C: D1D8          RCR	AX,1
SYS0C9E: 80C101        ADD	CL,01
SYS0CA1: 7219          JB	0CBC
```

Shift **DX**:**BX**:**AX** to the right by 1 then increment exponent in **CL**. If this results in a overflow in the exponent, return immediately.

```nasm
SYS0CA3: 058000        ADD	AX,0080
SYS0CA6: 83D300        ADC	BX,+00
SYS0CA9: 83D200        ADC	DX,+00
SYS0CAC: 7307          JNB	0CB5
```

Bias the exponent in **AL**. Check if further adjustments are needed on overflow.

```nasm
SYS0CAE: D1DA          RCR	DX,1
SYS0CB0: 80C101        ADD	CL,01
SYS0CB3: 7207          JB	0CBC
```

Shift **DX** to the right by 1 then increment the exponent in **CL**. If this causes an overflow then return immediately.

```nasm
SYS0CB5: 80E67F        AND	DH,7F
SYS0CB8: 8AC1          MOV	AL,CL
SYS0CBA: 0403          ADD	AL,03
```

If there were no adjustments after **SYS:0CA9** (or **SYS:0CB0**), then the net effect of this code is to actually multiply **DX**:**BX**:**AX** by **10** (**5/4** in the **significand**, **2**^**3**/**8** at the **exponent** in **AL**).

```nasm
SYS0CBC: 5E            POP	SI
SYS0CBD: 59            POP	CX
SYS0CBE: C3            RET
```

## Conversion - Step 1 (Entry Point)

This is the entrypoint to **Str**(***Real***, ***String***).

```nasm
SYS0CBF: 55            PUSH	BP
SYS0CC0: 8BEC          MOV	BP,SP
SYS0CC2: 83EC40        SUB	SP,+40
```

Reserve **40h**/**64** bytes on the stack. **BP** will be used as index to the items on the stack.

### Stack after SYS:0CC2

|Index|Contents                 |
| :-: |-------------------------|
|BP-40|(SP Points here)         |
|BP+00|Old BP                   |
|BP+02|Return Address (Offset)  |
|BP+04|Return Address (Segment) |
|BP+06|Buffer length (00FFh)    |
|BP+08|Output buffer (Offset)   |
|BP+0A|Output buffer (Segment)  |
|BP+0C|**Precision** (FFFFh)    |
|BP+0E|**Width** (0011h)        |
|BP+10|Low Word of *Real*       |
|BP+12|Mid Word of *Real*       |
|BP+14|High Word of *Real*      |

```nasm
SYS0CC5: 8B4610        MOV	AX,[BP+10]
SYS0CC8: 8B5E12        MOV	BX,[BP+12]
SYS0CCB: 8B5614        MOV	DX,[BP+14]
```

Load the ***Real*** number from the stack into **DX**:**BX**:**AX**.

```nasm
SYS0CCE: 8B4E0C        MOV	CX,[BP+0C]
```

Load precision from the stack into **CX**.

```nasm
SYS0CD1: 0BC9          OR	CX,CX
SYS0CD3: 790E          JNS	0CE3
```

If **CX** is a positive number, then the precision is fixed, proceed to the next step of the conversion.

```nasm
SYS0CD5: B90600        MOV	CX,0006
SYS0CD8: 2B4E0E        SUB	CX,[BP+0E]
SYS0CDB: 83F9FE        CMP	CX,-02
SYS0CDE: 7E03          JLE	0CE3
SYS0CE0: B9FEFF        MOV	CX,FFFE
```

This checks if the desired precision (= **FFFFh** max) compared to the width of the representation is enough. It sets the minimum to **2** (**FFFEh/-02**) digits.

```nasm
SYS0CE3: 8D7EC0        LEA	DI,[BP-40]
SYS0CE6: 16            PUSH	SS
SYS0CE7: 07            POP	ES
SYS0CE8: E88DFC        CALL	0978
```

Point **ES**:**DI** to the temporary buffer reserved earlier (**SS**:[**BP-40**]). Move to the next part of the conversion (**SYS:0978**).

```nasm
SYS0CEB: 1E            PUSH	DS
SYS0CEC: 8BF7          MOV	SI,DI
SYS0CEE: 16            PUSH	SS
SYS0CEF: 1F            POP	DS
```

Set **DS**:**SI** to the temporary buffer (**SS**[**BP-40**]).

```nasm
SYS0CF0: C47E08        LES	DI,[BP+08]
```

Set **ES**:**DI** to the **Output buffer** (passed on the stack on the call to **Str** function).

```nasm
SYS0CF3: 8B5606        MOV	DX,[BP+06]
```

Load the **Output buffer** length into **DX**.

```nasm
SYS0CF6: 8B460E        MOV	AX,[BP+0E]
```

Load the **Width** parameter (passed on the stack on the call to **Str** function) into **AX**.

```nasm
SYS0CF9: 3BC2          CMP	AX,DX
SYS0CFB: 7E02          JLE	0CFF
SYS0CFD: 8BC2          MOV	AX,DX
SYS0CFF: 3BCA          CMP	CX,DX
SYS0D01: 7E02          JLE	0D05
SYS0D03: 8BCA          MOV	CX,DX
SYS0D05: 3BC1          CMP	AX,CX
SYS0D07: 7D02          JGE	0D0B
SYS0D09: 8BC1          MOV	AX,CX
```

Compute for **AX** (string length of the conversion) and **CX** (precision).

```nasm
SYS0D0B: FC            CLD
SYS0D0C: AA            STOSB
```

Since **Output buffer** is a Pascal-string, store the length (**AL**) into the first byte (**Byte 0**).

```nasm
SYS0D0D: 2BC1          SUB	AX,CX
SYS0D0F: 7408          JZ	0D19
```

Check if there are digits to copy. If there is none, fill **Output buffer** **NULL** (**00h**) on **SYS**:**0D19**.

```nasm
SYS0D11: 51            PUSH	CX
SYS0D12: 8BC8          MOV	CX,AX
```

Preserve **CX** then copy the width in **AX** (less **CX** in **SYS**:**0D0D**).

```nasm
SYS0D14: B020          MOV	AL,20
SYS0D16: F3            REPZ
SYS0D17: AA            STOSB
```

Left-pad the **Output buffer** (fill with whitespace character **20h**).

```nasm
SYS0D18: 59            POP	CX
SYS0D19: F3            REPZ
SYS0D1A: A4            MOVSB
```

Restore **CX** (precision of the string conversion) then copy the digits from **DS**:**SI** to **ES**:**DI**.

```nasm
SYS0D1B: 1F            POP	DS
```

Restore **DS** (saved in **SYS**:**0CEB**).

```nasm
SYS0D1C: 8BE5          MOV	SP,BP
```

Restore **SP** (modified in **SYS**:**0CC2**). This *"removes"* any space it has reserved on the stack.

```nasm
SYS0D1E: 5D            POP	BP
SYS0D1F: CA1000        RETF	0010
```

Restore BP and pop off **10h**/**16** bytes from the stack (**8** parameters) upon return.

## String operation (II)
```nasm
SYS0D22: 55            PUSH	BP
SYS0D23: 8BEC          MOV	BP,SP
SYS0D25: C47E0A        LES	DI,[BP+0A]
SYS0D28: 26            ES:
SYS0D29: 8A0D          MOV	CL,[DI]
SYS0D2B: 32ED          XOR	CH,CH
SYS0D2D: 47            INC	DI
SYS0D2E: E309          JCXZ	0D39
SYS0D30: 26            ES:
SYS0D31: 803D20        CMP	BYTE PTR [DI],20
SYS0D34: 7503          JNZ	0D39
SYS0D36: 47            INC	DI
SYS0D37: E2F7          LOOP	0D30
SYS0D39: E8BFFD        CALL	0AFB
SYS0D3C: 7202          JB	0D40
SYS0D3E: E30B          JCXZ	0D4B
SYS0D40: 8BCF          MOV	CX,DI
SYS0D42: 2B4E0A        SUB	CX,[BP+0A]
SYS0D45: 33C0          XOR	AX,AX
SYS0D47: 33DB          XOR	BX,BX
SYS0D49: 33D2          XOR	DX,DX
SYS0D4B: C47E06        LES	DI,[BP+06]
SYS0D4E: 26            ES:
SYS0D4F: 890D          MOV	[DI],CX
SYS0D51: 5D            POP	BP
SYS0D52: CA0800        RETF	0008
```

## Clear Data
```nasm
SYS0D55: BF5000        MOV	DI,0050
SYS0D58: 1E            PUSH	DS
SYS0D59: 07            POP	ES
SYS0D5A: B9A203        MOV	CX,SizeOf(DATA)
SYS0D5D: 2BCF          SUB	CX,DI
SYS0D5F: D1E9          SHR	CX,1
SYS0D61: 33C0          XOR	AX,AX
SYS0D63: FC            CLD
SYS0D64: F3            REPZ
SYS0D65: AB            STOSW
SYS0D66: C3            RET
```

Go [Back](../../README.md)