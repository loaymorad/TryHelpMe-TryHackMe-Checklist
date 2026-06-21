
### Got a hash or encoded string?

| Condition | Action |
|-----------|--------|
| **MD5** | CrackStation or `hashcat -m 0` |
| **SHA-512 / shadow** | `hashcat -m 1800 hash.txt rockyou.txt` |
| **ZIP / RSA key** | `zip2john file.zip > h.txt && john h.txt`<br>`ssh2john id_rsa > h.txt && john h.txt` |
| **Base64** | `echo "..." \| base64 -d` |
| **ROT13 / cipher** | CyberChef — try ROT13 first<br>*Ref: Madness* |
| **Hex → other encoding** | Chain in CyberChef: From Hex → Base64 / Base85 / etc.<br>*Ref: Different CTF* |
| **Custom encoding** | Signs: letters only, alternating case, even length. Try CyberChef "Magic", then write decode script.<br>*Ref: Anonymous Playground* |

---

### Have username, need password? → Brute force

| Condition | Action |
|-----------|--------|
| **SSH / FTP** | `hydra -l <user> -P passwords.txt ssh://$IP` |
| **HTTP Basic Auth** | `hydra -l <user> -P passwords.txt $IP http-head /`<br>*Ref: Year of the Fox* |
| **Web param** | `ffuf -w wordlist.txt -u "http://target/?secret=FUZZ"`<br>*Ref: Madness* |
| **WordPress login** | `wpbrute -u admin -w passwords.txt -target http://target/wp-login.php`<br>*Ref: Internal* |
| **Local su (sucrack)** | `./sucrack -u <user> -w 100 wordlist.txt`<br>Pattern tip: `sed 's/^/prefix/' wordlist.txt > mutated.txt`<br>*Ref: Different CTF* |

---

### Suspicious image / binary file?

| Condition | Action |
|-----------|--------|
| **steghide (blank pass)** | `steghide extract -sf file.jpg` |
| **stegcracker** | `stegcracker file.jpg wordlist.txt`<br>*Ref: Different CTF* |
| **binwalk** | `binwalk -e file.png` |
| **strings** | `strings binary \| awk 'length($0) > 10'` |
| **Wrong magic number** | `hexeditor file.jpg` → fix to `FF D8 FF`<br>*Ref: Madness* |

