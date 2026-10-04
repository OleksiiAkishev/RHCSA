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

5. Filesystem Hierarchy Standard (FHS) - is the convention that says where Linux files each type of file.

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