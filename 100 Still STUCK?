### Nothing is working, Try

| Condition | Action |
|-----------|--------|
| **Re-read the room** | Title, description, and task hints often contain indirect clues about the attack vector |
| **Full port scan** | `nmap -p- --min-rate 5000 $IP` — you may have missed a service on a high port |
| **UDP scan** | `nmap -sU --top-ports 20 $IP` — SNMP (161), TFTP (69), or other UDP services may be running |
| **Check all vhosts** | `ffuf -w subdomains.txt -u http://$IP -H "Host: FUZZ.domain.htb"` — add findings to /etc/hosts |
| **Re-enumerate web deeper** | Try bigger wordlists: `dirbuster-medium.txt` or `raft-large-directories.txt` |
| **Extensions missed** | `gobuster dir -u http://$IP -w wordlist.txt -x php,txt,html,bak,old,zip,conf` |
| **Read .bash_history** | `cat ~/.bash_history` — previous commands may reveal creds, paths, or techniques used |
| **Check every user's home** | `ls -la /home/*/` — look for notes, keys, dotfiles, flags |
| **grep for passwords everywhere** | `grep -rn "password\|passwd\|secret\|key" /var/www/ /opt/ /home/ /etc/ 2>/dev/null` |
| **Check /etc/passwd carefully** | Non-standard shells (`/bin/false`, `/usr/bin/nologin`) = can't SSH. Custom shells = interesting. Users with `/bin/bash` = targets |
| **Environment variables** | `env` and `printenv` — sometimes creds or paths are stored here |
| **Recheck sudo with fresh eyes** | `sudo -l` — re-run after lateral move, you may have new permissions |
| **Check running processes** | `ps aux` — a root process running a script you can write to is a privesc |
| **Writable directories** | `find / -writable -type d 2>/dev/null` — writable paths near crons or SUID binaries are gold |
| **Try every password you have** | Test collected creds against every user in /etc/passwd — password reuse is very common in THM |
| **Check for internal ports again** | `ss -tulpn` — a service may only start after you trigger something |
| **Look for backup/config files** | `find / -name "*.bak" -o -name "*.conf" -o -name "*.old" -o -name "*.config" 2>/dev/null` |
| **Read all logs** | `ls /var/log/` — auth.log, syslog, and app logs can leak usernames, paths, or errors |
| **Check capabilities again** | `getcap -r / 2>/dev/null` — easy to miss on first pass |
| **Try null/default creds** | `admin:admin`, `admin:password`, `root:root`, `guest:guest`, `user:user` |
| **Steganography on all images** | Download every image from the web app and run `steghide`, `binwalk`, `strings`, `exiftool` |
| **Wrong rabbit hole?** | If you've spent 30+ min on one path with no progress — step back, list ALL findings, pick a different one |

---

### Mental model when truly stuck

```
1. List EVERYTHING you know:
   - Open ports
   - Users found
   - Passwords/hashes found
   - Directories found
   - Services running internally
   - Files that looked interesting but you skipped

2. Ask: "What have I NOT tried yet?"
   - A port I didn't enumerate deeply
   - A user I found but never targeted
   - A file I opened but didn't analyse fully

3. Check if you need to CHAIN things:
   - LFI + log poisoning → RCE
   - Low-priv shell + cron + writable script → root
   - Creds from DB → SSH login → sudo privesc
```
