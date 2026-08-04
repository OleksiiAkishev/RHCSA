1. Check existing keys
```bash
ls -la ~/.ssh
```

2. Delete if no need
```bash
rm -f ~/.ssh/<path_to_key>
```

3. Create a new key

```bash
ssh-keygen -t ed25519 -C "your_github_email@example.com" -f ~/.ssh/github_ed25519
```
3.1 Enter passphrase

3.2 Expected output
Your identification has been saved in /home/user/.ssh/github_ed25519
Your public key has been saved in /home/user/.ssh/github_ed25519.pub
The key fingerprint is:
SHA256:zlyjCxq3fTLpJloIG3T1256TcB5x5ljV/+o94R6gMng your_github_email@example.com
The key's randomart image is:
+--[ED25519 256]--+
|                 |
|     .           |
|  . . .        . |
| . .   .  .   . .|
|  o     S oo .  .|
|   + . +.=o.+ ...|
|  . o + **.B ...o|
|     =.+*oE.o  +o|
|    o..++=o+ .+o+|
+----[SHA256]-----+

4. Display a new key in shell, copy and add in yout Github settings
```bash
cat ~/.ssh/github_ed25519.pub
```

4.1 Copy complete output as:
```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI.... your_email@example.com
```

5. Load a new key to be used by SSH agent

Run agent
```bash
eval "$(ssh-agent -s)"
```

Then add a new key

```bash
ssh-add ~/.ssh/github_ed25519
```

Enter passphrase will be requested

Expected Output: Identity added


6. Try to authenticate
```bash
ssh -T git@github.com
```

Expected output:
Hi <NAME>! You've successfully authenticated, but GitHub does not provide shell access