1. Kernel and Linux
uname (1)            - print system information
uname (2)            - get name and information about current kernel
-r print kernel release
-m print the machine hardware name

2. VM processes
nproc (1)            - print the number of processing units available

3. Working with data
- Output the number of lines of the file
  cat -n <source>

4. Get help of command line
- type command
  Display information about command type
Check all
  type -a echo

Make always sure with the **type** command that the right command is called. As example
  **echo --help** outputs just --help
Because
  type -a echo
    echo is a shell builtin
    echo is /usr/bin/echo
    echo is /bin/echo

Where the echo of the shell builtin takes precedence over the rest. Thus **/usr/bin/echo --help** gives the desire output

Troubleshooting with man page

 Symptom                                          Likely cause                              Check
 ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
 No manual entry for xxx on a command that works  command built into the shell              type xxx, then help xxx
 No manual entry for xxx on a real program        page not installed                        man -w xxx, then install the doc package
 nothing appropriate but man works                mandb index missing or out of date        rebuild the index with mandb
 nothing appropriate and man -w returns nothing   the pages themselves are missing          install man-pages / manpages
 apropos: command not found                       man-db not installed                      see the packages at the top of the guide
 man shows the wrong documentation                wrong section picked                      whatis name, then man <number> name
 <command> --help prints --help                   built-in command that ignores the option  help command
 the man page does not match what runs            another binary is being run               type -a command

5. **Filesystem Hierarchy Standard (FHS)** - is the convention that says where Linux files each type of file.

Hierarchy

a. / - called root
**Note** / != /root, the /root it is a home directory for the root user (admin), can check with **su -** and **pwd**


b. /bin /sbin /lib are no longer directories
Historically, /bin and /sbin contained the binaries needed at boot, before /usr was mounted; /usr/bin and /usr/sbin
contained the rest. That separation no longer has a reason to exist, and modern distributions have removed it.
Verify:
    ls -ld /bin /sbin /lib /lib64
        lrwxrwxrwx 1 root root 7 Apr 22  2024 /bin -> usr/bin
        lrwxrwxrwx 1 root root 7 Apr 22  2024 /lib -> usr/lib
        lrwxrwxrwx 1 root root 9 Apr 22  2024 /lib64 -> usr/lib64
        lrwxrwxrwx 1 root root 8 Apr 22  2024 /sbin -> usr/sbin
Can see, they are all symbolic links to usr

The distinction 
     **/usr/bin** carries everybody's commands, 
     **/usr/sbin** the administration ones (fdisk, useradd, iptables, etc)
A binary that cannot be found as an ordinary user is often simply in sbin, outside your PATH

c. /var/run has been replaced by /run
    ls -ld /var/run /var/lock
        lrwxrwxrwx 1 root root 9 Feb 10  2026 /var/lock -> /run/lock
        lrwxrwxrwx 1 root root 4 Feb 10  2026 /var/run -> /run

d. **/usr**, **/usr/local** and **/opt**: three ways of installing software

 Path         Content (guide)
 ─────────────────────────────────────────────────────────────────────
 /usr/bin/    user commands
 /usr/sbin/   administration commands
 /usr/lib/    shared libraries and support files
 /usr/share/  documentation and architecture-independent data
 /usr/local/  software installed manually, outside the package manager

 /etc, /var, /srv: the configuration, the state, the served data                                                                                             

These three directories answer three distinct questions, and mixing them up is the most frequent filing mistake.                                            

/etc: what you decide. Configuration only, in editable text files, one subdirectory per service. ls -d /etc/*/ lists them, and ls -d /etc/*/ | wc -l counts 126 of them on the test machine: /etc/apache2/,
/etc/apt/, /etc/cron.d/, /etc/ssh/, /etc/systemd/ and one hundred and twenty-one others.                                                                    

The golden rule of the guide is worth remembering word for word: never modify a file in /usr/lib/ or /usr/share/, copy it or override it in /etc/.

6. Redirection operators
>> appends
> overwrites,
    The > trap: it empties the file before the command starts
