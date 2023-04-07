# RegEx to clean up debug dumps

## Transform CODEXXX/SYSXXXX into labels
Replace SYS: in DUMP
```
^SYS:
SYS
```

Replace CODE: in DUMP
```
^CODE:
CODE
```

```
^CODE[\0-9A-F]{4}()
$0:
```

```
^SYS[\0-9A-F]{4}()
$0:
```

## Remove OpCodes
```
(^SYS[\0-9A-F]{4}:)(\s)([\0-9A-F]{12}\s(.+?))
$1 
```

```
(^CODE[\0-9A-F]{4}:)(\s)([\0-9A-F]{12}\s(.+?))
$1 
```