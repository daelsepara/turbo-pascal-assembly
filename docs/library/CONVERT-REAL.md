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
CODE0000: CALL SYS:0000
CODE0005: PUSH BP
CODE0006: MOV BP,SP
CODE0008: XOR AX,AX
CODE000A: CALL SYS:02CD
CODE000F: MOV WORD PTR [A.Low],9282
CODE0015: MOV WORD PTR [A.Mid],DAA2
CODE001B: MOV WORD PTR [A.High],490F
CODE0021: PUSH [A.High]
CODE0025: PUSH [A.Mid]
CODE0029: PUSH [A.Low]
CODE002D: MOV AX,0011
CODE0030: PUSH AX
CODE0031: MOV AX,FFFF
CODE0034: PUSH AX
CODE0035: MOV DI,OFFSET B
CODE0038: PUSH DS
CODE0039: PUSH DI
CODE003A: MOV AX,00FF
CODE003D: PUSH AX
CODE003E: CALL SYS:0CBF
CODE0043: POP BP
CODE0044: XOR AX,AX
CODE0046: CALL SYS:0116
```

## Assignment

Assign a ***Real*** value to **A**.

```pascal
A := 0.3141592654E01;
```

Compiles to:

```nasm
CODE000F: MOV WORD PTR [A.Low],9282
CODE0015: MOV WORD PTR [A.Mid],DAA2
CODE001B: MOV WORD PTR [A.High],490F
```

## Conversion to String

Call on Pascal **Str()** function to convert ***Real*** variable **A** to a ***string*** in **B**.

```pascal
Str(A, B);
```

Compiles to:

```nasm
CODE0021: PUSH [A.High]
CODE0025: PUSH [A.Mid]
CODE0029: PUSH [A.Low]
CODE002D: MOV AX,0011
CODE0030: PUSH AX
CODE0031: MOV AX,FFFF
CODE0034: PUSH AX
CODE0035: MOV DI,OFFSET B
CODE0038: PUSH DS
CODE0039: PUSH DI
CODE003A: MOV AX,00FF
CODE003D: PUSH AX
CODE003E: CALL SYS:0CBF
```

## Initialize System Library
```nasm
SYS0000: MOV DX,DATA
SYS0003: MOV DS,DX
SYS0005: MOV [PrefixSeg],ES
SYS0009: XOR BP,BP
SYS000B: CALL 0D55
SYS000E: CALL 00B1
SYS0011: MOV AX,SP
SYS0013: ADD AX,0013
SYS0016: MOV CL,04
SYS0018: SHR AX,CL
SYS001A: MOV DX,SS
SYS001C: ADD AX,DX
SYS001E: MOV [OvrHeapOrg],AX
SYS0021: MOV [OvrHeapPtr],AX
SYS0024: ADD AX,[OvrHeapSize]
SYS0028: MOV [OvrHeapEnd],AX
SYS002B: MOV [HeapOrg.Segment],AX
SYS002E: MOV [HeapPtr.Segment],AX
SYS0031: MOV [FreeList.Segment],AX
SYS0034: MOV ES,[PrefixSeg]
SYS0038: ES:
SYS0039: MOV AX,[PrefixSeg:0002]
SYS003C: MOV [HeapEnd],AX
SYS003F: MOV WORD PTR [HeapError.Offset],00D6
SYS0045: MOV [HeapError.Segment],CS
```

## Save Interrupt Vectors
```nasm
SYS0049: MOV DI,OFFSET SaveInt00
SYS004C: MOV SI,0239
SYS004F: MOV CX,0013
SYS0052: NOP
SYS0053: CLD
SYS0054: CS:
SYS0055: LODSB
SYS0056: MOV AH,35
SYS0058: INT 21
SYS005A: MOV [DI],BX
SYS005C: MOV [DI+02],ES
SYS005F: ADD DI,+04
SYS0062: LOOP 0053
```

## Modify Interrupt Vectors
```nasm
SYS0064: PUSH DS
SYS0065: PUSH CS
SYS0066: POP DS
SYS0067: MOV DX,010C
SYS006A: MOV AX,2500
SYS006D: INT 21
SYS006F: MOV DX,0113
SYS0072: MOV AX,2523
SYS0075: INT 21
SYS0077: MOV DX,00DB
SYS007A: MOV AX,2524
SYS007D: INT 21
SYS007F: MOV DX,0104
SYS0082: MOV AX,253F
SYS0085: INT 21
```

## Input and Output File/Device Initialization
```nasm
SYS0087: POP DS
SYS0088: MOV AX,OFFSET Input
SYS008B: PUSH DS
SYS008C: PUSH AX
SYS008D: PUSH DS
SYS008E: PUSH AX
SYS008F: MOV AX,0263
SYS0092: PUSH CS
SYS0093: PUSH AX
SYS0094: PUSH CS
SYS0095: CALL 02E6
SYS0098: PUSH CS
SYS0099: CALL 0364
SYS009C: MOV AX,OFFSET Output
SYS009F: PUSH DS
SYS00A0: PUSH AX
SYS00A1: PUSH DS
SYS00A2: PUSH AX
SYS00A3: MOV AX,0263
SYS00A6: PUSH CS
SYS00A7: PUSH AX
SYS00A8: PUSH CS
SYS00A9: CALL 02E6
SYS00AC: PUSH CS
SYS00AD: CALL 0369
SYS00B0: RETF
```

## Determine CPU type
```nasm
SYS00B1: XOR AX,AX
SYS00B3: PUSHF
SYS00B4: POP BX
SYS00B5: AND BH,0F
SYS00B8: PUSH BX
SYS00B9: POPF
SYS00BA: PUSHF
SYS00BB: POP CX
SYS00BC: AND CH,F0
SYS00BF: CMP CH,F0
SYS00C2: JZ 00D2
SYS00C4: INC AX
SYS00C5: OR BH,F0
SYS00C8: PUSH BX
SYS00C9: POPF
SYS00CA: PUSHF
SYS00CB: POP CX
SYS00CC: AND CH,F0
SYS00CF: JZ 00D2
SYS00D1: INC AX
SYS00D2: MOV [Test8086],AL
SYS00D5: RET
```

## Heap Error Handler
```nasm
SYS00D6: XOR AX,AX
SYS00D8: RETF 0002
```

## INT 24h Critical Error Handler
```nasm
SYS00DB: STI
SYS00DC: ADD SP,+06
SYS00DF: POP AX
SYS00E0: AND DI,+1F
SYS00E3: ADD DI,0096
SYS00E7: CMP AH,39
SYS00EA: JNB 00EF
SYS00EC: MOV DI,FFFF
SYS00EF: PUSH DI
SYS00F0: MOV AH,54
SYS00F2: INT 21
SYS00F4: MOV BP,SP
SYS00F6: OR BYTE PTR [BP+16],01
SYS00FA: POP AX
SYS00FB: POP BX
SYS00FC: POP CX
SYS00FD: POP DX
SYS00FE: POP SI
SYS00FF: POP DI
SYS0100: POP BP
SYS0101: POP DS
SYS0102: POP ES
SYS0103: IRET
```

## INT 3Fh Overlay Manager Handler
```nasm
SYS0104: MOV AX,00D0
SYS0107: ADD SP,+06
SYS010A: JMP 010F
```

## INT 00h Handler
```nasm
SYS010C: MOV AX,00C8
SYS010F: POP CX
SYS0110: POP BX
SYS0111: JMP 011A
```

## INT 23h CTRL-C Handler and Exit Routine Entry Point
```nasm
SYS0113: MOV AX,00FF
SYS0116: XOR CX,CX
SYS0118: XOR BX,BX
SYS011A: MOV DX,DATA
SYS011D: MOV DS,DX
SYS011F: STI
SYS0120: MOV [ExitCode],AX
SYS0123: MOV AX,CX
SYS0125: OR AX,BX
SYS0127: JZ 0166
SYS0129: MOV AX,[OvrLoadList]
SYS012C: OR AX,AX
SYS012E: JZ 015F
SYS0130: MOV ES,AX
SYS0132: ES:
SYS0133: MOV AX,[OvrLoadList]
SYS0136: OR AX,AX
SYS0138: JZ 0155
SYS013A: SUB AX,BX
SYS013C: JA 0155
SYS013E: NEG AX
SYS0140: CMP AX,1000
SYS0143: JNB 0155
SYS0145: MOV DX,0010
SYS0148: MUL DX
SYS014A: ADD AX,CX
SYS014C: JB 0155
SYS014E: ES:
SYS014F: CMP AX,[OvrDebugPtr.Segment]
SYS0153: JB 015B
SYS0155: ES:
SYS0156: MOV AX,[OvrEmsHandle]
SYS0159: JMP 012C
SYS015B: MOV CX,AX
SYS015D: MOV BX,ES
SYS015F: SUB BX,[PrefixSeg]
SYS0163: SUB BX,+10
SYS0166: MOV [ErrorAddr.Offset],CX
SYS016A: MOV [ErrorAddr.Segment],BX
SYS016E: LES BX,[ExitProc]
SYS0172: MOV AX,ES
SYS0174: OR AX,BX
SYS0176: JZ 018B
SYS0178: XOR AX,AX
SYS017A: MOV [ExitProc.Offset],AX
SYS017D: MOV [ExitProc.Segment],AX
SYS0180: MOV [InOutRes],AX
SYS0183: MOV AX,016E
SYS0186: PUSH CS
SYS0187: PUSH AX
SYS0188: PUSH ES
SYS0189: PUSH BX
SYS018A: RETF
```

## Flush Input and Output File/Device
```nasm
SYS018B: MOV AX,OFFSET Input
SYS018E: PUSH DS
SYS018F: PUSH AX
SYS0190: PUSH CS
SYS0191: CALL 03BE
SYS0194: MOV AX,OFFSET Output
SYS0197: PUSH DS
SYS0198: PUSH AX
SYS0199: PUSH CS
SYS019A: CALL 03BE
```

## Restore Interrupt Vectors
```nasm
SYS019D: MOV DI,OFFSET SaveInt00
SYS01A0: MOV SI,0239
SYS01A3: MOV CX,0013
SYS01A6: NOP
SYS01A7: CLD
SYS01A8: CS:
SYS01A9: LODSB
SYS01AA: MOV AH,25
SYS01AC: PUSH DS
SYS01AD: LDS DX,[DI]
SYS01AF: INT 21
SYS01B1: POP DS
SYS01B2: ADD DI,+04
SYS01B5: LOOP 01A7
```

## Exit
```nasm
SYS01B7: MOV AX,[ErrorAddr.Offset]
SYS01BA: OR AX,[ErrorAddr.Segment]
SYS01BE: JZ 01E9
SYS01C0: MOV BX,024C
SYS01C3: CALL 01F0
SYS01C6: MOV AX,[ExitCode]
SYS01C9: CALL 01FE
SYS01CC: MOV BX,025B
SYS01CF: CALL 01F0
SYS01D2: MOV AX,[ErrorAddr.Segment]
SYS01D5: CALL 0218
SYS01D8: MOV AL,3A
SYS01DA: CALL 0232
SYS01DD: MOV AX,[ErrorAddr.Offset]
SYS01E0: CALL 0218
SYS01E3: MOV BX,0260
SYS01E6: CALL 01F0
SYS01E9: MOV AX,[ExitCode]
SYS01EC: MOV AH,4C
SYS01EE: INT 21
```

## Print string directly to console
```nasm
SYS01F0: CS:
SYS01F1: MOV AL,[BX]
SYS01F3: OR AL,AL
SYS01F5: JZ 01FD
SYS01F7: CALL 0232
SYS01FA: INC BX
SYS01FB: JMP 01F0
SYS01FD: RET
```

## Print character/digit directly to console
```nasm
SYS01FE: MOV CL,64
SYS0200: CALL 020A
SYS0203: MOV CL,0A
SYS0205: CALL 020A
SYS0208: JMP 020E
SYS020A: XOR AH,AH
SYS020C: DIV CL
SYS020E: ADD AL,30
SYS0210: PUSH AX
SYS0211: CALL 0232
SYS0214: POP AX
SYS0215: MOV AL,AH
SYS0217: RET
SYS0218: PUSH AX
SYS0219: MOV AL,AH
SYS021B: CALL 021F
SYS021E: POP AX
SYS021F: PUSH AX
SYS0220: MOV CL,04
SYS0222: SHR AL,CL
SYS0224: CALL 022A
SYS0227: POP AX
SYS0228: AND AL,0F
SYS022A: ADD AL,30
SYS022C: CMP AL,3A
SYS022E: JB 0232
SYS0230: ADD AL,07
SYS0232: MOV DL,AL
SYS0234: MOV AH,06
SYS0236: INT 21
SYS0238: RET
```

## SYS:0239 Data Block (Interrupt list)
```
SYS0230:                 00 02 1B 21 23 24 34
SYS0240:  35 36 37 38 39 3A 3B 3C-3D 3E 3F 75
```

## SYS:024C Runtime error strings
```
SYS024C:                                      52 75 6E 74               Runt
SYS0250:  69 6D 65 20 65 72 72 6F-72 20 00 20 61 74 20 00   ime error   at  
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
SYS028A: XOR AX,AX
SYS028C: XCHG AX,[InOutRes]
SYS0290: RETF
```

## Check I/O Result
```nasm
SYS0291: CMP WORD PTR [InOutRes],+00
SYS0296: JNZ 0299
SYS0298: RETF
SYS0299: MOV AX,[InOutRes]
SYS029C: JMP 010F
```

## Range check
```nasm
SYS029F: MOV SI,SP
SYS02A1: SS:
SYS02A2: MOV ES,[SI+02]
SYS02A5: ES:
SYS02A6: CMP DX,[DI+02]
SYS02A9: JG 02B2
SYS02AB: JL 02C1
SYS02AD: ES:
SYS02AE: CMP AX,[DI]
SYS02B0: JB 02C1
SYS02B2: ES:
SYS02B3: CMP DX,[DI+06]
SYS02B6: JL 02C0
SYS02B8: JG 02C1
SYS02BA: ES:
SYS02BB: CMP AX,[DI+04]
SYS02BE: JA 02C1
SYS02C0: RETF
```

## Range Error
```nasm
SYS02C1: MOV AX,00C9
SYS02C4: JMP 010F
```

## Arithmetic Overflow Error
```nasm
SYS02C7: MOV AX,00D7
SYS02CA: JMP 010F
```

## Check Stack
```nasm
SYS02CD: ADD AX,0200
SYS02D0: JB 02DF
SYS02D2: SUB AX,SP
SYS02D4: JNB 02DF
SYS02D6: NEG AX
SYS02D8: CMP AX,[StackLimit]
SYS02DC: JB 02DF
SYS02DE: RETF
SYS02DF: MOV AX,00CA
SYS02E2: JMP 010F
```

## Unused byte
```
SYS02E5:  BA
```

## Open File Function
```nasm
SYS02E6: XOR DX,DX
SYS02E8: MOV BX,SP
SYS02EA: PUSH DS
SYS02EB: SS:
SYS02EC: LES DI,[BX+08]
SYS02EF: SS:
SYS02F0: LDS SI,[BX+04]
SYS02F3: CLD
SYS02F4: XOR AX,AX
SYS02F6: STOSW
SYS02F7: MOV AX,fmClosed
SYS02FA: STOSW
SYS02FB: MOV AX,0080
SYS02FE: STOSW
SYS02FF: XOR AX,AX
SYS0301: STOSW
SYS0302: STOSW
SYS0303: STOSW
SYS0304: LEA AX,[DI+74]
SYS0307: STOSW
SYS0308: MOV AX,ES
SYS030A: STOSW
SYS030B: MOV AX,0499
SYS030E: STOSW
SYS030F: MOV AX,SYS
SYS0312: STOSW
SYS0313: XOR AX,AX
SYS0315: MOV CX,000E
SYS0318: REPZ
SYS0319: STOSW
SYS031A: MOV CX,004F
SYS031D: OR DX,DX
SYS031F: JNZ 032A
SYS0321: LODSB
SYS0322: CMP CL,AL
SYS0324: JBE 032A
SYS0326: MOV CL,AL
SYS0328: JCXZ 0332
SYS032A: LODSB
SYS032B: OR AL,AL
SYS032D: JZ 0332
SYS032F: STOSB
SYS0330: LOOP 032A
SYS0332: XOR AL,AL
SYS0334: STOSB
SYS0335: POP DS
SYS0336: RETF 0008
```

## Unknown code block
```nasm
SYS0339: MOV BX,SP
SYS033B: SS:
SYS033C: LES DI,[BX+0A]
SYS033F: SS:
SYS0340: MOV AX,[BX+04]
SYS0343: ES:
SYS0344: MOV [DI+04],AX
SYS0347: SS:
SYS0348: MOV AX,[BX+06]
SYS034B: ES:
SYS034C: MOV [DI+0C],AX
SYS034F: SS:
SYS0350: MOV AX,[BX+08]
SYS0353: ES:
SYS0354: MOV [DI+0E],AX
SYS0357: XOR AX,AX
SYS0359: ES:
SYS035A: MOV [DI+08],AX
SYS035D: ES:
SYS035E: MOV [DI+0A],AX
SYS0361: RETF 000A
```

## Entry point for Reset
```nasm
SYS0364: MOV DX,fmInput
SYS0367: JMP 0371
```

## Entry point for Rewrite
```nasm
SYS0369: MOV DX,fmOutput
SYS036C: JMP 0371
```

## Common code for Reset/Rewrite
```nasm
SYS036E: MOV DX,fmInOut
SYS0371: PUSH BP
SYS0372: MOV BP,SP
SYS0374: LES DI,[BP+06]
SYS0377: ES:
SYS0378: MOV AX,[DI:Mode]
SYS037B: CMP AX,fmInput
SYS037E: JZ 0392
SYS0380: CMP AX,fmOutput
SYS0383: JZ 0392
SYS0385: CMP AX,fmClosed
SYS0388: JZ 039A
SYS038A: MOV WORD PTR [InOutRes],0066
SYS0390: JMP 03B6
SYS0392: PUSH DX
SYS0393: PUSH ES
SYS0394: PUSH DI
SYS0395: PUSH CS
SYS0396: CALL 03BE
SYS0399: POP DX
SYS039A: XOR AX,AX
SYS039C: ES:
SYS039D: MOV [DI:Mode],DX
SYS03A0: ES:
SYS03A1: MOV [DI:BufPos],AX
SYS03A4: ES:
SYS03A5: MOV [DI:BufEnd],AX
```

## Open File
```nasm
SYS03A8: MOV BX,0010
SYS03AB: CALL 03FA
SYS03AE: JZ 03B6
SYS03B0: ES:
SYS03B1: MOV WORD PTR [DI:Mode],fmClosed
SYS03B6: POP BP
SYS03B7: RETF 0004
```

## Entry point for I/O Function Call
```nasm
SYS03BA: MOV AL,00
SYS03BC: JMP 03C0
```

## Entry point for I/O Function Call
```nasm
SYS03BE: MOV AL,01
SYS03C0: PUSH BP
SYS03C1: MOV BP,SP
SYS03C3: LES DI,[BP+06]
SYS03C6: ES:
SYS03C7: CMP WORD PTR [DI:Mode],fmInput
SYS03CC: JZ 03E6
SYS03CE: ES:
SYS03CF: CMP WORD PTR [DI:Mode],fmOutput
SYS03D4: JZ 03DE
SYS03D6: MOV WORD PTR [InOutRes],0067
SYS03DC: JMP 03F6
```

## Perform I/O
```nasm
SYS03DE: PUSH AX
SYS03DF: MOV BX,0014
SYS03E2: CALL 03FA
SYS03E5: POP AX
SYS03E6: OR AL,AL
SYS03E8: JZ 03F6
```

## Close File
```nasm
SYS03EA: MOV BX,001C
SYS03ED: CALL 03FA
SYS03F0: ES:
SYS03F1: MOV WORD PTR [DI:Mode],fmClosed
SYS03F6: POP BP
SYS03F7: RETF 0004
```

## I/O Function Call Dispatcher
```nasm
SYS03FA: PUSH ES
SYS03FB: PUSH DI
SYS03FC: PUSH ES
SYS03FD: PUSH DI
SYS03FE: ES:
SYS03FF: CALL FAR [BX+DI]
SYS0401: OR AX,AX
SYS0403: JZ 0408
SYS0405: MOV [InOutRes],AX
SYS0408: POP DI
SYS0409: POP ES
SYS040A: RET
```

## InOutFunc Handler (I)
```nasm
SYS040B: MOV BX,SP
SYS040D: PUSH DS
SYS040E: SS:
SYS040F: LES DI,[BX+04]
SYS0412: ES:
SYS0413: LDS DX,[DI:BufPtr]
SYS0416: ES:
SYS0417: MOV CX,[DI:BufSize]
SYS041A: ES:
SYS041B: MOV BX,[DI:Handle]
SYS041D: MOV AH,3F
SYS041F: INT 21
SYS0421: JB 0433
SYS0423: ES:
SYS0424: MOV [DI:BufEnd],AX
SYS0427: XOR AX,AX
SYS0429: ES:
SYS042A: MOV WORD PTR [DI:BufPos],0000
SYS042F: POP DS
SYS0430: RETF 0004
SYS0433: ES:
SYS0434: MOV WORD PTR [DI:BufEnd],0000
SYS0439: JMP 0429
```

## InOutFunc Handler (II)
```nasm
SYS043B: MOV BX,SP
SYS043D: PUSH DS
SYS043E: SS:
SYS043F: LES DI,[BX+04]
SYS0442: ES:
SYS0443: LDS DX,[DI:BufPtr]
SYS0446: XOR CX,CX
SYS0448: ES:
SYS0449: XCHG CX,[DI:BufPos]
SYS044C: ES:
SYS044D: MOV BX,[DI:Handle]
SYS044F: MOV AH,40
SYS0451: INT 21
SYS0453: JB 045C
SYS0455: SUB AX,CX
SYS0457: JZ 045C
SYS0459: MOV AX,0065
SYS045C: POP DS
SYS045D: RETF 0004
```

## InOutFunc Handler (III)
```nasm
SYS0460: MOV BX,SP
SYS0462: PUSH DS
SYS0463: SS:
SYS0464: LES DI,[BX+04]
SYS0467: ES:
SYS0468: LDS DX,[DI:BufPtr]
SYS046B: XOR CX,CX
SYS046D: ES:
SYS046E: XCHG CX,[DI:BufPos]
SYS0471: ES:
SYS0472: MOV BX,[DI:Handle]
SYS0474: MOV AH,40
SYS0476: INT 21
SYS0478: JB 047C
SYS047A: XOR AX,AX
SYS047C: POP DS
SYS047D: RETF 0004
```

## CloseFunc Handler
```nasm
SYS0480: MOV BX,SP
SYS0482: SS:
SYS0483: LES DI,[BX+04]
SYS0486: ES:
SYS0487: MOV BX,[DI:Handle]
SYS0489: CMP BX,+04
SYS048C: JBE 0494
SYS048E: MOV AH,3E
SYS0490: INT 21
SYS0492: JB 0496
SYS0494: XOR AX,AX
SYS0496: RETF 0004
```

## OpenFunc Handler
```nasm
SYS0499: MOV BX,SP
SYS049B: PUSH DS
SYS049C: SS:
SYS049D: LDS DI,[BX+04]
SYS04A0: XOR CX,CX
SYS04A2: MOV [DI:Handle],CX
SYS04A4: MOV AX,3D00
SYS04A7: CMP WORD PTR [DI:Mode],fmInput
SYS04AC: JZ 04BB
SYS04AE: MOV AL,02
SYS04B0: INC WORD PTR [DI:Handle]
SYS04B2: CMP WORD PTR [DI:Mode],fmInOut
SYS04B7: JZ 04BB
SYS04B9: MOV AH,3C
SYS04BB: CMP BYTE PTR [DI:Name],00
SYS04BF: JZ 04CA
SYS04C1: LEA DX,[DI:Name]
SYS04C4: INT 21
SYS04C6: JB 0522
SYS04C8: MOV [DI:Handle],AX
SYS04CA: MOV AX,040B
SYS04CD: MOV DX,SYS
SYS04D0: XOR CX,CX
SYS04D2: XOR BX,BX
SYS04D4: CMP WORD PTR [DI:Mode],fmInput
SYS04D9: JZ 050A
SYS04DB: MOV BX,[DI:Handle]
SYS04DD: MOV AX,4400
SYS04E0: INT 21
SYS04E2: TEST DL,80
SYS04E5: MOV AX,0460
SYS04E8: MOV DX,SYS
SYS04EB: MOV CX,AX
SYS04ED: MOV BX,DX
SYS04EF: JNZ 0505
SYS04F1: CMP WORD PTR [DI:Mode],fmInOut
SYS04F6: JNZ 04FB
SYS04F8: CALL 0526
SYS04FB: MOV AX,043B
SYS04FE: MOV DX,SYS
SYS0501: XOR CX,CX
SYS0503: XOR BX,BX
SYS0505: MOV WORD PTR [DI:Mode],fmOutput
SYS050A: MOV [DI:InOutFunc.Offset],AX
SYS050D: MOV [DI:InOutFunc.Segment],DX
SYS0510: MOV [DI:FlushFunc.Offset],CX
SYS0513: MOV [DI:FlushFunc.Segment],BX
SYS0516: MOV WORD PTR [DI:CloseFunc.Offset],0480
SYS051B: MOV WORD PTR [DI:CloseFunc.Segment],SYS
SYS0520: XOR AX,AX
SYS0522: POP DS
SYS0523: RETF 0004
```

## OpenFunc Handler: Seek
```nasm
SYS0526: XOR DX,DX
SYS0528: XOR CX,CX
SYS052A: MOV BX,[DI:Handle]
SYS052C: MOV AX,4202
SYS052F: INT 21
SYS0531: SUB AX,0080
SYS0534: SBB DX,+00
SYS0537: JNB 053D
SYS0539: XOR AX,AX
SYS053B: XOR DX,DX
SYS053D: MOV CX,DX
SYS053F: MOV DX,AX
SYS0541: MOV BX,[DI:Handle]
SYS0543: MOV AX,4200
SYS0546: INT 21
SYS0548: LEA DX,[DI:TextBuf]
SYS054C: MOV CX,0080
SYS054F: MOV BX,[DI:Handle]
SYS0551: MOV AH,3F
SYS0553: INT 21
SYS0555: JNB 0559
SYS0557: XOR AX,AX
SYS0559: XOR BX,BX
SYS055B: CMP BX,AX
SYS055D: JZ 057F
SYS055F: CMP BYTE PTR [BX+DI:TextBuf],1A
SYS0564: JZ 0569
SYS0566: INC BX
SYS0567: JMP 055B
SYS0569: MOV DX,BX
SYS056B: SUB DX,AX
SYS056D: MOV CX,FFFF
SYS0570: MOV BX,[DI:Handle]
SYS0572: MOV AX,4202
SYS0575: INT 21
SYS0577: XOR CX,CX
SYS0579: MOV BX,[DI:Handle]
SYS057B: MOV AH,40
SYS057D: INT 21
SYS057F: RET
```

```nasm
SYS0580: XOR DI,8000
SYS0584: OR CL,CL
SYS0586: JZ 0603
SYS0588: OR AL,AL
SYS058A: JZ 0604
SYS058C: CMP AL,CL
SYS058E: JBE 0595
SYS0590: XCHG CX,AX
SYS0591: XCHG BX,SI
SYS0593: XCHG DX,DI
SYS0595: SUB AL,CL
SYS0597: NEG AL
SYS0599: CMP AL,29
SYS059B: JNB 0604
SYS059D: XCHG AL,CL
```

```nasm
SYS059F: PUSH BP
SYS05A0: PUSH AX
```

Save **BP** and **AX**.

```nasm
SYS05A1: MOV AH,DH
SYS05A3: AND AH,80
SYS05A6: MOV BP,AX
SYS05A8: XOR AX,DI
```

```nasm
SYS05AA: POP AX
```

Restore **AX** (saved in **SYS:05A0**).

```nasm
SYS05AB: PUSHF
SYS05AC: MOV AL,00
SYS05AE: OR DH,80
SYS05B1: OR DI,8000
SYS05B5: CMP CL,08
SYS05B8: JB 05CB
SYS05BA: MOV AL,AH
SYS05BC: MOV AH,BL
SYS05BE: MOV BL,BH
SYS05C0: MOV BH,DL
SYS05C2: MOV DL,DH
SYS05C4: XOR DH,DH
SYS05C6: SUB CL,08
SYS05C9: JMP 05B5
```

```nasm
SYS05CB: OR CL,CL
SYS05CD: JZ 05D9
```

## Adjust Number and Exponent
```nasm
SYS05CF: SHR DX,1
SYS05D1: RCR BX,1
SYS05D3: RCR AX,1
SYS05D5: DEC CL
```

This shifts the **MSW** in **DX** by one power of two (to the right) and adjust the exponent **CL**, i.e **DX**:**BX**:**AX** * 2^(**-1**), **CL** = **CL - 1**.

```nasm
SYS05D7: JNZ 05CF
SYS05D9: POPF
SYS05DA: JS 0613
```

```nasm
SYS05DC: ADD AX,CX
SYS05DE: ADC BX,SI
SYS05E0: ADC DX,DI
```

Add **DI**:**SI**:**CX** to **DX**:**BX**:**AX** (handle all carries).

```nasm
SYS05E2: MOV CX,BP
SYS05E4: POP BP
```

Transfer **BP** to **CX** then restore **BP** (saved in **SYS:059F**).

```nasm
SYS05E5: JNB 05F1
```

Check if there was an overflow in **SYS:05E0** then adjust.

```nasm
SYS05E7: RCR DX,1
SYS05E9: RCR BX,1
SYS05EB: RCR AX,1
SYS05ED: INC CL
```

Shifts **DX**:**BX**:**AX** to the right once but compensate for it by increasing **CL**.

```nasm
SYS05EF: JZ 0611
```

If adjustment in **CL** causes the value to wrap around to 0, then return immediately with carry flag set (**SYS:0611**).

```nasm
SYS05F1: ADD AX,0080
SYS05F4: ADC BX,+00
SYS05F7: ADC DX,+00
SYS05FA: JB 060B
```

Test if exponent in **AX** causes an overflow.

```nasm
SYS05FC: MOV AL,CL
SYS05FE: AND DH,7F
SYS0601: OR DH,CH
SYS0603: RET
```

Copy the adjusted exponent in **CL** to **AL**. Clear the sign bit in **DH** then copy over the adjusted **MSB** in **CH** to **DH**. Return.

```nasm
SYS0604: MOV AX,CX
SYS0606: MOV BX,SI
SYS0608: MOV DX,DI
SYS060A: RET
```

Copy the adjusted number in DI:SI:CX to **DX**:**BX**:**AX** then return.

## Adjust MSW and Exponent
```nasm
SYS060B: RCR DX,1
SYS060D: INC CL
```

This shifts the **MSW** in **DX** by one power of two (to the right) but compensates for it in **CL**, i.e **DX** * 2^(**CL - 1**) * 2^(**CL + 1**).

```nasm
SYS060F: JNZ 05FC
SYS0611: STC
SYS0612: RET
```

If the result wraps **CL** back to **0**, then return with CF set (**SYS:0611**) otherwise continue to **SYS:05FC**.

```nasm
SYS0613: SUB AX,CX
SYS0615: SBB BX,SI
SYS0617: SBB DX,DI
```

Subtract **DI**:**SI**:**CX** from **DX**:**BX**:**AX**.

```nasm
SYS0619: MOV CX,BP
SYS061B: POP BP
SYS061C: JNB 062E
SYS061E: NOT DX
SYS0620: NOT BX
SYS0622: NEG AX
SYS0624: CMC
SYS0625: ADC BX,+00
SYS0628: ADC DX,+00
SYS062B: XOR CH,80
SYS062E: MOV DI,DX
SYS0630: OR DI,BX
SYS0632: OR DI,AX
SYS0634: JZ 0603
SYS0636: OR DH,DH
SYS0638: JS 05F1
```

```nasm
SYS063A: SHL AX,1
SYS063C: RCL BX,1
SYS063E: RCL DX,1
SYS0640: DEC CL
SYS0642: JNZ 0636
```

Multiply **DX**:**BX**:**AX** 2 then decrease the exponent in **CL**. This has the same effect just shifting the significand but preserving the overall magnitiude of the number, i.e. ```(A * 2) * 2^-1```. If CL drops to zero, then set **DX**:**BX**:**AX** to 0 instead.

## Jump point en route to setting **DX**:**BX**:**AX** to zero

```nasm
SYS0644: JMP 0743
```

## Step 7b
This is step 7b of the conversion. At this point:
- **DI**:**SI**:**CX** holds the adjusted ***Real*** number obtained from **SYS:0C35-0C70**.
- **DX**:**BX**:**AX** holds the ***Real*** number being converted.

For the number we are trying to convert, following number is loaded into **DI**:**SI**:**CX** = **000000000081h**.

```nasm
SYS0647: OR AL,AL
SYS0649: JZ 0644
```

Return with **DX**:**BX**:**AX** = ***0*** if the **exponent** in **AL** is 0.

```nasm
SYS064B: OR CL,CL
SYS064D: JZ 0644
```

Return with **DX**:**BX**:**AX** = ***0*** if the **exponent** in **CL** is 0.

```nasm
SYS064F: PUSH BP
```

Save **BP** which keeps track of the parameters and immediate results as well as locations to the temporary buffers.

```nasm
SYS0650: MOV BP,DX
```

Save **DX** into **BP**.

```nasm
SYS0652: XOR DX,DI
SYS0654: AND DX,8000
```

Complement the sign bit in **DI** into **DX**. Clear all the bits except for the sign bit in **DX**.

```nasm
SYS0658: XCHG DL,AL
SYS065A: ADD DL,CL
SYS065C: ADC DH,AL
SYS065E: MOV CL,AL
```

Copy the exponent in **AL** into **DL**. Add the exponent in **CL** to **DL**. Add the overflow in **SYS**:**065A** to **DH**. Clear **CL**.

```nasm
SYS0660: OR BP,8000
SYS0664: OR DI,8000
```

Set the sign bits of **BP** and **DI**.

```nasm
SYS0668: PUSH DX
```

Save **DX**.

```nasm
SYS0669: OR AH,AH
SYS066B: JNZ 0671
SYS066D: OR BX,BX
SYS066F: JZ 067E
SYS0671: OR CH,CH
SYS0673: JNZ 069B
SYS0675: OR SI,SI
SYS0677: JNZ 069B
SYS0679: XCHG CX,AX
SYS067A: XCHG BX,SI
SYS067C: XCHG BP,DI
SYS067E: MOV AX,CX
SYS0680: MUL BP
SYS0682: MOV BX,DX
SYS0684: MOV AX,SI
SYS0686: MUL BP
SYS0688: ADD BX,AX
SYS068A: ADC DX,+00
SYS068D: MOV CX,DX
SYS068F: MOV AX,DI
SYS0691: MUL BP
SYS0693: ADD AX,CX
SYS0695: ADC DX,+00
SYS0698: JMP 0716
```

```nasm
SYS069A: NOP
```

```nasm
SYS069B: PUSH DI
SYS069C: PUSH SI
SYS069D: PUSH CX
SYS069E: PUSH BP
SYS069F: PUSH BX
SYS06A0: PUSH AX
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
SYS06A1: MOV BP,SP
```

Point **BP** to top of stack (**SP**).

```nasm
SYS06A3: XOR CX,CX
```

Clear **CX**.

```nasm
SYS06A5: MOV AL,[BP+01]
```

Load **AH** (from the stack) into **AL**.

```nasm
SYS06A8: MUL BYTE PTR [BP+07]
```

Multiply **CH** (from stack) to **AL** and store in **AL**.

```nasm
SYS06AB: MOV SI,AX
SYS06AD: MOV DI,CX
SYS06AF: MOV BX,CX
```

Clear **BX**, **SI**, **DI** (**CX** = 0 from **SYS:06A3**).

```nasm
SYS06B1: MOV AX,[BP+00]
SYS06B4: MUL WORD PTR [BP+08]
SYS06B7: ADD SI,AX
SYS06B9: ADC DI,DX
SYS06BB: ADC BX,CX
SYS06BD: MOV AX,[BP+02]
SYS06C0: MUL WORD PTR [BP+06]
SYS06C3: ADD SI,AX
SYS06C5: ADC DI,DX
SYS06C7: ADC BX,CX
SYS06C9: MOV SI,CX
SYS06CB: MOV AX,[BP+00]
SYS06CE: MUL WORD PTR [BP+0A]
SYS06D1: ADD DI,AX
SYS06D3: ADC BX,DX
SYS06D5: ADC SI,CX
SYS06D7: MOV AX,[BP+02]
SYS06DA: MUL WORD PTR [BP+08]
SYS06DD: ADD DI,AX
SYS06DF: ADC BX,DX
SYS06E1: ADC SI,CX
SYS06E3: MOV AX,[BP+04]
SYS06E6: MUL WORD PTR [BP+06]
SYS06E9: ADD DI,AX
SYS06EB: ADC BX,DX
SYS06ED: ADC SI,CX
SYS06EF: MOV DI,CX
SYS06F1: MOV AX,[BP+02]
SYS06F4: MUL WORD PTR [BP+0A]
SYS06F7: ADD BX,AX
SYS06F9: ADC SI,DX
SYS06FB: ADC DI,CX
SYS06FD: MOV AX,[BP+04]
SYS0700: MUL WORD PTR [BP+08]
SYS0703: ADD BX,AX
SYS0705: ADC SI,DX
SYS0707: ADC DI,CX
SYS0709: MOV AX,[BP+04]
SYS070C: MUL WORD PTR [BP+0A]
SYS070F: ADD AX,SI
SYS0711: ADC DX,DI
SYS0713: ADD SP,+0C
SYS0716: XCHG BX,AX
SYS0717: POP CX
SYS0718: POP BP
SYS0719: OR DH,DH
SYS071B: JS 0724
SYS071D: SHL AX,1
SYS071F: RCL BX,1
SYS0721: RCL DX,1
SYS0723: DEC CX
SYS0724: SUB CX,8081
SYS0728: ADD AX,0080
SYS072B: ADC BX,+00
SYS072E: ADC DX,+00
SYS0731: JNB 0736
SYS0733: RCR DX,1
SYS0735: INC CX
SYS0736: TEST CH,40
SYS0739: JNZ 0743
SYS073B: INC CX
SYS073C: MOV AL,CL
SYS073E: XOR DH,CH
SYS0740: SHR CH,1
SYS0742: RET
```

## Clear number

```nasm
SYS0743: XOR AX,AX
SYS0745: MOV BX,AX
SYS0747: MOV DX,AX
SYS0749: RET
```

## Step 7a

This is step 7a of the conversion. At this point:
- **DI**:**SI**:**CX** holds the adjusted ***Real*** number obtained from **SYS:0C35-0C70**.
- **DX**:**BX**:**AX** holds the ***Real*** number being converted.

```nasm
SYS074A: OR AL,AL
SYS074C: JZ 0743
```

Return with the number **DX**:**BX**:**AX** set to 0 if the exponent in **AL** is **0**.

```nasm
SYS074E: PUSH BP
SYS074F: MOV BP,DX
SYS0751: XOR DX,DI
SYS0753: OR DI,8000
SYS0757: OR BP,8000
SYS075B: AND DX,8000
SYS075F: XCHG AL,DL
SYS0761: SUB DL,CL
SYS0763: SBB DH,AL
SYS0765: PUSH DX
SYS0766: MOV AL,02
SYS0768: MOV DX,0001
SYS076B: CMP BP,DI
SYS076D: JNZ 0775
SYS076F: CMP BX,SI
SYS0771: JNZ 0775
SYS0773: CMP AH,CH
SYS0775: JB 077D
SYS0777: SUB AH,CH
SYS0779: SBB BX,SI
SYS077B: SBB BP,DI
SYS077D: RCL DX,1
SYS077F: JB 0792
SYS0781: SHL AH,1
SYS0783: RCL BX,1
SYS0785: RCL BP,1
SYS0787: JNB 076B
SYS0789: SUB AH,CH
SYS078B: SBB BX,SI
SYS078D: SBB BP,DI
SYS078F: CLC
SYS0790: JMP 077D
SYS0792: DEC AL
SYS0794: JS 07A0
SYS0796: PUSH DX
SYS0797: MOV DX,0001
SYS079A: JNZ 0781
SYS079C: MOV DL,40
SYS079E: JMP 0781
SYS07A0: MOV AX,DX
SYS07A2: MOV CL,06
SYS07A4: SHL AX,CL
SYS07A6: POP BX
SYS07A7: POP DX
SYS07A8: POP CX
SYS07A9: POP BP
SYS07AA: NOT AX
SYS07AC: NOT BX
SYS07AE: XOR DX,-01
SYS07B1: JS 07BA
SYS07B3: RCL AX,1
SYS07B5: RCL BX,1
SYS07B7: RCL DX,1
SYS07B9: DEC CX
SYS07BA: ADD CX,8080
SYS07BE: JMP 0728
SYS07C1: PUSH DX
SYS07C2: XOR DX,DI
SYS07C4: POP DX
SYS07C5: JNS 07CC
SYS07C7: PUSH DX
SYS07C8: RCL DX,1
SYS07CA: POP DX
SYS07CB: RET
```

```nasm
SYS07CC: TEST DH,80
SYS07CF: JZ 07D8
SYS07D1: CALL 07D8
SYS07D4: JZ 07EA
SYS07D6: CMC
SYS07D7: RET
```

```nasm
SYS07D8: CMP AL,CL
SYS07DA: JNZ 07EA
SYS07DC: OR AL,AL
SYS07DE: JZ 07EA
SYS07E0: CMP DX,DI
SYS07E2: JNZ 07EA
SYS07E4: CMP BX,SI
SYS07E6: JNZ 07EA
SYS07E8: CMP AH,CH
SYS07EA: RET
```

```nasm
SYS07EB: MOV BX,AX
SYS07ED: OR BX,DX
SYS07EF: JZ 0826
SYS07F1: MOV CH,DH
SYS07F3: OR DX,DX
SYS07F5: JNS 07FE
SYS07F7: NEG DX
SYS07F9: NEG AX
SYS07FB: SBB DX,+00
SYS07FE: MOV BX,AX
SYS0800: MOV AX,00A0
SYS0803: OR DX,DX
SYS0805: JNZ 0813
SYS0807: XCHG DX,BX
SYS0809: MOV AL,90
SYS080B: OR DH,DH
SYS080D: JNZ 0813
SYS080F: XCHG DH,DL
SYS0811: MOV AL,88
SYS0813: OR DX,DX
SYS0815: JS 081F
SYS0817: DEC AL
SYS0819: ADD BX,BX
SYS081B: ADC DX,DX
SYS081D: JNS 0817
SYS081F: OR CH,CH
SYS0821: JS 0826
SYS0823: AND DH,7F
SYS0826: RET
```

```nasm
SYS0827: XCHG BX,AX
SYS0828: MOV CL,A0
SYS082A: SUB CL,BL
SYS082C: JB 0889
SYS082E: MOV BL,DH
SYS0830: OR DH,80
SYS0833: CMP CL,20
SYS0836: JNB 088A
SYS0838: CMP CL,10
SYS083B: JB 0846
SYS083D: MOV BH,AH
SYS083F: MOV AX,DX
SYS0841: XOR DX,DX
SYS0843: SUB CL,10
SYS0846: CMP CL,08
SYS0849: JB 0858
SYS084B: MOV BH,AL
SYS084D: MOV AL,AH
SYS084F: MOV AH,DL
SYS0851: MOV DL,DH
SYS0853: XOR DH,DH
SYS0855: SUB CL,08
SYS0858: OR CL,CL
SYS085A: JZ 0866
SYS085C: SHR DX,1
SYS085E: RCR AX,1
SYS0860: RCR BH,1
SYS0862: DEC CL
SYS0864: JNZ 085C
SYS0866: OR CH,CH
SYS0868: JZ 0874
SYS086A: ADD BH,BH
SYS086C: ADC AX,0000
SYS086F: ADC DX,+00
SYS0872: JB 0889
SYS0874: MOV CX,AX
SYS0876: OR CX,DX
SYS0878: JZ 0889
SYS087A: OR BL,BL
SYS087C: JNS 0885
SYS087E: NEG DX
SYS0880: NEG AX
SYS0882: SBB DX,+00
SYS0885: XOR BL,DH
SYS0887: ADD BL,BL
SYS0889: RET
```

```nasm
SYS088A: MOV BH,DH
SYS088C: MOV AX,0000
SYS088F: MOV DX,0000
SYS0892: JZ 0866
SYS0894: RET
```

```nasm
SYS0895: MOV CX,DI
SYS0897: MOV SI,000A
SYS089A: MOV BX,DX
SYS089C: OR BX,BX
SYS089E: JNS 08B1
SYS08A0: NEG BX
SYS08A2: NEG AX
SYS08A4: SBB BX,+00
SYS08A7: CALL 08B1
SYS08AA: DEC DI
SYS08AB: ES:
SYS08AC: MOV BYTE PTR [DI],2D
SYS08AF: INC CX
SYS08B0: RET
```

```nasm
SYS08B1: XOR DX,DX
SYS08B3: XCHG BX,AX
SYS08B4: DIV SI
SYS08B6: XCHG BX,AX
SYS08B7: DIV SI
SYS08B9: ADD DL,30
SYS08BC: CMP DL,3A
SYS08BF: JB 08C4
SYS08C1: ADD DL,07
SYS08C4: DEC DI
SYS08C5: ES:
SYS08C6: MOV [DI],DL
SYS08C8: MOV DX,AX
SYS08CA: OR DX,BX
SYS08CC: JNZ 08B1
SYS08CE: SUB CX,DI
SYS08D0: RET
```

```nasm
SYS08D1: XOR AX,AX
SYS08D3: XOR DX,DX
SYS08D5: XOR SI,SI
SYS08D7: JCXZ 0936
SYS08D9: ES:
SYS08DA: CMP BYTE PTR [DI],2B
SYS08DD: JZ 08E6
SYS08DF: ES:
SYS08E0: CMP BYTE PTR [DI],2D
SYS08E3: JNZ 08EA
SYS08E5: DEC SI
SYS08E6: INC DI
SYS08E7: DEC CX
SYS08E8: JZ 0936
SYS08EA: ES:
SYS08EB: CMP BYTE PTR [DI],24
SYS08EE: JZ 0938
SYS08F0: ES:
SYS08F1: MOV BL,[DI]
SYS08F3: SUB BL,3A
SYS08F6: ADD BL,0A
SYS08F9: JNB 0920
SYS08FB: TEST DH,F0
SYS08FE: JNZ 0936
SYS0900: PUSH BX
SYS0901: SHL AX,1
SYS0903: RCL DX,1
SYS0905: PUSH DX
SYS0906: PUSH AX
SYS0907: SHL AX,1
SYS0909: RCL DX,1
SYS090B: SHL AX,1
SYS090D: RCL DX,1
SYS090F: POP BX
SYS0910: ADD AX,BX
SYS0912: POP BX
SYS0913: ADC DX,BX
SYS0915: POP BX
SYS0916: XOR BH,BH
SYS0918: ADD AX,BX
SYS091A: ADC DX,+00
SYS091D: INC DI
SYS091E: LOOP 08F0
SYS0920: MOV BX,AX
SYS0922: OR BX,DX
SYS0924: JZ 0935
SYS0926: OR SI,SI
SYS0928: JNS 0931
SYS092A: NEG DX
SYS092C: NEG AX
SYS092E: SBB DX,+00
SYS0931: XOR SI,DX
SYS0933: JS 0936
SYS0935: RET
```

```nasm
SYS0936: STC
SYS0937: RET
```

```nasm
SYS0938: INC DI
SYS0939: DEC CX
SYS093A: JZ 0936
SYS093C: ES:
SYS093D: MOV BL,[DI]
SYS093F: CMP BL,61
SYS0942: JB 0947
SYS0944: SUB BL,20
SYS0947: SUB BL,3A
SYS094A: ADD BL,0A
SYS094D: JB 095A
SYS094F: SUB BL,17
SYS0952: ADD BL,06
SYS0955: JNB 0920
SYS0957: ADD BL,0A
SYS095A: MOV BH,04
SYS095C: SHL AX,1
SYS095E: RCL DX,1
SYS0960: JB 0936
SYS0962: DEC BH
SYS0964: JNZ 095C
SYS0966: OR AL,BL
SYS0968: INC DI
SYS0969: LOOP 093C
SYS096B: OR SI,SI
SYS096D: JNS 0976
SYS096F: NEG DX
SYS0971: NEG AX
SYS0973: SBB DX,+00
SYS0976: CLC
SYS0977: RET
```

## Conversion - Step 2

This is step 2 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0978: PUSH BP
SYS0979: MOV BP,SP
SYS097B: SUB SP,+14
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
SYS097E: PUSH DI
```

