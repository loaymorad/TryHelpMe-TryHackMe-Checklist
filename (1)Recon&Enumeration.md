
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