2> redirects errors (descriptor 2)
2>&1 make descriptor 2 point where descriptor 1 points


## Useful commands and tools

1. Work with the lines, chars 
    wc - print newline, word, and byte counts for each file
e.g. count number of lines in the file
    wc -l text.txt

2. Grep and regular expressions
    a. find the lines which contain only 5xx
        grep -E '\b5[0-9]{2}\b' access.log
    where
        \b a boundary(wall) which help to match the case, what counts as a boundary: Spaces / Tabs / Newlines, Punctuation & Symbols: quotes ("), colons (:), slashes (/), brackets ([ ]), commas (,), periods (.), equals (=), start or End of a line. 
        5[0-9]{2}: Matches 5 followed by any 2 digits (00 through 99)
        -E (Extended Regex): Keeps the syntax clean

3. Distinct/sort by values
    sort -u <file_name>
        where -u is unique

3.1 To get the distinct values and sort them
    awk '{print $1}' | sort -u

4. Extract a specific column by delimiter
    4.1 cut -d':' -f2 file.csv
    4.2 awk -F';' '{print $2}' file.csv
    4.3 Count similar occurances
        cut -d';' -f2 file.csv | sort | uniq -c

5. Count/Sum columns in the row
    awk -F';' '{print NF}'
        where: 
             NF Prints the number of fields (columns)

6. Replace one delimiter with another
    a. tr ';' ',' < input.txt > output.txt
    b. sed 's/;/,/g' input.txt > output.txt
        To modify in the same file, use -i flag
            sed -i 's/;/,/g' input.txt
    where:
        /g: Global flag — essential because it tells sed to replace every semicolon on every line, not just the first one.

7. Sum numbers from filtered columns per row
    a. awk -F';' '{sum += $4} END {print sum}' file.csv

        awk -F';' ' { sum += $4 }   END   { print sum } ' ventes.csv
           \____________/  /   \  \___________/
                 |           |          |
        Runs on every line  When?  Runs 1 time at the end

    awk 'BEGIN { ... }  { ... }  END { ... }' filename
    BEGIN { ... }	Before reading any lines	Exactly 1 time	Initialization (e.g., printing headers, setting variables).
    { ... } (Main)	During file processing	Once per line in the file	Processing line-by-line (accumulating sums, filtering).
    END { ... }	After reaching End-Of-File (EOF)	Exactly 1 time	Printing final results (totals, averages, counts

    b. awk -F';' 'NR > 1 {sum += $4} END {print sum}' file.csv
        where NR > 1 to skip the header


## Inodes and links

**Spot on!** Your definition of an inode is correct: an inode is a data structure that stores all **metadata** (permissions, owner, size, timestamps) and **pointers to the physical data blocks** on the disk. It does *not* store the file name.

Here is the short and clear breakdown of how links relate to it:

---

### 1. Hard Links (The Twin)

* **Inode:** Shares the **exact same inode** as the original file.
* **How it works:** A hard link is just another name pointing directly to the same inode number.
* **Key rule:** They act as equal peers. If you edit or delete one, the other updates or remains intact. The physical data is only deleted when *all* hard links to that inode are removed (link count drops to 0).
* **Limitation:** Cannot cross filesystem/partition boundaries.

### 2. Symbolic / Soft Links (The Shortcut)

* **Inode:** Has a **completely separate, new inode** of its own.
* **How it works:** A symlink is a tiny independent file whose contents are simply a text string pointing to the *path* of the original file (e.g., `/home/user/original.txt`).
* **Key rule:** If you delete the original file, the symlink breaks ("dangling link") because the path it points to no longer exists.
* **Advantage:** Can easily cross filesystems and point to directories.

---

### Quick Comparison

| Feature | Hard Link | Symbolic (Soft) Link |
| --- | --- | --- |
| **Shares Inode?** | **Yes** (Same inode) | **No** (New unique inode) |
| **Target Deleted?** | Data is **safe** (via other links) | Link becomes **broken/dead** |
| **Cross Filesystem?** | **No** | **Yes** |