Preserve **DI**.

```nasm
SYS097F: CMP CX,+0B
SYS0982: JLE 0987
SYS0984: MOV CX,000B
SYS0987: CMP CX,-0B
SYS098A: JGE 098F
SYS098C: MOV CX,FFF5
```

Limits the precision to **000Bh**/**12** (or **FFF5h**/**-12**).

```nasm
SYS098F: MOV [BP-02],CX
SYS0992: MOV [BP-04],DH
```

Record the final **precision** in **SS**:[**BP-02**] and the numbers' **MSB** (most significant byte) in **SS**:[**BP-04**].

```nasm
SYS0995: PUSH ES
SYS0996: PUSH DI
```

Once more, **ES**:**DI** onto the stack.

```nasm
SYS0997: LEA DI,[BP-14]
SYS099A: PUSH SS
SYS099B: POP ES
```

Point **ES**:**DI** to the 20 bytes reserved earlier (at **SS**:[**BP-14**]).

```nasm
SYS099C: CALL 0A70
```

Proceed to the next step of the conversion (at **SYS:0A70**).

```nasm
SYS099F: POP DI
SYS09A0: POP ES
```

Restore **ES**:**DI** (saved in **SYS**:**0995**)

## Step 9 - Round off

This routine will perform any rounding-off on the converted digits stored **SS**:[**BP-14**]

