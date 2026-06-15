
#### Common flag locations & search commands

| Condition | Action |
|---|---|
| Search by name | `find / -name "*flag*" 2>/dev/null` / `find / -name "user.txt" -o -name "root.txt" 2>/dev/null` |
| Common paths | `/home/<user>/user.txt` · `/root/root.txt` · `/home/<user>/flag.txt` |
| Flags swapped | Some rooms flip locations try `cat /root/user.txt` directly ·|
| Hash in URL | MD5 of numbers 0–13 as URL path ·|
