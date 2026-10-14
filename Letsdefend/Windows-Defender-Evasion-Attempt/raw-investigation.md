LetsDefend SOC321

Windows Defender Evasion Attempt


Rule - SOC321

EventID - 299

Hostname - Elenora

Dest IP - 172.16.17.126

Process - RUNDLL32.EXE

EDR/AV Action - Not Detected

attacker IP - 89.187.177.73

target username - LetsDefend

target host - 172.16.17.126 (Elenora) - Windows 10 client

RDP bruteforce started - Sep 12 2024, 07:04 AM

failed attempts (EventID 4625) - count - 10 

successful login - 07:07 AM, 11th attempt

source_port - 41589

destination_port - 3389

attacker is in - interactive RDP session confirmed

post-login recon commands 

`07:07:36 - whoami`
`07:07:40 - whoami /all`
`07:07:51 - net user`
`07:08:03 - Get-WmiObject -Class Win32_UserAccount`
`07:08:19 - Get-NetTCPConnection`
`07:08:35 - net share`
`07:08:40 - Get-Service | Where-Object { $_.Status -eq 'Running' }`
`07:08:49 - Get-Service -Name WinDefend`
`07:08:56 - Get-MpPreference | Select-Object -Property DisableRealtimeMonitoring`
`07:09:03 - Get-NetFirewallProfile`

recon = user/privilege check, local+domain user enum, network conns, shares, running services, defender status, firewall profile


LOLBIN execution test - 07:09:22 AM

2nd run (obfuscated) - 07:09:35 AM

Sysmon EventID 1 - process create

`Image: C:\Windows\System32\rundll32.exe`
`OriginalFileName: RUNDLL32.EXE`
`User: EC2AMAZ-ILGVOIN\LetsDefend`
`ProcessId: 224`
`IntegrityLevel: Medium`


hashes

`MD5=9F0E80EC26028E83F1EF371D7B44DE3DF`
`SHA256=9F1E56A3BF293AC536CF4B8DAD57040797D62DBB0CA19C4ED9683B5565549481`

parent process

`ParentProcessGuid: {a584806d-939d-66e2-5201-000000006201}`
`ParentProcessId: 6760`
`ParentImage: C:\Windows\System32\cmd.exe`
`ParentCommandLine: "C:\Windows\system32\cmd.exe"`
`ParentUser: EC2AMAZ-ILGVOIN\LetsDefend`

child process of rundll32 (search term used - mshtml)

`ProcessId: 6428`
`ParentProcessId: 224`
`ParentImage: C:\Windows\System32\rundll32.exe`
`ParentUser: EC2AMAZ-ILGVOIN\LetsDefend`

unrelated/secondary event noticed - DismHost.exe spawn

`OriginalFileName: DismHost.exe`
`CommandLine: C:\Users\LETSDE~1\AppData\Local\Temp\BBEC8D60-D67E-4F21-8C73-CD415D7D744A\dismhost.exe {9891A1D8-49D9-4B5F-9A27-9729AAF4F9F3}`
`ParentImage: C:\Windows\System32\cleanmgr.exe`
`ParentCommandLine: C:\Windows\System32\cleanmgr.exe /autoclean /d C:`
`IntegrityLevel: High`

logged separately from the rundll32 chain, parent is cleanmgr.exe /autoclean - disk cleanup utility spawning DismHost, High integrity. kept as a side note, not tied directly to the attacker's interactive chain.


windows defender status checked post-incident

`Get-Service -Name WinDefend -> Running`
`Get-MpPreference | Select DisableRealtimeMonitoring -> False (real-time monitoring ON)`


Get-MpThreatDetection run - confirms Defender DID catch the activity

detection time - 07:09:22 AM (same time as first rundll32 execution)


anti-forensic activity after - 07:11:15 AM

`The event logging service has shutdown`
`The system time was changed`