```nasm
SYS09A1: MOV [BP-06],CX
```

Save adjusted precision in **CX** to stack without modifying **BP**.

```nasm
SYS09A4: MOV SI,[BP-02]
```

Retrieve original **precision** stored previously on the stack (saved in **SYS:098F**).

```nasm
SYS09A7: OR SI,SI
SYS09A9: JS 09B7
```

Check if **SI** is signed / negative.

```nasm
SYS09AB: ADD SI,[BP-06]
SYS09AE: INC SI
SYS09AF: JNS 09B9
SYS09B1: MOV BYTE PTR [BP-14],00
SYS09B5: JMP 09E5
SYS09B7: NEG SI
SYS09B9: CMP SI,+0C
SYS09BC: JB 09C1
```

### Round off up to **0Bh**/**11** digits
```nasm
SYS09BE: MOV SI,000B
```

Move pointer (in **SI**) to end of the string.

```nasm
SYS09C1: CMP BYTE PTR [BP+SI-14],35
SYS09C5: MOV BYTE PTR [BP+SI-14],00
SYS09C9: JB 09E5
```

Check if digit is below **'5'**/**35h**.

```nasm
SYS09CB: DEC SI
```

Move pointer **SI** one digit to one byte to the left (of the string).

```nasm
SYS09CC: JS 09DD
```

Check if the sign (of **SI**) changes, i.e. if it is already on the first digit (after the decimal point).

```nasm
SYS09CE: INC BYTE PTR [BP+SI-14]
```

Round up the digit at this location.

```nasm
SYS09D1: CMP BYTE PTR [BP+SI-14],39
SYS09D5: JBE 09E5
SYS09D7: MOV BYTE PTR [BP+SI-14],00
SYS09DB: JMP 09CB
```

Check if the digit is below or equal to **'9'**/**39h**. If the value of the byte at this location is greater than **'9'** mark it as **NULL** then repeat the operation. Otherwise, the round up operation is complete.

