# Cybersecurity Investigations

A collection of my cybersecurity investigations from Hack The Box, TryHackMe, and Let'sDefend.

I document my findings, investigation steps, and the techniques involved in each case.

## Investigations

| Investigation | Platform | Topics |
|---|---|---|
| [Imagery](Hackthebox/Machines/Imagery/README.md) | Hack The Box | XSS, LFI, command injection, privilege escalation |
| [Telly](Hackthebox/Sherlocks/SOC/Telly/Readme.md) | HTB Sherlocks | Linux logs, persistence, credential access, data exfiltration |
| [MSHTML Malicious Document](Letsdefend/MSHTML-(Malicious-Document-Investigation)/Readme.md) | Let'sDefend | Malicious documents, CVE-2021-40444, IOC analysis |
| [First Shift - Portal Drop](THM/Firstshift/Readme.md) | TryHackMe | Brute force, access logs, web shells, reverse shells |

## Skills

- Log analysis and incident investigation
- Malware and malicious document analysis
- Network and web application security
- IOC analysis and threat intelligence
- Linux investigation and privilege escalation
- MITRE ATT&CK

## Tools

Wazuh, ELK Stack, Suricata, Splunk, Nmap, Burp Suite, VirusTotal, and other tools used during investigations.

## Repository Structure

```text
Investigations/
├── Hackthebox/
│   ├── Machines/
│   │   └── Imagery/
│   │       ├── README.md
│   │       └── raw-investigation.md
│   └── Sherlocks/
│       └── SOC/
│           └── Telly/
│               ├── Readme.md
│               └── raw-investigation.md
├── Letsdefend/
│   ├── MSHTML-(Malicious-Document-Investigation)/
│   │   └── Readme.md
│   └── DLL hijacking/
│       └── Readme.md
├── THM/
│   ├── Firstshift/
│   │   ├── Readme.md
│   │   └── investigation.md
│   └── Phising unfolding
├── LICENSE
└── README.md
```

## Ongoing

I'm adding more investigations and documenting what I learn along the way.
