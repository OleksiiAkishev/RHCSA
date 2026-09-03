
## Chapter 2 — Essential Tools

### Quick references
- Search man pages:

```bash
man -k <keyword>
```

- Update man database:

```bash
mandb
```

### Task 1 — Set an environment variable for subshells
Make the variable `COLOR=red` available in every subshell.

```bash
# ensure ~/.bashrc exists and append the export
echo 'export COLOR=red' >> ~/.bashrc

# test in current shell
echo $COLOR

# test in a subshell
bash -c 'echo $COLOR'
```

Notes: use `export` so the variable is inherited by child shells.

### Task 2 — Find the command to change passwords

```bash
man -k "change user password"
# the command is `passwd`
passwd          # changes current user's password (no root required)
sudo passwd jack   # changing another user's password requires root
```

### Task 3 — Redirect stdout and stderr to a file
From your home directory, run `ls -al wergihl *` and capture both stdout and stderr into `/tmp/lsoutput`.

```bash
cd ~
# modern bash/ksh-style
ls -al wergihl * &> /tmp/lsoutput

# POSIX-compatible style
ls -al wergihl * > /tmp/lsoutput 2>&1

cat /tmp/lsoutput
```


## Chapter 3 — File management

### Task 1 — Create an archive containing `/home` and `/etc`

```bash
sudo -i
tar -cvf /root/essentials.tar /home /etc
ls -lh /root/essentials.tar
```

Notes: `-f` must immediately precede the archive filename.

### Task 2 — Copy and create a hard link

```bash
cp /root/essentials.tar /tmp
ln /tmp/essentials.tar /essentials.tar
ls -l /tmp/essentials.tar /essentials.tar
ls -i /tmp/essentials.tar /essentials.tar
```

### Task 3 — Rename and create a symbolic link

```bash
mv /essentials.tar /archive.tar
ln -s /archive.tar /root/link.tar
ls -l /root/link.tar
```

### Task 4 — Remove target and examine the symlink

```bash
rm -f /archive.tar
ls -l /root/link.tar   # shows a dangling symlink
rm -f /root/link.tar
```

### Task 5 — Compress the archive

```bash
gzip /root/essentials.tar
ls -lh /root/essentials.tar.gz
```


## Chapter 4 — Text files

### Task 1 — Show line 5 of `/etc/passwd`

```bash
head -n 5 /etc/passwd | tail -n 1
sed -n '5p' /etc/passwd
awk 'NR==5' /etc/passwd
```

### Task 2 — Find files containing a specific IP

```bash
grep -rl "198.168.1.10" /etc
```

### Task 3 — Revert a `sed -i` replacement

```bash
echo "The Server Administrator needs Administrator access." > ~/sed_test.txt
sed -i.bak 's/Administrator/root/g' ~/sed_test.txt

# revert by swapping or restoring backup
sed -i 's/root/Administrator/g' ~/sed_test.txt
# or
mv ~/sed_test.txt.bak ~/sed_test.txt
```

### Task 4 — Sort processes by memory (heaviest first)

```bash
ps aux | sort -k5 -rn | head
```

### Task 5 — Print/filter the 6th column from `ps aux`

```bash
ps aux | awk '{print $6}'
ps aux | awk '$6 > 10000'
```

### Task 6 — Delete line 6 from a file

```bash
sed -i '6d' ~/myfile
```


## Chapter 8 — Networking (lab tasks)

### Task 1 — Set FQDNs
- server1.example.com
- server2.example.com

### Task 2 — Configure DHCP and fixed addresses
- server1: primary interface DHCP, add secondary fixed address `192.168.4.210`
- server2: set fixed address `192.168.4.220`

### Task 3 — Verify connectivity

```bash
ping -c 3 server2.example.com   # from server1
ping -c 3 server1.example.com   # from server2
```

### Task 4 — Ensure DHCP provides default router and DNS
Verify DHCP scope contains gateway and DNS entries (DHCP server configuration).


## Chapter 9 — Working with software

1. List repositories in use:

```bash
dnf repolist
```

2. Search for the package (example: cache-only DNS name server) and inspect without installing:

```bash
dnf search <package_keyword>
dnf info <package_name>
dnf repoquery --list <package_name>    # shows files in package
rpm -qpi <package.rpm>                 # package metadata (if downloaded)
rpm -q --scripts <package.rpm>         # shows scripts in an RPM file
```

3. Install and then undo installation:

```bash
sudo dnf install -y <package_name>
sudo dnf remove -y <package_name>
```

4. Install Firefox for a single user (example using Flatpak or local install for that user):

```bash
# Flatpak per-user install (if Flatpak configured)
flatpak install --user flathub org.mozilla.Firefox
```


## Chapter 10 — Managing processes

1. Launch `dd` three times in background:

```bash
dd if=/dev/zero of=/dev/null &
dd if=/dev/zero of=/dev/null &
dd if=/dev/zero of=/dev/null &
```

2. Change priority of a process (example using PID):

```bash
renice -n -5 -p <pid>
renice -n -15 -p <pid>
```

3. Kill the `dd` processes:

```bash
pkill dd
```

4. Ensure `tuned` is installed and set an appropriate profile:

```bash
sudo dnf install -y tuned
sudo systemctl enable --now tuned
sudo tuned-adm profile virtual-guest
```


## Chapter 11 — Working with systemd

1. Install services and configure unit behavior:

```bash
sudo dnf install -y vsftpd httpd

# set default editor for systemctl (affects 'systemctl edit')
export SYSTEMD_EDITOR=vim

# Make httpd start vsftpd: create a drop-in
sudo systemctl edit --full httpd.service
# Add `Wants=vsftpd.service` under [Unit] and `Restart=on-failure` with `RestartSec=10` under [Service]

sudo systemctl daemon-reload
sudo systemctl enable --now vsftpd httpd
```

Adjust the unit file carefully (use `systemctl edit` to create a drop-in or edit the unit safely).