```nasm
SYS09DD: MOV WORD PTR [BP-14],0031
SYS09E2: INC WORD PTR [BP-06]
```

Round up the number to **1**. Increase the precision/exponent in **SS**:[**BP-06**].

```nasm
SYS09E5: XOR SI,SI
SYS09E7: CLD
```

Reset the index (in **SI**) to 0.

```nasm
SYS09E8: MOV DX,[BP-02]
SYS09EB: OR DX,DX
SYS09ED: JS 0A24
```

Retrieve the exponent from **SS**:[**BP-02**] and check if it is signed.

```nasm
SYS09EF: TEST BYTE PTR [BP-04],80
SYS09F3: JZ 09F8
SYS09F5: MOV AL,2D
SYS09F7: STOSB
SYS09F8: MOV CX,[BP-06]
SYS09FB: OR CX,CX
SYS09FD: JNS 0A04
SYS09FF: MOV AL,30
SYS0A01: STOSB
SYS0A02: JMP 0A0B
SYS0A04: CALL 0A64
SYS0A07: STOSB
SYS0A08: DEC CX
SYS0A09: JNS 0A04
SYS0A0B: OR DX,DX
SYS0A0D: JZ 0A5B
SYS0A0F: MOV AL,2E
SYS0A11: STOSB
SYS0A12: INC CX
SYS0A13: JZ 0A1B
SYS0A15: MOV AL,30
SYS0A17: STOSB
SYS0A18: DEC DX
SYS0A19: JNZ 0A12
SYS0A1B: DEC DX
SYS0A1C: JS 0A5B
SYS0A1E: CALL 0A64
SYS0A21: STOSB
SYS0A22: JMP 0A1B
```

