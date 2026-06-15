
#### Got a hash?

| Condition | Action |
|---|---|
| MD5 | CrackStation or `hashcat -m 0` |
| SHA-512 / shadow | `hashcat -m 1800 hash.txt rockyou.txt` |
| ZIP / RSA key | `zip2john file.zip > h.txt && john h.txt` / `ssh2john id_rsa > h.txt && john h.txt` |
| Base64 encoded | `echo "..." \| base64 -d` |
| ROT13 / cipher | Use CyberChef, try ROT13 first |
| Custom encoding | Spot the signs: letters only, alternating case, even length, no standard encoding. Write a decode script based on the pattern. <br> **Clue:** pairs of chars where `(ord(a) & 31) + (ord(b) & 31)` maps to alphabet position. <br> Always try CyberChef "Magic" mode first it auto-detects many schemes |

#### Have username, need password? -> Brute force

| Condition | Action |
|---|---|
| SSH / FTP | `hydra -l <user> -P rockyou.txt ssh://$IP` ·|
| Web param | `ffuf -w wordlist.txt -u "http://target/?secret=FUZZ"` ·|

#### Suspicious image file? -> Steganography

| Condition | Action |
|---|---|
| steghide | `steghide extract -sf file.jpg` (try blank password) ·|
| binwalk | `binwalk -e file.png` ·|
| strings | `strings binary_file` ·|
| Wrong magic number | Fix hex header: `hexeditor file.jpg` -> `FF D8 FF` for JPEG ·|
