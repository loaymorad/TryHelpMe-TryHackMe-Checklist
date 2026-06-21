
### Common flag locations & search commands

| Condition | Action |
|-----------|--------|
| **Search by name** | `find / -name "*flag*" 2>/dev/null`<br>`find / -name "user.txt" -o -name "root.txt" 2>/dev/null` |
| **Common paths** | `/home/<user>/user.txt` · `/root/root.txt` · `/var/www/html/` |
| **Hidden dotfiles** *(new)* | Flags can be hidden as dotfiles with unusual names — always use `ls -la` not just `ls`<br>`ls -la /home/<user>/`<br>*Ref: Year of the Fox (.did-you-think0I-was-useless.root in rascal's home)* |
| **Odd filenames** | Flags don't always follow user.txt/root.txt — `ls` carefully in web roots<br>*Ref: Different CTF (wwe3bbfla4g.txt)* |
| **Flags swapped** | Some rooms flip locations — try `cat /root/user.txt` directly<br>*Ref: Wonderland* |
| **Hash in URL** | MD5 of numbers 0–13 as URL path<br>*Ref: Corridor* |