```nasm
SYS0A24: MOV AL,20
SYS0A26: TEST BYTE PTR [BP-04],80
SYS0A2A: JZ 0A2E
```

Check if the number is negative (using the **MSB** stored at **SS**:[**BP-04**]). If it is positive, set a whitespace (20h) character in **AL**.

```nasm
SYS0A2C: MOV AL,2D
```

The number is negative, set a **'-'**/**2Dh** character in **AL** instead.

```nasm
SYS0A2E: STOSB
```

Store the sign in output buffer buffer.

```nasm
SYS0A2F: CALL 0A64
SYS0A32: STOSB
```

Retrieve next digit and store in buffer (on the call to **SYS:0A64**).

```nasm
SYS0A33: INC DX
SYS0A34: JZ 0A40
```

Increase character count **DX** and check if all the digits (before the decimal point) have been stored in **ES**:**DI**.

```nasm
SYS0A36: MOV AL,2E
SYS0A38: STOSB
```

Store the decimal point **'.'**/**2Eh**.

```nasm
SYS0A39: CALL 0A64
SYS0A3C: STOSB
SYS0A3D: INC DX
SYS0A3E: JNZ 0A39
```

Retrieve the next digits then store it.

```nasm
SYS0A40: MOV AL,45
SYS0A42: STOSB
```

