# SOC138 - LetsDefend

EventID - 77

alert - SOC138 - Detected Suspicious Xls File

event time - Mar 13, 2021, 08:20 PM

source address - 172.16.17.56

source hostname - Sofia

file name - `ORDER SHEET & SPEC.xlsm`

file hash (md5) - `7ccf88c0bbe3b29bf19d877c4596a8d4`

file size - 2.66 Mb

device action - Allowed

endpoint info
`Hostname: Sofia`
`Domain: LetsDefend`
`IP Address: 172.16.17.56`
`OS: Windows 10`
`Bit Level: 64`
`Client/Server: Client`
`Primary User: Sofia2020`
`Last Login: Oct 25, 2020, 11:44 PM`


terminal history (endpoint)


`2020-10-18 12:17   cd`
`2020-10-18 12:18   dir`
`2020-10-18 22:17   PowerSheLL -ENCOD IAAgAHMAZQB0ACoASQBUAEUATQAgAHYAYQByAGkAQQBCAEwAZQA6AGsAegBlAgIAFEAbABVACAAIAAoAFsAdABZAFAAZQBd...`


mixed-case `PowerSheLL` + base64 `-ENCOD` - AMSI/logging evasion pattern


VirusTotal - hash lookup (sha256: `7bcd31bd41686c32663c7cabf42b18c50399e3b3b4533fc2ff002d9f2e058813`)

popular threat label - trojan.acao/docdl

family labels - acao, docdl, valyria

code insights - obfuscation: heavily obfuscated variable/function names in macro

`CVE-2017-11882 `- Office Equation Editor memory corruption RCE

`ANY.RUN` - dynamic detonation

verdict - malicious activity (100/100)

tags - phishing, phish-doc, arch-doc

MITRE - T1566.001 Spearphishing Attachment - "Phishing document has been detected"

process tree

```
WinRAR.exe  C:\Users\admin\AppData\Local\Temp\7ccf88c0bbe3b29bf19d877c4596a8d4.zip
 └─ EXCEL.EXE  "...\7zSrBD4640.36109\ORDER SHEET & SPEC.xlsm"
     └─ DWWIN.EXE

EXCEL.EXE  "C:\Users\admin\Desktop\ORDER SHEET & SPEC.xlsm"
 └─ DWWIN.EXE
```

`EXCEL.EXE `spawning `DWWIN.EXE` (Dr. Watson) = crash handler firing - consistent with CVE-2017-11882 exploitation crashing EQNEDT32.EXE/WinWord-Excel after shellcode runs

sandbox OS - Win10 64bit

containment

threat indicator selected - "Unknown or unexpected outgoing internet traffic"

containment toggled on for Sofia endpoint

case closed - malicious, contained