Store the **'E'**/**45h** or character (for the scientific notation).

```nasm
SYS0A43: MOV AL,2B
SYS0A45: MOV DX,[BP-06]
SYS0A48: OR DX,DX
SYS0A4A: JNS 0A50
```

Prepare the **'+'**/**2B** character. Retrieve the exponent in **DX** from **SS**:[**BP-06**]. Test whetherthe exponent is positive.

```nasm
SYS0A4C: MOV AL,2D
SYS0A4E: NEG DX
```

Preepare **'+'**/**2D** character and negate the exponent in **DX** because the exponent was negative.

```nasm
SYS0A50: STOSB
```

Store this sign at the oputput buffer.

```nasm
SYS0A51: MOV AX,DX
SYS0A53: MOV DL,0A
SYS0A55: IDIV DL
```

Move the exponent to **AX**. Convert the exponent in **AL** by doing a signed division of **AL** by **DL**. The quotient will be stored in **AL** while the remainder is stored in **AH**.

```nasm
SYS0A57: ADD AX,3030
```

Convert both bytes of the result in **AX** into ASCII.

```nasm
SYS0A5A: STOSW
```

Store **AX** into the output buffer. Because the remainder is in **AX**, storing the word will actually store **AL** first then followed by **AH** next.

```nasm
SYS0A5B: MOV CX,DI
SYS0A5D: POP DI
SYS0A5E: SUB CX,DI
```

Compute for the number of characters rendered in **CX** by computing for **CX-DI**.

```nasm
SYS0A60: MOV SP,BP
```

Restore SP (modified in **SYS:097B**).

```nasm
SYS0A62: POP BP
SYS0A63: RET
```

Restore **BP** then return.

### Retrieve next character from buffer
```nasm
SYS0A64: MOV AL,[BP+SI-14]
SYS0A67: INC SI
SYS0A68: OR AL,AL
SYS0A6A: JNZ 0A6F
SYS0A6C: MOV AL,30
SYS0A6E: DEC SI
SYS0A6F: RET
```

This subroutine retrieves one byte from the buffer and store it into **AL**. If it is empty, it store **'0'**/**30h** into **AL** instead. The index to the buffer is moved to the next location.

## Conversion - Step 3

This is step 3 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0A70: OR AL,AL
SYS0A72: JNZ 0A81
```

Proceed to the next step (at **SYS:0A81**) if the exponent is non-zero.

```nasm
SYS0A74: MOV CX,0006
SYS0A77: MOV AX,3030
SYS0A7A: CLD
SYS0A7B: REPZ
SYS0A7C: STOSW
SYS0A7D: XOR AL,AL
SYS0A7F: STOSB
SYS0A80: RET
```

Since the exponent is zero, as per the rules of the [***Real***-data type](REAL-TYPE.md), the value is zero. This entire section of the code fills the buffer with 12 **0**'s (ASCII **30h**).

## Conversion - Step 4

This is step 4 the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** holds the desired precision.

```nasm
SYS0A81: AND DH,7F
```

Clear the sign flag (bit 7 of the number's **MSB**).

```nasm
SYS0A84: PUSH AX
```

Save the exponent in **AX** onto the stack.

```nasm
SYS0A85: SUB AL,80
SYS0A87: MOV AH,4D
SYS0A89: IMUL AH
SYS0A8B: ADD AX,0005
SYS0A8E: MOV AL,AH
SYS0A90: CBW
SYS0A91: MOV CX,AX
```

Calculate needed adjustments to the exponent into **CX**.

```nasm
SYS0A93: POP AX
```

Restore **AX** to the saved value (**exponent**)  in the stack.

```nasm
SYS0A94: CMP CX,-27
SYS0A97: JNZ 0A9A
SYS0A99: INC CX
```

Check if CX needs further adjustment (if equal to **FFD9h**/**-27**).

```nasm
SYS0A9A: PUSH CX
SYS0A9B: PUSH DI
```

Save **CX** and **DI**.

```nasm
SYS0A9C: NEG CX
```

Reverse the sign of **CX**.

```nasm
SYS0A9E: CALL 0BE5
```

Proceed to the next step in the conversion (at **SYS:0BE5**).

```nasm
SYS0AA1: POP DI
SYS0AA2: POP CX
```

Restore **CX** and **DI** (saved in **SYS**:**0A9A**).

```nasm
SYS0AA3: CMP AL,81
SYS0AA5: JNB 0AAB
SYS0AA7: CALL 0C71
SYS0AAA: DEC CX
```

If the number needs further adjustment call **SYS**:**0C71** then decrease the **precsion**.

```nasm
SYS0AAB: PUSH CX
```

Save **CX** (precision).

## Step 8 - Conversion to ASCII

### Conversion to ASCII (I)

This routine is the entry point for the actual conversion to ASCII. At this point, **DX**:**BX**:**AX** is in the normalized form after undergoing previous adjustments.

```nasm
SYS0AAC: OR DH,80
```

Set the sign-bit (**bit 7**) in **DH**.

```nasm
SYS0AAF: MOV CL,84
SYS0AB1: SUB CL,AL
SYS0AB3: MOV AL,00
SYS0AB5: JZ 0AC1
```

Check if **DX**:**BX**:**AX** needs to be shifted right based on the exponent in **AL**. The amount of shifts are computed and stored in **CL**. Shifting will only be done on the significand (excluding **AL**, hence it is cleared prior).

```nasm
SYS0AB7: SHR DX,1
SYS0AB9: RCR BX,1
SYS0ABB: RCR AX,1
SYS0ABD: DEC CL
SYS0ABF: JNZ 0AB7
```

This shifts the number **DX**:**BX**:**AH**:**00h** to the right one bit at a time.

### Conversion to ASCII (II)

This routine does the actual conversion to ASCII. At this point **DX**:**BX**:**AH:00h** contains the final normalized form of the the number (without the exponent). In the final normalized form, the upper bits (bit **4-7**) of **DH** should already contain the first 'digit' in base 10.

```nasm
SYS0AC1: MOV SI,000C
```

Convert up to **0Ch**/**12** digits.

```nasm
SYS0AC4: MOV CH,DH
SYS0AC6: MOV CL,04
SYS0AC8: SHR CH,CL
```

Get the upper bits of the number (in **DH**) into **CH**.

```nasm
SYS0ACA: ADD CH,30
SYS0ACD: ES:
SYS0ACE: MOV [DI],CH
```

Convert the base digit in **CH** to ASCII by adding it to '**0**'/**30h**.

```nasm
SYS0AD0: AND DH,0F
```

Clear the upper bits of the number.

```nasm
SYS0AD3: PUSH DX
SYS0AD4: PUSH BX
SYS0AD5: PUSH AX
SYS0AD6: SHL AX,1
SYS0AD8: RCL BX,1
SYS0ADA: RCL DX,1
SYS0ADC: SHL AX,1
SYS0ADE: RCL BX,1
SYS0AE0: RCL DX,1
SYS0AE2: POP CX
SYS0AE3: ADD AX,CX
SYS0AE5: POP CX
SYS0AE6: ADC BX,CX
SYS0AE8: POP CX
SYS0AE9: ADC DX,CX
SYS0AEB: SHL AX,1
SYS0AED: RCL BX,1
SYS0AEF: RCL DX,1
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
SYS0AF1: INC DI
SYS0AF2: DEC SI
SYS0AF3: JNZ 0AC4
```

Move the buffer pointer (**DI**) then decrease the count (**SI**). Repeat the process until all digits have been converted.

```nasm
SYS0AF5: ES:
SYS0AF6: MOV BYTE PTR [DI],00
```

**NULL** (**00h**) terminate the ASCII string conversion.

```nasm
SYS0AF9: POP CX
```

Restore **CX** (saved in **SYS**:**0AAB**).

```nasm
SYS0AFA: RET
```

Return.

## String Operation (I)
```nasm
SYS0AFB: PUSH BP
SYS0AFC: MOV BP,SP
SYS0AFE: SUB SP,+08
SYS0B01: XOR AX,AX
SYS0B03: MOV [BP-08],AX
SYS0B06: MOV [BP-06],AX
SYS0B09: MOV [BP-04],AX
SYS0B0C: JCXZ 0B6F
SYS0B0E: ES:
SYS0B0F: MOV AL,[DI]
SYS0B11: MOV [BP-02],AL
SYS0B14: CMP AL,20
SYS0B16: JZ 0B20
SYS0B18: CMP AL,2B
SYS0B1A: JZ 0B20
SYS0B1C: CMP AL,2D
SYS0B1E: JNZ 0B22
SYS0B20: INC DI
SYS0B21: DEC CX
SYS0B22: CALL 0BA7
SYS0B25: JB 0B6F
SYS0B27: OR BX,BX
SYS0B29: JZ 0B35
SYS0B2B: XOR BX,BX
SYS0B2D: JCXZ 0B46
SYS0B2F: ES:
SYS0B30: CMP BYTE PTR [DI],2E
SYS0B33: JNZ 0B46
SYS0B35: JCXZ 0B6F
SYS0B37: ES:
SYS0B38: CMP BYTE PTR [DI],2E
SYS0B3B: JNZ 0B6F
SYS0B3D: INC DI
SYS0B3E: DEC CX
SYS0B3F: CALL 0BA7
SYS0B42: JB 0B6F
SYS0B44: NEG BX
SYS0B46: JCXZ 0B72
SYS0B48: ES:
SYS0B49: MOV AL,[DI]
SYS0B4B: CMP AL,45
SYS0B4D: JZ 0B53
SYS0B4F: CMP AL,65
SYS0B51: JNZ 0B72
SYS0B53: INC DI
SYS0B54: DEC CX
SYS0B55: PUSH BX
SYS0B56: CALL 08D1
SYS0B59: POP BX
SYS0B5A: JB 0B6F
SYS0B5C: ADD BX,AX
SYS0B5E: MOV SI,DX
SYS0B60: CWD
SYS0B61: CMP SI,DX
SYS0B63: JNZ 0B6F
SYS0B65: CMP AX,0040
SYS0B68: JGE 0B6F
SYS0B6A: CMP AX,FFC0
SYS0B6D: JG 0B72
SYS0B6F: STC
SYS0B70: JMP 0BA3
SYS0B72: PUSH CX
SYS0B73: PUSH DI
SYS0B74: MOV CL,BL
SYS0B76: MOV AX,[BP-08]
SYS0B79: MOV BX,[BP-06]
SYS0B7C: MOV DX,[BP-04]
SYS0B7F: CMP CL,DC
SYS0B82: JGE 0B8E
SYS0B84: PUSH CX
SYS0B85: MOV CL,DC
SYS0B87: CALL 0BE5
SYS0B8A: POP CX
SYS0B8B: ADD CL,24
SYS0B8E: CALL 0BE5
SYS0B91: POP DI
SYS0B92: POP CX
SYS0B93: JB 0BA3
SYS0B95: OR AL,AL
SYS0B97: JZ 0BA3
SYS0B99: CMP BYTE PTR [BP-02],2D
SYS0B9D: CLC
SYS0B9E: JNZ 0BA3
SYS0BA0: OR DH,80
SYS0BA3: MOV SP,BP
SYS0BA5: POP BP
SYS0BA6: RET
```

```nasm
SYS0BA7: XOR BX,BX
SYS0BA9: JCXZ 0BE4
SYS0BAB: ES:
SYS0BAC: MOV AL,[DI]
SYS0BAE: SUB AL,3A
SYS0BB0: ADD AL,0A
SYS0BB2: JNB 0BE4
SYS0BB4: INC BX
SYS0BB5: INC DI
SYS0BB6: DEC CX
SYS0BB7: PUSH BX
SYS0BB8: PUSH CX
SYS0BB9: PUSH DI
SYS0BBA: CBW
SYS0BBB: CWD
SYS0BBC: CALL 07EB
SYS0BBF: MOV CX,AX
SYS0BC1: MOV SI,BX
SYS0BC3: MOV DI,DX
SYS0BC5: MOV AX,[BP-08]
SYS0BC8: MOV BX,[BP-06]
SYS0BCB: MOV DX,[BP-04]
SYS0BCE: CALL 0C71
SYS0BD1: JB 0BDF
SYS0BD3: CALL 0584
SYS0BD6: MOV [BP-08],AX
SYS0BD9: MOV [BP-06],BX
SYS0BDC: MOV [BP-04],DX
SYS0BDF: POP DI
SYS0BE0: POP CX
SYS0BE1: POP BX
SYS0BE2: JNB 0BA9
SYS0BE4: RET
```

## Conversion - Step 5

This is step 5 of the conversion. At this point:
- **ES**:**DI** points to the temporary buffer
- **DX**:**BX**:**AX** holds the ***Real*** number being converted
- **CX** adjusted exponent.

```nasm
SYS0BE5: CMP CL,DA
SYS0BE8: JL 0C33
SYS0BEA: CMP CL,26
SYS0BED: JG 0C33
```

Return immediately if adjusted exponent is not in the range **DAh**/**-38** <= **CX** <= **26h**/**38**.

```nasm
SYS0BEF: PUSH DX
SYS0BF0: PUSH BX
SYS0BF1: PUSH AX
```

Save the number.

```nasm
SYS0BF2: OR CL,CL
SYS0BF4: PUSHF
```

Check exponent (**CL**) then save the results (in **Flags**) to stack.

```nasm
SYS0BF5: JNS 0BF9
SYS0BF7: NEG CL
```

If exponent is negative then make it positive (reverse the sign).

```nasm
SYS0BF9: MOV BL,CL
SYS0BFB: AND BL,FC
```

Copy the exponent in **BL** and clear bits 0-1.

```nasm
SYS0BFE: MOV BH,BL
SYS0C00: SHR BL,1
```

Copy **BL** to **BH** then shift **BL** to the right once.

```nasm
SYS0C02: ADD BL,BH
SYS0C04: XOR BH,BH
SYS0C06: LEA DI,[BX+0C35]
```

Calculate lookup index to number table in **SYS:0C35**.

```nasm
SYS0C0A: CS:
SYS0C0B: MOV AX,[DI]
SYS0C0D: CS:
SYS0C0E: MOV BX,[DI+02]
SYS0C11: CS:
SYS0C12: MOV DX,[DI+04]
```

Load ***Real*** number in **ES**:[**DI**] to **DX**:**BX**:**AX**.

```nasm
SYS0C15: AND CL,03
SYS0C18: JZ 0C21
```

Clear top bits (2-7) of the exponent in **CL**. Check if **DX**:**BX**:**AX** requires adjustment (based on the exponent in **CL**).

```nasm
SYS0C1A: CALL 0C71
```

If adjustments are needed go to step 6 (**SYS:0C71**) and do so.

```nasm
SYS0C1D: DEC CL
SYS0C1F: JNZ 0C1A
```

Keep adjusting while **CL** is non-zero.

```nasm
SYS0C21: MOV CX,AX
SYS0C23: MOV SI,BX
SYS0C25: MOV DI,DX
```

Copy the number in **DX**:**BX**:**AX** to **DI**:**SI**:**CX**.

```nasm
SYS0C27: POPF
```

Restore flags.

```nasm
SYS0C28: POP AX
SYS0C29: POP BX
SYS0C2A: POP DX
```

Restore the number **DX**:**BX**:**AX**

```nasm
SYS0C2B: JS 0C30
```

Check the result (that was saved in **SYS:0BF4**). If the exponent is a negative, proceed to step 7a on **SYS:074A**

```nasm
SYS0C2D: JMP 0647
```

Otherwise proceed to step 7b in **SYS:0647**.

```nasm
SYS0C30: JMP 074A
```

Jump to step 7a in **SYS:074A**.

## Number out of range
```nasm
SYS0C33: STC
SYS0C34: RET
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
SYS0C71: OR AL,AL
SYS0C73: JZ 0CBE
```

Return immediately if exponent (in **AL**) is ***0***.

```nasm
SYS0C75: PUSH CX
SYS0C76: PUSH SI
```

Save **CX** and **SI**.

```nasm
SYS0C77: OR DH,80
```

Set the sign bit of the **MSB** (bit 7 in **DH**).

```nasm
SYS0C7A: MOV CL,AL
```

Copy the exponent in **AL** to **CL**.

```nasm
SYS0C7C: XOR AL,AL
```

Clear exponent in **AL**.

```nasm
SYS0C7E: PUSH DX
SYS0C7F: PUSH BX
SYS0C80: PUSH AX
SYS0C81: SHR DX,1
SYS0C83: RCR BX,1
SYS0C85: RCR AX,1
SYS0C87: SHR DX,1
SYS0C89: RCR BX,1
SYS0C8B: RCR AX,1
SYS0C8D: POP SI
SYS0C8E: ADD AX,SI
SYS0C90: POP SI
SYS0C91: ADC BX,SI
SYS0C93: POP SI
SYS0C94: ADC DX,SI
```

This entire sequence adjusts the number by scaling **DX**:**BX**:**AX** by **5/4** or (**1.25**).

```nasm
SYS0C96: JNB 0CA3
```

Check if further adjustments are needed on an overflow.

```nasm
SYS0C98: RCR DX,1
SYS0C9A: RCR BX,1
SYS0C9C: RCR AX,1
SYS0C9E: ADD CL,01
SYS0CA1: JB 0CBC
```

Shift **DX**:**BX**:**AX** to the right by 1 then increment exponent in **CL**. If this results in a overflow in the exponent, return immediately.

```nasm
SYS0CA3: ADD AX,0080
SYS0CA6: ADC BX,+00
SYS0CA9: ADC DX,+00
SYS0CAC: JNB 0CB5
```

Bias the exponent in **AL**. Check if further adjustments are needed on overflow.

```nasm
SYS0CAE: RCR DX,1
SYS0CB0: ADD CL,01
SYS0CB3: JB 0CBC
```

Shift **DX** to the right by 1 then increment the exponent in **CL**. If this causes an overflow then return immediately.

```nasm
SYS0CB5: AND DH,7F
SYS0CB8: MOV AL,CL
SYS0CBA: ADD AL,03
```

If there were no adjustments after **SYS:0CA9** (or **SYS:0CB0**), then the net effect of this code is to actually multiply **DX**:**BX**:**AX** by **10** (**5/4** in the **significand**, **2**^**3**/**8** at the **exponent** in **AL**).

```nasm
SYS0CBC: POP SI
SYS0CBD: POP CX
SYS0CBE: RET
```

## Conversion - Step 1 (Entry Point)

This is the entrypoint to **Str**(***Real***, ***String***).

```nasm
SYS0CBF: PUSH BP
SYS0CC0: MOV BP,SP
SYS0CC2: SUB SP,+40
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
SYS0CC5: MOV AX,[BP+10]
SYS0CC8: MOV BX,[BP+12]
SYS0CCB: MOV DX,[BP+14]
```

Load the ***Real*** number from the stack into **DX**:**BX**:**AX**.

```nasm
SYS0CCE: MOV CX,[BP+0C]
```

Load precision from the stack into **CX**.

```nasm
SYS0CD1: OR CX,CX
SYS0CD3: JNS 0CE3
```

If **CX** is a positive number, then the precision is fixed, proceed to the next step of the conversion.

```nasm
SYS0CD5: MOV CX,0006
SYS0CD8: SUB CX,[BP+0E]
SYS0CDB: CMP CX,-02
SYS0CDE: JLE 0CE3
SYS0CE0: MOV CX,FFFE
```

This checks if the desired precision (= **FFFFh** max) compared to the width of the representation is enough. It sets the minimum to **2** (**FFFEh/-02**) digits.

```nasm
SYS0CE3: LEA DI,[BP-40]
SYS0CE6: PUSH SS
SYS0CE7: POP ES
SYS0CE8: CALL 0978
```

Point **ES**:**DI** to the temporary buffer reserved earlier (**SS**:[**BP-40**]). Move to the next part of the conversion (**SYS:0978**).

```nasm
SYS0CEB: PUSH DS
SYS0CEC: MOV SI,DI
SYS0CEE: PUSH SS
SYS0CEF: POP DS
```

Set **DS**:**SI** to the temporary buffer (**SS**[**BP-40**]).

```nasm
SYS0CF0: LES DI,[BP+08]
```

Set **ES**:**DI** to the **Output buffer** (passed on the stack on the call to **Str** function).

```nasm
SYS0CF3: MOV DX,[BP+06]
```

Load the **Output buffer** length into **DX**.

```nasm
SYS0CF6: MOV AX,[BP+0E]
```

Load the **Width** parameter (passed on the stack on the call to **Str** function) into **AX**.

```nasm
SYS0CF9: CMP AX,DX
SYS0CFB: JLE 0CFF
SYS0CFD: MOV AX,DX
SYS0CFF: CMP CX,DX
SYS0D01: JLE 0D05
SYS0D03: MOV CX,DX
SYS0D05: CMP AX,CX
SYS0D07: JGE 0D0B
SYS0D09: MOV AX,CX
```

Compute for **AX** (string length of the conversion) and **CX** (precision).

```nasm
SYS0D0B: CLD
SYS0D0C: STOSB
```

Since **Output buffer** is a Pascal-string, store the length (**AL**) into the first byte (**Byte 0**).

```nasm
SYS0D0D: SUB AX,CX
SYS0D0F: JZ 0D19
```

Check if there are digits to copy. If there is none, fill **Output buffer** **NULL** (**00h**) on **SYS**:**0D19**.

```nasm
SYS0D11: PUSH CX
SYS0D12: MOV CX,AX
```

Preserve **CX** then copy the width in **AX** (less **CX** in **SYS**:**0D0D**).

```nasm
SYS0D14: MOV AL,20
SYS0D16: REPZ
SYS0D17: STOSB
```

Left-pad the **Output buffer** (fill with whitespace character **20h**).

```nasm
SYS0D18: POP CX
SYS0D19: REPZ
SYS0D1A: MOVSB
```

Restore **CX** (precision of the string conversion) then copy the digits from **DS**:**SI** to **ES**:**DI**.

```nasm
SYS0D1B: POP DS
```

Restore **DS** (saved in **SYS**:**0CEB**).

```nasm
SYS0D1C: MOV SP,BP
```

Restore **SP** (modified in **SYS**:**0CC2**). This *"removes"* any space it has reserved on the stack.

```nasm
SYS0D1E: POP BP
SYS0D1F: RETF 0010
```

Restore BP and pop off **10h**/**16** bytes from the stack (**8** parameters) upon return.

## String operation (II)
```nasm
SYS0D22: PUSH BP
SYS0D23: MOV BP,SP
SYS0D25: LES DI,[BP+0A]
SYS0D28: ES:
SYS0D29: MOV CL,[DI]
SYS0D2B: XOR CH,CH
SYS0D2D: INC DI
SYS0D2E: JCXZ 0D39
SYS0D30: ES:
SYS0D31: CMP BYTE PTR [DI],20
SYS0D34: JNZ 0D39
SYS0D36: INC DI
SYS0D37: LOOP 0D30
SYS0D39: CALL 0AFB
SYS0D3C: JB 0D40
SYS0D3E: JCXZ 0D4B
SYS0D40: MOV CX,DI
SYS0D42: SUB CX,[BP+0A]
SYS0D45: XOR AX,AX
SYS0D47: XOR BX,BX
SYS0D49: XOR DX,DX
SYS0D4B: LES DI,[BP+06]
SYS0D4E: ES:
SYS0D4F: MOV [DI],CX
SYS0D51: POP BP
SYS0D52: RETF 0008
```

## Clear Data
```nasm
SYS0D55: MOV DI,0050
SYS0D58: PUSH DS
SYS0D59: POP ES
SYS0D5A: MOV CX,SizeOf(DATA)
SYS0D5D: SUB CX,DI
SYS0D5F: SHR CX,1
SYS0D61: XOR AX,AX
SYS0D63: CLD
SYS0D64: REPZ
SYS0D65: STOSW
SYS0D66: RET
```

Go [Back](../../README.md)