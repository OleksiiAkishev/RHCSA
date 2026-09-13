# Exam Objectives

# Additional exam objectives --> try to search in the book for the key words as Exam objectives/tips

## Archive

a.Create an archive 
b.List the contents of an archive 
c.Extract an archive 
d.Compress and uncompress archives


# Chapter 9 Managing Softwares

- you have to be able to configure a repository client to specify yourself which repository you want to use, which is an important skill. Telling your server which repository to use is not difficult, but it is important that you know how to do it (for the RHCSA exam, too).
Tip:
To learn how to work with repositories and software packages, do not use the repositories that are provided by default. If you have installed RHEL, do not register using subscription-manager. If you overlooked this requirement while installing earlier, you can use subscription-manager unregister to remove all registration.

*This:
In older versions of RHEL, you needed to memorize how to create a repository client file manually. In RHEL 10, the dnf config-manager tool is available, even in a minimal installation, to create the repository client file for you.
--> wondering if I need to go in details for that, as I am planning for version 10, not sure I need those knoweledges.


If you are are using the dnf config-manager, to disable a gpg check (which loads a lot of additional packages) - modify a file /etc/yum.conf by set the gpgcheck=0

Repositories for the dnf manager are under: /etc/yum.repos.d

Tip:
For using internal repositories, the security risks are not that high. For that reason, you do not have to know how to work with GPG-signed packages on the RHCSA exam.
But be aware that it is important to know that working with the external packages introduce the security risks as the package pull executed with root priveledges and the script which will be run in the package - run on the current machine. And if that package was compromised the risk will be high. 

### Exercise 9-1 Creating your own Repository 

Context:
Windows
  │
  │ RHEL ISO file
  ↓
VMware
  │
  │ virtual CD/DVD drive
  ↓
RHEL VM
  │
  ↓
/dev/sr0
  │
  │ mount
  ↓
/repo
  │
  ↓
RHEL installation files/packages
  │
  ↓
DNF repository

1) what is the /dev/sr0?
    In the Linux it represents the optical CD/DVD drive

Schematically it is:
Windows
└── RHEL-10.x.iso
       ↓
   VMware virtual CD/DVD drive
       ↓
      RHEL
       ↓
    /dev/sr0

Where the iso file remains on Windows. 

2) what does the mount mean?
Normally Linux is not like windows, you won't see the DVD it is drive F (whatever). Instead Linux attaches the filesystem to a directory of the existing filesystem. 
Example:
/
├── etc
├── home
├── var
├── repo

Then if we mount the DVD to /repo, /dev/sr0 -- mount --> /repo, the contents of the DVD becomes accessible via /repo. And ls /repo shows that content.

Thus:
/dev/sr0 --> physical/virtual storage device
mount --> make its filesystem accessible
/repo --> directory through which we access it

Prequisites for SOlution:

a. Check if the Linux sees the optical device with lsblk (lisk block devices)
    lsblk
        output example:
                                    NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
                        sr0            11:0    1  9.5G  0 rom  /run/media/oleksii_ops/RHEL-10-1-BaseOS-x86_64
                        nvme0n1       259:0    0   40G  0 disk 
                        ├─nvme0n1p1   259:1    0    1M  0 part 
                        ├─nvme0n1p2   259:2    0    1G  0 part /boot
                        └─nvme0n1p3   259:3    0   39G  0 part 
                        ├─rhel-root 253:0    0 35.1G  0 lvm  /
                        └─rhel-swap 253:1    0  3.9G  0 lvm  [SWAP]

where:
    rom - read only
    /run/media/oleksii_ops/RHEL-10-1-BaseOS-x86_64 --> current mounted location, the same will be visible with mount command

b. Check the device Node: 
    ls -l /dev0/sr0
        Example output:
        brw-rw----+ 1 root cdrom 11, 0 Aug 27 15:03 /dev/sr0
        where, b - block device; same as disks/partitions category




c. Check if mounted already:
    mount
    OR filter with mount | grep sr0    


Output example:
/dev/sr0 on /run/media/oleksii_ops/RHEL-10-1-BaseOS-x86_64 type iso9660 (ro,nosuid,nodev,relatime,nojoliet,check=s,map=n,blocksize=2048,uid=1000,gid=1000,dmode=500,fmode=400,iocharset=utf8,uhelper=udisks2)


Prerequisites summary:
Windows ISO
     ↓
 VMware virtual DVD
     ↓
   /dev/sr0
     ↓
   mounted at
     ↓
/run/media/oleksii_ops/RHEL-10-1-BaseOS-x86_64

Solution to exercise 9-1:
As per task, it is propossed to mount the /dev/sr0 to the /repo

a. Unmnount the /dev/sr0 from original location
    sudo umount /dev/sr0, notice it is uMount NOT uNmount

Now if check with lsblk:
sblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  

So, now the /dev/sr0 was unmounted

b. Create a requested mount directory in root
    sudo mkdir /repo

c. Mount /dev/sr0 manually to /repo
    sudo mount /dev/sr0 /repo

Output with lsblk:
sblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  /repo

d. Edit /etc/fstab
    1) What is the fstab?
        fstab (filesystem table/static information about the filesystems) is a configuration file that tells Linux about filesystems that should be mounted. And lies under /etc/fstab, it is a basic instructions list. 

    2) mount vs fstab:
        mount is command which executes right now, once it has been ran. Normally lost after reboot.
        fstab defines what should be mounted, where mounted, and how. Persistent

e. Edit /etc/fstab by adding mounting parameters for /repo target
    sudo echo "/dev/sr0 /repo iso9660 defaults 0 0" >> /etc/fstab
f. Ensure that the system pick the latest
    systemctl daemon-reload
g. Run command for mount with the flag that it will rely on fstab file now
    mount -a 
h. Check result:
    lsblk 
    OR 
    mount | grep sr0
i. Add new repos to the yum.repos.d
    dnf config-manager --add-repo=file:///repo/BaseOS
    If system was unregistered, register it as:
        sudo subscription-manager register
    Check with the subscription-manager status
    Once promted, type user name and password which is used on the Red Hat portal for dev subscription

    if good, add the next repo:
        dnf config-manager --add-repo=file:///repo/AppStream
    
    check if added:
    sudo ls /etc/yum.repos.d

    Also add this: gpgcheck=0 to the end of both files

j. Check the availability of the new repos:
    dnf repolist

### Exercise 9-2 Working with packages with dnf

a. Check with dnf if the package it is what you are really lookign for.
    dnf info nmap
If ok, then install.

a.1 Find the packages with given spec
    dnf provides <spec_name>
    dnf provides seinfo

b. Install packages
    sudo dnf info nmap

b1. To remove packages
    sudo dnf remove nmap
        note: can use directly with the -y option for both commands if sure (ideally to not use). 

c. List packages
    dnf list 
    Note: it will be big list, use with | less to not flood in the terminal.

    If looking for something specific, also possible:
        dnf list google* | less

d. TO check which packages are installed on your server
    dnf list kernel

e. Update packages
    sudo dnf update nmap

To install a group of packages, rather to install them one by one, when need all them at once, use dnf group/groups/groupinstall. For example for the .NET Development, etc.

f. List available groups
    dnf group list

g. Check the history of dnf, all registered by the way.
    dnf history

h. Be in the history the last step can be undone
    dnf history undo <number_of_the_history_element>
    dnf history 8

### Exercise 9-3 Working with packages with rpm

Useful
a. List installed packages on the machine
    rpm -qa

b. Check for files that specific package has
    rpm -ql nmap
    OR
    rpm -qd nmap ; more shorter and precised

c. Find the package name from where the particular command comes from
    rpm -qf /bin/ls
    output: coreutils-9.5-8.el10_2.x86_64 --> show from where the ls come from

d. Query a package for specific option
    rpm -qp --scripts httpd-2.4.63-1.el10.x86_64.rpm
    Note: -p is used to query the package file and not a database. Also .rpm extension at the file end.

Task:

a. Install any package
    dnf install -y dnsmasq

b. Get the complete path of the installed command
    which dnsmasq
    output: /usr/sbin/dnsmasq

c. Use a rpm query to get the package name which holds that command/tool
    rpm -qf $(which dnsmasq)
        where full command will be resolved as:
            rpm -qf /usr/sbin/dnsmasq --> where as we remmember the -qf is a query format
d. Show more info now about the package
    rpm -qi dnsmasq

e. List all files in the package
    rpm -ql dnsmasq

f. Show the files of the available documentation in the package
    rpm -qd dnsmasq

g. Before installation it is always good to know which scripts are going to be executed during installation
    rpm -q --scripts dnsmasq


### Flatpak

Was used mainly with the GUI. Flatpak can help with the concern when the same package with the different versions need to be installed on the same machine for different purposes. Normally it is not possible to do in the standard way. Flatpak uses a like container approach - all dependencies are in the image. Flatpak can be a user custom tool, which does not need the admin previledges to work with the packages as dnf or rpm do. 


a. Install Flatpak
    dnf install flatpak


### Exercise 9-4 Working with Flatpak Applications

a. Login as non-root
b. Add a reference to the remote repo:
    flatpak remote-add --user myrepo https://dl.flathub.org/repo/flathub.flatpakrepo
c. Verify all the repos
    flatpak remotes
d. Use a flatpak to search for the program
    flatpak search gimp
e. Install with Flatpak
    flatpak install -u gimp.
f. Check if the application was installed
    flatpak list
g. Check from other non-root users if repo and app do not exist for them
    flatpak list 
    flatpak remotes


# Chapter 10 Managing processes

Processes types:

- shell jobs: started from command line; associated with the shell where were started; also reffered as interactice processes.
- daemons: jobs that provide services; noramlly started at the boot of machine, and often(not all cases) run in root.
- kernel threads: part of Linux kernel; cannot manage them with the common tools, for the monitoring and performance it is useful to keep trak on them. 

Process: multiple threads (subdivision of the process). 

##

Normally when the command is entered in the terminal and the shell job is started. That job runs as a foreground process by occupying the terminal, the command can be run in the background process by making the terminal available. 

Job management overview:

- **&**   starts the command immediately in the background. Ex: ping -c 3 8.8.8.8 **&**
- **fg** brings the last job to the foreground which was moved to the background
- **jobs** shows the current jobs
- **Crtl-Z** stops the job temporary; so that job can be managed now, e.g., moved to the background.
- **bg** continues the job which has been frozen with **Ctrl-Z** in the background process.
- **Ctrl-C** cancells the current interactive job
- **Ctrl-D** sends the EOF character to the current job to indicate that it should stop waiting for further input.
- **top** shows the live top running jobs
- **ps** overview of the current running processes
- **ps aux** summary of the processes with additional information; normally exposes more processes: including kernel, daemons.
    Note: instead of ps aux | grep dd we can use also pgrep dd --> outputs only PIDs
- **ps fax** shows the parent-child relationships between processes

### Exercise 10-1 Managing jobs

1. Open a root shell and type a next commands:
    sleep 3600 & 
    dd if=/dev/zero of=/dev/null & 
    sleep 7200

    Now: because the sleep 7200 was run without **&** need to wait 2 hours before terminal will be available. 

1.1 Type **Ctrz-Z** to stop the command.

2. See the jobs which were started:
    **jobs**

3. Put the stopped job for the background process
    bg <job_n>
    bg 4

4. Check jobs again
    jobs

5. Move the first job to the fg
    fg 2

5.1 Cancel job (stop it)
    **Ctrl-Z**

5.2 Check jobs that it is gone
    jobs
6. Cancel all other jobs in the same way
7. Open a second terminal and type:
    dd if=/dev/zero of=/dev/null &
8. Close a second terminal
    exit
9. Back to the previous terminal and type
    top
9.1 See that the dd job is still running
9.2 Kill the process, type **k** in the top foreground process
9.3 PID promted to be typed, hence put that PID manually and kill the process

## Parent-Child relations
When a process is started from a shell it becomes a child process of that shell. 
- Process are killed if the terminal is closed from where they were started
- Process are NOT killed if they were ran in the background and terminal is closed

As Linux admins we cannot manage individual threads but we can manage processes.
2 Types of the background processes:
    - kernel threads; they are part of the Linux kernel processes. Each has PID (process identification number); we can easily indetify them, e.g:
            root           1  0.2  0.9  44580 36200 ?        Ss   09:59   0:30 /usr/lib/systemd/systemd --switched-root --system --deserialize=43 rhgb
            root           2  0.0  0.0      0     0 ?        S    09:59   0:00 [kthreadd]
            root           3  0.0  0.0      0     0 ?        S    09:59   0:00 [pool_workqueue_release]
            root           4  0.0  0.0      0     0 ?        I<   09:59   0:00 [kworker/R-rcu_gp]
        Kernel process have the name between the square brackets. 
        Note: as admins we cannot manage the kernel threads only by complete downtime of machine. 
    - daemon processes

## Processes priorities
cgroups are used in Linux to allocate resources. There are 3 areas, which are called **slices**.
    - system: where all Systemd-managed processes are running
    - user: where all user processes, including root processes are running
    - machine: optional slice for VMs and containers
All slices have the same CPU weight. Means the CPU capacity is equally devided for them. 

Normally all the processes are starting with the same priority. Changing a process priority can be done with the **nice** (start a new process with specified priority) and **renice** (modify the priority for the current active process). Alternatively use the **r** from the **top** command.  

Note: in RHEL 10 if you kill the parent process all the child processes become as childs of the Systemd process - before RHEL all childs were killed once the parent one is. 

## Signals
Signal is an anstruction can be sent to a process. Common signals: SIGTERM and SIGKILL; 
**kill** the way to send the signal to the process
**kill -l** check all available signals can be sent
Output example:
1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM

Hence, to send a signal use e.g. **kill -3**

### Exercise 10-1 Managing processes from the command line
1. In the root shell type the same command 3 times:
    dd if=/dev/zero of=/dev/null &

2. Verify those processes
    ps aux | grep dd
output:
PID   USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+  COMMAND
15928 oleksii+  20   0  226864   1940   1820 R  47.6   0.1   0:44.02  dd                                
15926 oleksii+  20   0  226864   1984   1860 R  44.1   0.1   0:49.70  dd                                                                                         
15930 oleksii+  20   0  226864   1884   1764 R  40.6   0.1   0:43.65  dd 

e.g. we can see the PR which is 20. 

3. Try to change one of the process priority with the renice
    renice -n 5 15926
Now we see:
15926 oleksii+  25   5  226864   1984   1860 R  19.3   0.1   2:41.68 dd

The niceness was updated to 5 and priority to 25. 

3.1 Try for opposite value
    renice -n -10 15926

output: 15926 oleksii+  10 -10  226864   1984   1860 R  78.3   0.1   3:17.87 dd
The niceness is -10 and PR is 10. 

4. Check the parent-child relations
    ps fax | grep -B5 dd
        where the -B5 shows the matching lines inluding the 5 lines before that. 
output:
  14558 ?        Ssl    1:17  \_ /usr/bin/ptyxis --gapplication-service
  14565 ?        Ssl    0:04      \_ /usr/libexec/ptyxis-agent --socket-fd=3 --rlimit-nofile=1024
  14597 pts/0    Ss     0:00          \_ /usr/bin/bash
  14708 pts/0    T      0:00              \_ sudo sleep 3600
  14719 pts/0    T      0:00              \_ sudo sleep 3600
  15926 pts/0    R<     4:26              \_ dd if=/dev/zero of=/dev/null
  15928 pts/0    R      4:26              \_ dd if=/dev/zero of=/dev/null
  15930 pts/0    R      4:23              \_ dd if=/dev/zero of=/dev/null

5. Kill the dd processes
    kill -9 15926

6. Kill all
    killall dd
    Note: the process name, do not kill all at all. 

## Killing Zombies
Zombies are processes with a special state. Zombie processes are processes that have completed execution but are still listed in the process table.

- **ps aux | grep defunct**

Killing them with the reboot (which normally helps) can be expensive in terms of server distrupting. 
**kill -SIGCHLD <parentpid>** to properly kill a zombie

How to find the Parent Id of the Child Id? 

can use:
ps -eo pid,ppid,state,cmd | grep defunct
    where -e is like ALL
          -o is Format output where we can pass some columns
output:
  11308    1149 Z [pkla-check-auth] <defunct>
  16331   14597 S grep --color=auto defunct

And now:
kill -SIGCHLD 1149
    where the 1149 is a Parent id from the column of ppid


## Linux process states
- Running (R)
- Sleeping (S)
- Uninterruptible sleep (D): in sleep but cannot be stopped
- Stopped (T)
- Zombie (Z)

**uptime** - information about average load
As a rule of thumb, the load average should not be higher than the number of CPU cores in your system.
**lscpu** - check the CPU cores

### Exercise 10-3 Managing load average

1. In root, run 3 times:
    dd if=/dev/zero of=/dev/null &

2. Observe current load average
    top

3. Check with uptime

Before: 18:19:54 up  5:11,  1 user,  load average: 0.34, 0.31, 0.65
After: 18:20:45 up  5:12,  1 user,  load average: 2.00, 0.75, 0.79

4. Check CPU and core numbers
    lscpu
output:
Byte Order:                              Little Endian
CPU(s):                                  2
On-line CPU(s) list:                     0,1
...
CPU family:                              6
Model:                                   158
Thread(s) per core:                      1
Core(s) per socket:                      1

5. Kill all dd
    killall dd


In Linux a user can tune a sytem to optimize performance. It can be done with the **tuned** command. In the profiles (file for performance settings) admins can easily tune the system.
**Profile Overview**
balanced                The best compromise between power usage and performance
desktop                 Based on the balanced profile, but tuned for better response to interactive applications
latency-performance     Tuned for maximum throughput
network-latency         Based on latency-performance, but with additional options to reduce network latency
network-throughput      Based on throughput-performance, optimizes older CPUs for streaming content
powersave               Tunes for maximum power saving. Use this if you’re using a RHEL virtual machine on your laptop and you want your battery to last longer
throughput-performance  Tunes for maximum throughput
virtual-guest           Optimizes Linux for running as a virtual machine
virtual-host            Optimizes Linux for use as a KVM host

### Exercise 10-3 Managing load average

1. Install tuned with dnf if not the case
    dnf install tuned

2. Check if running
    systemctl status tuned

2.1 If not
    systemctl enable --now tuned

3. To see which profile is used
    tuned-adm active

4. To see which profile is recommended
    tuned-adm recommend

5. Activate throughput-performance profile
    tuned-adm profile throughput-performance

# Chapter 11 Working with the Systemd

**Systemd** The service manager on RHEL 10, Systemd is the very first process that starts after the kernel has loaded, and it takes care of starting all other processes and services on a Linux system. Unit an item that is managed by Systemd. Different types of units exist, including service, path, mount, and target units. So, basically a **unit** is a configuration object that tells systemd what resource/job it should manage and how.

unit file
   ↓
describes what/how to manage
   ↓
systemd (manager)
   ↓
starts/stops/schedules/etc.

.service  → manage a process/service
.timer    → schedule/trigger something
.socket   → manage a socket
.mount    → manage a filesystem mount
.target   → group/coordinate other units

systemd  ← the manager
   ↑
systemctl ← CLI to talk to/manage it

- systemd = the system/service manager (the daemon is usually systemd, PID 1).
- systemctl = the command-line tool used to control and query systemd.

- display available list of available units:
    systemctl -t help

- /usr/lib/systemd/system: default unit files. Installed from RPM manager
- /etc/systemd/system: custom unit files generated by admin or added with **systemctl edit**
- /run/systemd/system: unit files that have been generated automatically
where the priority: from the last to first. Means /run takes precendence over /etc/ and /etc/ oever /usr

Users can create their units under ~/.config/systemd.
If check any of the unit files under /usr.. path we can find the clear pattern structure

[Unit]
Description=Podman API Service
Requires=podman.socket
After=podman.socket
Documentation=man:podman-system-service(1)
StartLimitIntervalSec=0

[Service]
Delegate=true
Type=exec
KillMode=process
Environment=LOGGING="--log-level=info"
ExecStart=/usr/bin/podman $LOGGING system service

[Install]
WantedBy=default.target

where 
    [Unit] defines dependencies and describes the unit
    [Service] describes how to start and stop the service and request status installation.
    [Install] indicates in which target this unit has to be started

### Systemd mount units

It is alreanative way of /etc/fstab; how to mount a file system on a specific directory.
To check the unit files, specifically for the mount now, we can use:
    cat /usr/lib/systemd/system/tmp.mount
    OR directly with the command systemctl
    systemctl cat tmp.mount
    THe output will be the same

### Systemd socket units
A socket creates a method for an application to communicate with another one. It can be defined as a file but also as a port on which the Systemd will be listening on incoming conenctions. Thus the service does not need to stay in idle mode (running all the time) it wait to run until a connection is established.

Check if unit service is running
systemctl status cockpit.service

### Systemd target units
The unit files are used to build the functionality that is needed on your server.

**want** An indication for a Systemd unit file that it is supposed to be started from a specific Systemd target.

### Exercise 11-1 Managing units with systemctl

1. From root install a Very Secure FTP service
    dnf install vsftpd

2. Active that service
    systemctl start vsftpd

3. Check the status
    systemctl status vsftpd
    See: loaded (/usr/lib/systemd/system/vsftpd.service; disabled;
        Means the service won't be started after system restart

4. Enable vsftpd service
    systemctl enable vstfpd

4.1 Check the status again

### Managing dependecies
Some unit types might have dependencies on the service units.
- Check dependencies:
    systemctl list-dependencies vsftpd

### Exercise 11-2 Changing unit configuration

1. From root install Apache web server package
    dnf install httpd

2. Show the current service unit
    systemctl cat httpd.service

3. Check for the available directives
    systemctl show httpd.service

4. Change the default configurations
    systemctl edit httpd.service
    add a [Service] section that includes the Restart=always and RestartSec=5s lines.

5. Reload the configs
    sudo systemctl daemon-reload

6. Start the service and verify if running
    systemctl start httpd
    systemctl status httpd

7. Kill all 
    killall httpd

8. Check after 5 sec again the status to see that it is again running
    systemctl status httpd


**Notes**:
edit the /root/.bash_profile
    export SYSTEMD_EDITOR="/usr/bin/vim"
and add this line to the ~/.bashrc file.
    After login the vim will be used as a default editor

# Chapter 12 Scheduling Tasks

RHEL 10 offeres different solutions for the scheduling tasks:
    - Systemd timers as a default solution
    - **cron** is the legacy scheduler service. But still supported and used.
    - **at** is used to schedule an occasional user job for the future execution. 


### Using Systemd Timers
A Systemd timer is always used together with a service file, and the names should match.

Example:
systemctl cat logrotate.timer
# /usr/lib/systemd/system/logrotate.timer
[Unit]
Description=Daily rotation of log files
Documentation=man:logrotate(8) man:logrotate.conf(5)

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true

[Install]
WantedBy=timers.target

If you need a service to be started by a timer, you enable the timer, not the service.

in case if you want to check with the man pages what are the attibutes or other options available for the systemd units, try below:
    Section → unit type → man page
    ex: man systemd.timer
        OR man systemd.unit
        OR man systemd.install

Tip: use **man -K RandomizedDelaySec** --> to search accross all man pages if do not know where to search
    Also use **apropos <key_word>** --> for searching key words accros man pages 

### Exercise 12-1 Using systemd timers

1. Show a list of all the timers
    systemctl list-units -t timer

2. Show all where logrorate keyword is matched
    systemctl list-unit-files logrotate.*

3. Check the contents of logrorate.service unit file. Notice: no install section
    systemctl cat logrotate.service

4. Check the status of service and timer
    systemctl status logrorate.service
    AND
    systemctl status logrorate.timer

5. Install sysstat pacage 
    dnf install sysstat

6. Check if unit files are added from the systat
    systemctl list-unit-files sysstat*

7. Check what the sysstat timer is doing
    systemctl cat sysstat-collect.timer
    Output:
        [Timer]
        OnCalendar=*:00/10
## Crond 

**Cron timing**:
minute 0–59 
hour 0– 23 
day of month 1–31 
month 1–12 (or month names) 
day of week 0–7 (Sunday is 0 or 7) or day names

┌──────── minute       0–59
│ ┌────── hour         0–23
│ │ ┌──── day of month 1–31
│ │ │ ┌── month        1–12
│ │ │ │ ┌ day of week  0–7
│ │ │ │ │
0 0 1 1 *
minute → hour → day → month → weekday

where **'*'** means every possible value of this particular field.

Thus:
*  *  *  *  *
│  │  │  │  │
│  │  │  │  └─ every weekday
│  │  │  └──── every month
│  │  └─────── every day
│  └────────── every hour
└───────────── every minute

Example:
* 11 * * *

where, 11 says  **"during hour 11."** and not at 11. 
11:00  ✓
11:01  ✓
11:02  ✓
...
11:59  ✓
12:00  ✗
Thus  every minute during the 11th hour, every day of every month, regardless of weekday.

One important cron gotcha
When both day-of-month and day-of-week are specified, traditional cron usually treats them as **OR**, not **AND**.
0 9 15 * 1

Example:
*/15 * * * * Every 15 minutes: 00, 15, 30, 45 of every hour, every day

* */2 * * * Every 2 hours on the hour field

### Manging cron config files
The main file is /etc/crontab. But to modify the cron jobs:
    - /etc/cron.d
    - /etc/cron.hourly, cron.daily, cron.weekly, cron.monthly
    - user specific files created with **crontab -e**


## Anacron

To ensure regular execution of the job, cron uses the anacron service. This service takes care of starting the hourly, daily, weekly, and monthly cron jobs, no matter at which exact time. Anacron uses /etc/anacrontab file.
File example:
#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
@monthly 45	cron.monthly		nice run-parts /etc/cron.monthly

Where
    1st column: frequenccy of the job execution in days
    2nd column: how long anacron waits before executing the job
    3rd column: job identifier

Limit who can access the cron jobs with:
    - /etc/cron.allow
    - /etc/cron.deny

### Exercise 12-2 Running scheduled tasks through cron

1. Check crontab
    cat /etc/crontab

2. Edit a cron tab
    crontab -e
    Add this: 0 2 * * 1-5 logger message from root
3. Save and close the vim
4. Create a script in the /etc/cron.hourly
    cd /etc/cron.hourly
    echo "logger This message is written at $(date)" > eachhour
5. Make the script executable
    chmod +x eachhour
6. Enter the directory /etc/cron.d and create a new script **eachhour** and put the following code
    11 * * * * root logger This message is written from /etc/cron.d

7. After couple hours type grep written /var/log/messages and read them

Here is the idea, that inside such files:
root@localhost:/etc# ls | grep cron
anacrontab
cron.d
cron.daily
cron.deny
cron.hourly
cron.monthly
crontab
cron.weekly

like cron.daily, or monthly, the schedule already determined by the file name. And the script will run as per the cron scheduler defined as file name. BUt if put the script inside the cron.d --> the script who determines when it will be run. 

## Configuring **at** schedule future tasks
    whatis at
at (1)               - queue, examine, or delete jobs for later execution

### Exercise 12-3 Scheduling Jobs with at

1. Check if atd is enabled and running
    systemctl status atd

2. Schedule one job with **at** for some time
    at 18:30
2.1 Press Enter and exit with Crtl+D

3. Check the queue with the **atq**
    atq

4. Check the logs if messages appeared there
    less /var/log/messages

# Chapter 13 Configuring logging

Normally 3 ways of log writes are used in Linux:
    - **systemd-journald**: a service tightly is intergrated with Systemd. Allows administrators to read detailed information from journal. Command: **systemctl status** or **journalctl**
    -  **dirict write** some services write log directly during runtime. But that approach is not recommended, to have one centralized log service where all logs can be found - much appropriate.
    - **rsyslogd** is the enhancement of syslogd, a service that takes care of managing centralized log files. 
Also
    - **auditd** to keep track of the kernel events

For Linux admins, to understand what is happening on the server:
    - journalctl
    - systemctl status <unit>
    - monitor files in /var/log that are written by rsyslogd; Depends on the service and server configs.

Example: systemctl status sshd -l, shows relavant log information


/var/log/messages           This is the most commonly used log file; it is the generic log file where most messages are written to. 
/var/log/dmesg              Contains kernel log messages. 
/var/log/secure             Contains authentication-related messages. Look here to see which authentication errors have occurred on a server.  
/var/log/boot.log           Contains messages that are related to system startup. 
/var/log/audit/audit.log    Contains audit messages. SELinux writes to this file. 
/var/log/maillog            Contains mail-related messages. 
/var/log/httpd/             Contains log files that are written by the Apache web server (if it is installed). Notice that Apache writes messages to these files directly and not through rsyslog.

### Live log file monitoring

See the reak time logging:
    tail -f <logfile>

### Exercise 13-1 Discovering journalctl

1. Type jounalctl to see the journal since last server started
    journalctl
    Use it with **less** and jump to bottom **G**
    journalctl | less 

2. Check content without pager and Press Ctrl-C to interrupt 
    journalctl --no-pager

3. Check the live logs with the scrolling option
    journalctl -f

4. Check the specific option for filtering when use journalctl
    a. journalctl
    b. Press space
    c. Tab 2 times
    d. Type y (yes) and Enter
4.1 Now check from the proposed e.g., logs for the user account
    journalctl _UID=1000

5. Check the last lines of the journal
    journalctl -n 20

6. Check errors only
    journalctl -p err

7. To see the logs from or to specific time period use **--since** or **--until**.  Formats: YYYY-MM-DD hh:mm:ss or just yesterday, today, tomorrow. 
    journalctl --since yesterday

8. Try to combine different options
    journalctl --since yesterday -p err 

9. To see much more details 
    journalctl -o verbose
    THis command shows the different options (e.g. UID, GID, priority, etc) that are used to write logs to journal. 
    For instance try
    journalctl -u sshd.service
    And compare with
    journalctl -u sshd.service -o verbose

10. Kernel related logs 
    journalctl --dmesg

Note: Most useful journalctl options 
    journactl -b    - boot logs
    journalctl -x   - explanation to information it shows
    -f              - shows the bottom of the journal
    -u              - filters for a specific unit only

### Exercise 13-2 Making the systemd journal persistent

1. Create a directory (if not there):
    mkdir /var/log/journal

2. Use a special systemctl command which allows to keep the persistent state of the logging even after system reboot
    systemctl restart systemd-journal flush


### Configuring rsyslogd

To configure, edit 
    /etc/rsyslog.conf - central location
    /etc/rsyslog.d - directory which also included during rsys log run

To see the all rslog facilities, destinations, priorities: man rsyslog.conf

### Exercise 13-3 Changing rsyslog.conf rules

By default the Appache service writes the logs to the own location. With the rsyslog.conf it can be changed. 

1. Add the following line: ErrorLog syslog:local1    
To the /etc/httpd/conf/httpd.conf
**Note:** be sure there are no other ErrorLog managed in the same config file, otherwise comment them

2. Restart the httpd service
    systemctl restart httpd

3. Add line to config file which will send all data to particular facility
    a. file: /etc/rsyslog.conf
    b. facility local1
    c. logs will go: /var/log/httpd-error.log
    d. the line to be included under #### RULES ####
    e. line to add: local1.error /var/log/httpd-error.log

4. Reload rsyslogd
    systemctl restart rsyslog

4.1 Send the logs with the Apache
    a. logger -p local1.error "TEST local1 message"
    b. verify the /var/log/httpd-error.log

5. Create a drop-in file for the debug messages to a specific file.
    echo "*.debug/var/log/messages-debug" > /etc/rsyslog.d/debug.conf

6. Restart rsyslogd
    systemctl restart rsyslog

7. Check the latest debug messages
    tail -f /var/log/messages-debug

8. From another terminal
    logger -p daemon.debug "Daemon Debug Message"

9. From the first terminal where the live latest debug ongoing check if message appeared

## Rotating log files
To not overflow the system with logs, based on the threshold the old logs can be closed and new can be opened. As a default **4** old log files are kept on the server, older than those will be automatically removed.
File name example, of the rotated(old) log file: /var/log/messages-20260608. Note, there is no default job which backups old logs if so needed the centilized log backup server to be configured.

Default settings for rotating are kept under: /etc/logrotate.conf

## Using logger

The **logger** command allows to the users enter the logs to the terminal which will be passed to the rsyslog.

### Exercise 13-4 Using Live Log Monitoring and logger

1. Open a root shell
2. Check the live tailed logs
    tail -f /var/log/messages
3. In another terminal log to the student account
    su - student
4. Type **su -** to open a root shell but put the wrong password
5. See the log files now in the /var/log/messages from another window
6. Type from student shell
    logger hello
6.1 Confirm logs from root shell
7. From root, check the latest security logs
    tail -20 /var/log/messages

# Chapter 14 - Managing storage

**Partition** logic split of the disk.

Physical disk
┌──────────────────────────────────────────────┐
│                                              │
│          one big physical storage            │
│                                              │
└──────────────────────────────────────────────┘

Disk
┌──────────────┬──────────────────┬───────────┐
│ Partition 1  │   Partition 2    │ Partition3│
│              │                  │           │
└──────────────┴──────────────────┴───────────┘

WHy need partitions?
    For example:
    Disk
┌──────────────┬───────────────┬──────────────┐
│ /boot        │ /             │ /home        │
│ 1 GB         │ 50 GB         │ 100 GB       │
└──────────────┴───────────────┴──────────────┘

The above approache of the partiitons gives ability:
    - to not mix the data of /home with /boot
    - able to restore OS by not touching data in /home
    - etc

Thus:
Physical disk
      ↓
Partition
      ↓
Filesystem
      ↓
Directories/files

MBR (Master Boot Record) - partition scheme. On a BIOS system, the first 512 bytes on the primary hard disk. It contains a boot loader and a partition table that give access to the different partitions on the hard disk of that computer.

With the new computers the new scheme comes up as the MBR cannot handle that. It is GPT (GUID Partition Table). GPT is a modern solution to store partitions on a hard disk, as opposed to the older MBR partition table. In GUID partitions, a total of 128 partitions can be created, and no difference exists between primary, extended, and logical partitions anymore.

UEFI (Unified Extensible Firmware Interface) - the replacment of the old BIOS system.

MBR
├── old
├── limited
├── widely supported by old firmware/OS
└── ~2 TB disk limit

GPT
├── modern
├── supports huge disks
├── more partitions
├── more robust
└── normally used with UEFI

GPT → modern RHEL → normal/default → know well

MBR → legacy → less common → understand it + recognize it

## Understanding storage measurement units

MB (megabyte): is muptiple(кратне) of 1000
MiB (mebibyte) - is muptiple of 1024

## Managing partitions and File systems

Top commands:

- **fdisk** (manipulate disk partition table)
- **parted** (a partition manipulation program)
- **gdisk**

/dev is the Linux device filesystem

Common Disk Device Types

/dev/sda                A hard disk that uses the SCSI driver. It is used for SCSI and SATA disk devices and is common on physical servers but also in VMware virtual machines. 
/dev/nvme0n1            The first hard disk on an NVM Express (NVMe) interface. NVMe is a server-grade method to address advanced SSD devices. At the end of the device name, note that the first disk in this case is referred to as n1 instead of a (as is common with the other types). NVMe is the storage interface/protocol; it isn't a partitioning scheme.
/dev/hda                The (legacy) IDE disk device type. You will seldom see this device type on modern computers.
/dev/vda                disk in a KVM virtual machine that uses the virtio disk driver. This is the common disk device type for KVM virtual machines.
/dev/xvda               disk in a Xen virtual machine that uses the Xen virtual disk driver. You see this when installing RHEL as a virtual machine in Xen virtualization. RHEL 10 cannot be used as a Xen hypervisor, but you might see RHEL virtual machines on top of the Xen hypervisor using these disk types.

### Exercise 14-1 Creating MBR Partitions with fdisk

1. Check the list of lock devices. 
    lsblk
Example output:
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  /repo
nvme0n1       259:0    0   40G  0 disk 
├─nvme0n1p1   259:1    0    1M  0 part 
├─nvme0n1p2   259:2    0    1G  0 part /boot
└─nvme0n1p3   259:3    0   39G  0 part 
  ├─rhel-root 253:0    0 35.1G  0 lvm  /
  └─rhel-swap 253:1    0  3.9G  0 lvm  [SWAP]

There is only one disk is used. Thus it is better to add one more disk for such exercise. If VMWare, add one with UI. 

VMWare proposes several types of disks to be added, and the nvme is a recommended one. But if chose the different storage interface/protocol nothing specifically won't happen. Hence, keep the recommended from VMWare as nvme adn create a new disk with 3 GB.

2. Now check with lsblk
    Output:
        NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  /repo
nvme0n1       259:0    0   40G  0 disk 
├─nvme0n1p1   259:1    0    1M  0 part 
├─nvme0n1p2   259:2    0    1G  0 part /boot
└─nvme0n1p3   259:3    0   39G  0 part 
  ├─rhel-root 253:0    0 35.1G  0 lvm  /
  └─rhel-swap 253:1    0  3.9G  0 lvm  [SWAP]
nvme0n2       259:4    0    3G  0 disk

3. Use a **fdisk** command to create partitions
    sudo fdisk /dev/nvme0n2

4. Check how much space is available, inside **fdisk**
    p
output:
    Command (m for help): p
    Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
    Disk model: VMware Virtual NVMe Disk
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: dos
    Disk identifier: 0x107a6c8d

4.1 After several promts for partition number, first sector, last sector the partition was created

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-6291455, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-6291455, default 6291455): 

Created a new partition 1 of type 'Linux' and of size 3 GiB.

4.2 If all good with the proposed changes, type **w** to write them to the system. 

5. Check the block list again
    lsblk
output:
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  /repo
nvme0n1       259:0    0   40G  0 disk 
├─nvme0n1p1   259:1    0    1M  0 part 
├─nvme0n1p2   259:2    0    1G  0 part /boot
└─nvme0n1p3   259:3    0   39G  0 part 
  ├─rhel-root 253:0    0 35.1G  0 lvm  /
  └─rhel-swap 253:1    0  3.9G  0 lvm  [SWAP]
nvme0n2       259:4    0    3G  0 disk 
└─nvme0n2p1   259:6    0    3G  0 part 

### Exercise 14-2 Creating Logical Partitions

1. In root type, to open a fdisk interface
    sudo fdisk /dev/nvme0n2

2. Type **n** for the new partition
    If see that all in use:
            Command (m for help): n
             All space for primary partitions is in use.
That means that there is no more space in the /dev/nvme0n2 to have another partition. 
Note: Disklabel type: dos, means MBR partition scheme
Thus, we will try to undo the previous steps on the exercises 14-1 by removing the previously created partition and create new one with the less memory usage.

2.1 Check the current partition
    p
Output:
Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x107a6c8d

Device         Boot Start     End Sectors Size Id Type
/dev/nvme0n2p1       2048 6291455 6289408   3G 83 Linux

3. Delete partition
    d 

4. Check again
    p
Output:
Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x107a6c8d

Now there is no partition

5. Create a new partition
    n
5.1 For the last sector type:
    +1G
Press Enter, the msg will appear:
    Created a new partition 1 of type 'Linux' and of size 1 GiB.

6. Check with **p**
Output:
Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x107a6c8d

Device         Boot Start     End Sectors Size Id Type
/dev/nvme0n2p1       2048 2099199 2097152   1G 83 Linux

6.1 Write the changes
    w

7. Create a new extended partition.
    Back to the fdisk and type **n**

7.1 Type **e** (extended)
Again follow the promts and for the last sector type: **+1G**

8. Create a logical partition
    Back to fdisk

8.1 Select **l**
    Answer to promts, for the last use +1G or default. 

9. Verify **p**, write **w**, check with **lsblk**
Outputs:
Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x107a6c8d

Device         Boot   Start     End Sectors  Size Id Type
/dev/nvme0n2p1         2048 2099199 2097152    1G 83 Linux
/dev/nvme0n2p2      2099200 4196351 2097152    1G  5 Extended
/dev/nvme0n2p5      2101248 4196351 2095104 1023M 83 Linux

lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sr0            11:0    1  9.5G  0 rom  /repo
nvme0n1       259:0    0   40G  0 disk 
├─nvme0n1p1   259:1    0    1M  0 part 
├─nvme0n1p2   259:2    0    1G  0 part /boot
└─nvme0n1p3   259:3    0   39G  0 part 
  ├─rhel-root 253:0    0 35.1G  0 lvm  /
  └─rhel-swap 253:1    0  3.9G  0 lvm  [SWAP]
nvme0n2       259:4    0    3G  0 disk 
├─nvme0n2p1   259:8    0    1G  0 part 
├─nvme0n2p2   259:9    0    1K  0 part 
└─nvme0n2p5   259:10   0 1023M  0 part 

### Exercise 14-3 Creating GPT Partitions

Prerequisites: there previous disk can be used. For that remove all the partitions with the **d** option by selecting the right one. Then write the changes **w**.

1. In the fdisk for the disk nvme0n2, type **g** which will change the type of the partition table to 
**Disklabel type: gpt**

Output p:
p

Disk /dev/nvme0n2: 3 GiB, 3221225472 bytes, 6291456 sectors
Disk model: VMware Virtual NVMe Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: F4047196-D228-4814-95AD-1FA1215D44CD

1.1 Write changes

2. Come back to fdisk and check with **p**

3. Type **n**, now see the difference it is proposing from 1-128 against 1-4 as for the MBR. 
    Select by default 1.
3.1 First sector, also default
3.2 Last sector, if default it takes the whole disk, thus can specify +1G
3.3 Check with **p**
3.4 Write changes
4. Check with lsblk
5. If error that the table is in use, use **partprobe** to update the kernel partition table.

### Exercise 14-4 Creating Partitions with parted
Note: **parted** is lack latest builds on the latest RHELs

1. From root, type
    parted /dev/nvme0n2
It will open a GNU parted
2. Use **help** to available commands, type **print** to see unrecognized disk labels.
Output:
(parted) print                                                            
Model: VMware Virtual NVMe Disk (nvme)
Disk /dev/nvme0n2: 3221MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 


3. Type **mklabel** and press Enter. 
After promt **tab** 2 times to see the available options
Output:
    aix    amiga  atari  bsd    dvh    gpt    loop   mac    msdos  pc98   sun

Use **gpt**
See the promted Warning: The existing disk label on /dev/nvme0n2 will be destroyed and all data on this disk will be lost. Do you want to continue?

4. After typed **Yes**, use another command for partition creation **mkpart**
    Partition name: part1
    File system: default ext2 or xfs Note: use twice **Tab** to see the options
    Start(sector) : 1MiB
    End: 1GiB

5. Print with **print** what we have now. 
    Output:
        Model: VMware Virtual NVMe Disk (nvme)
        Disk /dev/nvme0n2: 3221MB
        Sector size (logical/physical): 512B/512B
        Partition Table: gpt
        Disk Flags: 
                Number  Start   End     Size    File system  Name   Flags
                1      1049kB  1074MB  1073MB  xfs          part1

6. Type **quit**, this will commit all the changes

7. Check with lsblk
    Output:
    nvme0n2       259:4    0    3G  0 disk 
    └─nvme0n2p1   259:6    0 1023M  0 part 

## Creating File systems
Once the partition is created still it cannot be used, the file system has to be created there. 

File System             Overview
XFS                     The default file system in RHEL 10. 
Ext4                    The default file system in previous versions of RHEL; it is still available and supported in RHEL 10. 
Ext3                    The previous version of Ext4. On RHEL 10, there is no need to use Ext3 anymore. 
Ext2                    A basic file system that was developed in the early 1990s. There is no need to use this file system on RHEL 10 anymore. 
BtrFS                   A relatively new file system that is not supported in RHEL 10. 
NTFS                    A Windows-compatible file system that is not supported on RHEL 10. 
VFAT                    A file system that offers compatibility with Windows and macOS and is the functional equivalent of the FAT32 file system. It is useful on USB thumb drives that exchange data with other computers but not on a server’s hard disks.

What is the File System?
A filesystem is the system that organizes raw storage into files and directories.
A partition it is just a range of blocks:
        /dev/nvme0n2p1
        ┌──────────────────────────────────────┐
        │ blocks │ blocks │ blocks │ blocks... │
        └──────────────────────────────────────┘
The file system tells **how the storage is organized and managed** it is not about file types it can have (txt, gpg, sql, etc).

Example, what is the file system:

1 GB partition:
/dev/nvme0n2p1
┌─────────────────────────────────────┐
│ 1 GB of raw storage blocks          │
│                                     │
│ 000 001 002 003 004 005 ... 262143  │
└─────────────────────────────────────┘
These are just numbered chunks of storage. There is no concept of **photos/birthday.jpg**
What the **XFS** (or any other) gives us? When mkfs.xfs /dev/nvme0n2p1, now the XFS puts its own management structures onto that storage. 
Very simplified example:
/dev/nvme0n2p1
┌───────────────────────────────────────────┐
│ XFS metadata │ file data │ free space ... │
└───────────────────────────────────────────┘
Then we mount: mount /dev/nvme0n2p1 /data ; And now we can do **echo "hello" > /data/test.txt**

So, file system:
    - Where it should go? test.txt --> blocks 1834, 1835, 1836...
    - Metadata (remembering that data):
        test.txt
        ├── owner = root
        ├── permissions = 644
        ├── size = 6 bytes
        ├── modification time = ...
        └── data = blocks 1834-1836

**Another File system Example:**

Well known that on the Windows we have **NTFS (New Technology File System)** file system. Means organized with NTFS rules. So, Windows can understand:
C:\
├── Windows\
├── Users\
├── Program Files\
└── myfile.txt

USB as **FAT32** 
USB
└── FAT32 filesystem
    ├── photo.jpg
    ├── document.txt
    └── video.mp4
Why FAT for USBs?
NTFS
    ↓
excellent for Windows
but less universally supported by devices
FAT32
    ↓
very widely supported
Windows, Linux, macOS, cameras, TVs, old devices, etc.

So, we can say that the filesystem determines how those files are represented and managed on the storage.
Filesystem = rules + data structures used to manage files on the storage

To format a partition the **mkfs** command can be used, with the option **-t** by specifying which file system to be there. If no specific choosen the **ext2** will be used. 

### Exercise 14-5 Creating a File System

Prerequisites:
    To check what is the current file system on the partition use
    **lsblk -f**
Output:
NAME          FSTYPE      FSVER            LABEL                   UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
sr0           iso9660     Joliet Extension RHEL-10-1-BaseOS-x86_64 2025-10-21-06-14-30-00                       0   100% /repo
nvme0n1                                                                                                           
├─nvme0n1p1                                                                                                       
├─nvme0n1p2   xfs                                                  b38e3495-a851-47f1-946c-0e4b32c0a8b4    368.9M    62% /boot
└─nvme0n1p3   LVM2_member LVM2 001                                 X9piS8-5XDU-KxYt-9xap-cFV1-3nq6-HDPwIP                
  ├─rhel-root xfs                                                  95547bc3-3ba2-44dc-9415-8d0d97fa6530       28G    20% /
  └─rhel-swap swap        1                                        f753112b-7583-4e7a-ab9e-14b89cb0389a                  [SWAP]
nvme0n2                                                                                                           
└─nvme0n2p1  

1. From the previously created partition **/dev/nvme0n2p1** create a XFS file system
    mkfs.xfs /dev/nvme0n2p1

## Changing file system properties
For example to manage the Ext4 file system properties. Use the tool: **tune2fs**
For the XFS file system there are different tools. 

## Adding swap partitions
Swap - disk/SSD space used as an extension/backing area for memory. 

In **Linux**
RAM is the fast working area:
          RAM
     ┌──────────────┐
     │ Running apps │
     │ Kernel       │
     │ Cached data  │
     └──────────────┘

When RAM becomes pressured, Linux can move some less actively used memory pages from RAM to swap: 
RAM                         Swap
┌──────────────┐           ┌──────────────┐
│ active data  │           │ less active  │
│ active apps  │  <---->   │ memory pages │
│ kernel       │           │              │
└──────────────┘           └──────────────┘
        ↑                         ↑
     very fast                disk/SSD  

What is the **swap partition**?
It is simply a partition which is used for a swap. A kernel manages to put them there. 


### Exercise 14-6 Creating a Swap Partition

1. Open a disk tool for partition
    fdisk /dev/nvme0n2

2. Create a 1 GB partition as did before in previous exercises.
3. Change partition type 
    **t**
    Select partition if not one there
    type **L** to see all
    type **swap**
output:
evice           Start     End Sectors  Size Type
/dev/nvme0n2p1    2048 2097151 2095104 1023M Linux filesystem
/dev/nvme0n2p2 2097152 4194303 2097152    1G Linux swap

4. Write changes **w**

5. Format partition as swap space. 
    mkswap /dev/nvme0n2p2

6. Check the amount of the swap currently used
    free -m
Output:
               total        used        free      shared  buff/cache   available
Mem:            3621        1445        1496           7         912        2175
Swap:           4007           0        4007

7. Switch to newly allocated space
    swapon /dev/nvme0n2p2

8. Check the free space with **free -m**
    Output:
                   total        used        free      shared  buff/cache   available
    Mem:            3621        1438        1502           7         912        2182
    Swap:           5031           0        5031

9. To make sure that the swap is also alvailable after reebot, add the following line to the **/etc/fstab**
    **/dev/nvme0n2p2 none swap defaults 0 0**

Note: if there are no reources or time to have the swap partition, the swap file can be used for that. In fact, there is no much difference in the performance perpective, etc by using a file.
    Exmaple:
        a. dd if=/dev/zero of=/swapfile bs=1M count=100
        b. mkswap /swapfile
        c. swapon /swapfile

## Mounting File systems
Just to create a partition and putting a file system on it are not enough to start using it. To use it, we also need to mount it. By mounting it, we make it accessible thorugh the specific directory.
    For mountin steps:
        - What to mount? - name of the device to be mounted
        - Where to mount? - specifies the directory on which the device should be mounted
        - What file system to be mount? - optionally can be specified, but normally not required, detected by mount command
        - What are the mount options? - optional, but still can use many options if need. 

Mount a file system : **mount**
Umnount a file system : **umount** Note: NOT **uN** but **u**

Example mount the /dev/nvme0n2p1 to some folder.
    a. show the current blocks: lsblk
        nvme0n2       259:4    0    3G  0 disk 
        ├─nvme0n2p1   259:5    0 1023M  0 part 
        └─nvme0n2p2   259:8    0    1G  0 part [SWAP]
    b. create a tmp directory in the root
        sudo mkdir /mount-test-disk
    c. mount a partition with the file system
        sudo mount /dev/nvme0n2p1 /mount-test-disk
    d. check with the block devices
        nvme0n2       259:4    0    3G  0 disk 
        ├─nvme0n2p1   259:5    0 1023M  0 part /mount-test-disk
        └─nvme0n2p2   259:8    0    1G  0 part [SWAP]
    Can see the mount place now as /mount-test-disk

To get the system overview for the blocks UUID: **blkid**. Note: use with sudo to see all the block devices.  Thus the mounting can be done based on the UUID rather than the device name (e.g ├─nvme0n2p3 use UUID). Example:
blkid
/dev/mapper/rhel-root: UUID="95547bc3-3ba2-44dc-9415-8d0d97fa6530" BLOCK_SIZE="512" TYPE="xfs"
/dev/nvme0n1p3: UUID="X9piS8-5XDU-KxYt-9xap-cFV1-3nq6-HDPwIP" TYPE="LVM2_member" PARTUUID="125e7e06-e9ed-4743-b5a5-ec64b181353d"
/dev/sr0: BLOCK_SIZE="2048" UUID="2025-10-21-06-14-30-00" LABEL="RHEL-10-1-BaseOS-x86_64" TYPE="iso9660" PTTYPE="PMBR"

Normally manuall file systems mounting it is not the efficient one, better to do it with the /etc/fstab
Example of it:
UUID=95547bc3-3ba2-44dc-9415-8d0d97fa6530 /                       xfs     defaults        0 0
UUID=b38e3495-a851-47f1-946c-0e4b32c0a8b4 /boot                   xfs     defaults        0 0
UUID=f753112b-7583-4e7a-ab9e-14b89cb0389a none                    swap    defaults        0 0
/dev/sr0 /repo iso9660 defaults 0 0

where we already can see the ROM device is mounted in the auto mode for read/write ISO file. Or the /boot one.

### Exercise 14-7 Mounting Partitions Through /etc/fstab

1. Copy one UUID which has to be mounted from **blkid**
    Example: /dev/nvme0n2p1: UUID="39c449e5-6ed8-4c67-a847-39ca088ebc79" BLOCK_SIZE="512" TYPE="xfs" PARTUUID="5d1142e1-bc0e-4016-b5c1-2507555e28f5"

2. Create a tmp folder now.
    sudo mkdir /swap_mount-test

3. Add the following line in the /etc/fstab
    UUID="39c449e5-6ed8-4c67-a847-39ca088ebc79 /swap_mount-test xfs defaults 0 0

4. By not testing it directly with rebooting, it is good to check directly with the **mount -a**
    if any errors, it gives:
        mount: /etc/fstab: parse error at line 16 -- ignored
After all compiler errors fixed:
    mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.

5. Verify with the lsblk
    nvme0n2       259:4    0    3G  0 disk 
    └─nvme0n2p1   259:5    0    1G  0 part /mount_from_fstab

If need changes, do a refresh with **sudo systemctl daemon-reload**.

### Exercise 14-8 Creating a Systemd Mount File

1. Format /dev/nvme0n2p1 file into ext4
    mkfs.ext4 /dev/nvme0n2p1

2. Create a folder for future mount point at the root
    mkdir exercise

3. Modify the systemd mount file
    vim /etc/systemd/system/exercise.mount

    Add following:
    [Unit]
    Before=local-fs.target

    [Mount]
    What=/dev/nvme0n2p1
    Where=/exercise
    Type=ext4

    [Install]
    WantedBy=multi-user.target

4. Enable and start the mount unit
    systemctl enable --now exercise.mount

5. Check if mount was created
    mount | grep exercise

    Output:
    mount | grep exercise
    /dev/nvme0n2p1 on /exercise type ext4 (rw,relatime,seclabel)

    ls output of /:
    /$ ls exercise
    lost+found

6. Check the unit file
    systemctl status exercise.mount

# Chapter 15 - Managing logical volumes

**LVM (Logical Volume Manager)** - a solution for the limitation of the traditional partitions. 

Partitions:
Disk
├── partition 1 → /boot
├── partition 2 → /
├── partition 3 → /home
└── partition 4 → ...

In this case they are **rigid** no flexible. 

For example:
/dev/sda
┌──────────┬──────────────────────┬──────────┐
│ /boot    │          /           │  /home   │
│ 1 GB     │       20 GB          │  20 GB   │
└──────────┴──────────────────────┴──────────┘
The root / becomes full, now we want to take 10 free GB from the /home, that is where the LVM comes.
And now instead of: Disk -> Partition -> File System
We get: Disk -> Partition -> LVM -> Logical Vilume -> File System -> Mount Point

**Logical Volume (LV)** behaves like a normal disk/partition from the filesystem's perspective. 
Physical storage
┌─────────────────────────────────────┐
│                                     │
│          LVM storage pool           │
│                                     │
└─────────────────────────────────────┘
          ↓          ↓          ↓
       LV root    LV home     LV data
         ↓           ↓           ↓
       XFS         XFS         XFS
         ↓           ↓           ↓
         /          /home       /data

LVM lets you treat physical storage as a pool of storage, then allocate pieces of that pool as logical volumes.
And later you can often resize those logical volumes much more flexibly than traditional partitions.
Note: not all file systems allow growing and shrinking. E.g., ext4 supports both, where XFS only growing.
A snapshot is a good advantage why to use LVM. Also moving the data from the failed hard drive is easier with the logical volumes.

## Creating LVM logical volumes
1. Convert physical devices (such as disks or partitions) into physical volumes (PVs)
2. Create a volume group (VG) and assign PV to it. 
3. Create the logical volume (LV) itself.

For exam: pv, vg, lv

### Exercise 15-1 Creating a Physical Volume
1. With **fdisk** create a new 1 GB partition 
2. Select a type of the partition
    **t** 
Choose **lvm**
3. See output of print now
    /dev/nvme0n2p1  2048 2099199 2097152   1G Linux LVM
4. Create 3 more LVMs for other usage. And write changes
Output:
Device           Start     End Sectors  Size Type
/dev/nvme0n2p1    2048 2099199 2097152    1G Linux LVM
/dev/nvme0n2p2 2099200 3123199 1024000  500M Linux LVM
/dev/nvme0n2p3 3123200 4147199 1024000  500M Linux LVM
/dev/nvme0n2p4 4147200 5171199 1024000  500M Linux LVM
5. Use lsblk 
6. Mark a partition as an LVM physical volume. --> THis is an actual creation of the PV
    output:   Physical volume "/dev/nvme0n2p1" successfully created.
7. Check if PV was created
    sudo pvs
Output:
PV             VG   Fmt  Attr PSize   PFree
  /dev/nvme0n1p3 rhel lvm2 a--  <39.00g    0 
  /dev/nvme0n2p1      lvm2 ---    1.00g 1.00g
OR with pvdisplay
output:
  --- Physical volume ---
  PV Name               /dev/nvme0n1p3
  VG Name               rhel
  PV Size               <39.00 GiB / not usable 0   
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              9983
  Free PE               0
  Allocated PE          9983
  PV UUID               X9piS8-5XDU-KxYt-9xap-cFV1-3nq6-HDPwIP
   
  "/dev/nvme0n2p1" is a new physical volume of "1.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/nvme0n2p1
  VG Name               
  PV Size               1.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               PBts37-SeDU-UDyL-gq6d-ueNC-D4Fx-xJTKrE

## Creating the volume groups (VG)

As we created a PV we can now assing it to a volume group (VG).
Command to greate a VG is **vgcreate**. Thus **vgcreate <name_of_volume> <source_PV>
An output:
    Volume group "my_new_volume_groups" successfully created

Note: if directly use the vgcreate on the disk which is not created a PV, the vgcreate will automatically mark it as PV. 

Now, use **sudo pvs** to see the PVs and their VGs
Output:
PV               VG                   Fmt  Attr PSize    PFree   
  /dev/nvme0n1p3 rhel                 lvm2 a--   <39.00g       0 
  /dev/nvme0n2p1 my_new_volume_groups lvm2 a--  1020.00m 1020.00m
OR **sudo vgs** OR **vgdisplay**

## Creating the logical volume (LG) and file systems
Now, the last step from the process of creation LVM logical volumes. As we have a group now, we can create a LV from it.

To create a LG there more options can be selected during creation, some of them:
    - **-L <size>**, -L 5G - to create a LVM colume with a 5GiB size
    - **-l <percentage>FREE** -l 50%FREE - to create by using half free available disk space
    - **-n** -n my_logical_volume - to specify a particular name for a LV

### Exercise 15-2 Creating the Volume Group and Logical Volumes

1. Open root. Check the previously created PVs, if not create.
    pvs
2. Create a VG (if not the case)
    vgcreate <VG_name> <Source_PV>
    Example: sudo vgcreate my_new_volume_groups /dev/nvme0n2p1

3. Check if created with
    pvs OR vgs

4. Create LVM LV with the desire name, which will use 50 % of the free disk space
    lvcreate -n <lv_name> -l 50%FREE <vg_name>
    example: lvcreate -n <lv_name> -l 50%FREE my_new_volume_groups

5. Check LVs 
    lvs
output:
  LV        VG                   Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_new_lv my_new_volume_groups -wi-a----- 508.00m                                                    
  root      rhel                 -wi-ao----  35.08g                                                    
  swap      rhel                 -wi-ao----   3.91g 

6. Now the File system can be created on top of the logical volume
    mkfs.ext4 /dev/<volume_group_name>/<logical_volume_name>
    sudo mkfs.ext4 /dev/my_new_volume_groups/my_new_lv

7. Now edit /etc/fstab to inlcude automount on startup
    /dev/my_new_volume_groups/my_new_lv /files ext4 defaults 0 0

8. Create at the root, /files folder and check mount with -a
    sudo mount -a
9. Check with lsblk that partition was mounted
    lsblk
output:
nvme0n2                            259:4    0    3G  0 disk 
├─nvme0n2p1                        259:5    0    1G  0 part 
│ └─my_new_volume_groups-my_new_lv 253:2    0  508M  0 lvm  /files
├─nvme0n2p2                        259:6    0  500M  0 part 
├─nvme0n2p3                        259:7    0  500M  0 part 
└─nvme0n2p4                        259:8    0  500M  0 part 

## Resizing LVM LVs

This is the one of the greatest benefit of using LVM LVs. If the system is running out of available disk space. 

**Resizing VG (volume groups)**
    vgextend
    vgreduce
    vgs - for verify

Steps:
1. Make sure a free space is available in the VG
    sudo vgs
  VG                   #PV #LV #SN Attr   VSize    VFree  
  my_new_volume_groups   1   1   0 wz--n- 1020.00m 512.00m
  rhel                   1   2   0 wz--n-  <39.00g      0 

Can see here that 512 MB are free in the VG

2. Use **vgextend** to extend the group

**Resizing LVs and File Systems**
For extension of LVs - **lvextend** or **lvresize**
Example: **lvresize -L +512MiB -r /dev/my_new_volume_groups/my_new_lv
Some other options:
    - lvresize -r -l 75%VG /dev/my_new_volume_groups/my_new_lv --> takes 75% of the total disk space in VG on top of LV
    - lvresize -r -l +75%VG /dev/my_new_volume_groups/my_new_lv --> tries to add 75% of total VG size on top of LV
    - lvresize -r -l +75%FREE /dev/my_new_volume_groups/my_new_lv --> add 75% based on the free available
    - lvresize -r -l 75%FREE /dev/my_new_volume_groups/my_new_lv --> resize that it equals to the 75 % of the VG disk space.

### Exercise 15-3 Resizing Logical Volumes

1. Check current physical volume
    vgs
2. Check that there are any free partitions
    lsblk 
output:
nvme0n2                            259:4    0    3G  0 disk 
├─nvme0n2p1                        259:5    0    1G  0 part 
│ └─my_new_volume_groups-my_new_lv 253:2    0  508M  0 lvm  /files
├─nvme0n2p2                        259:6    0  500M  0 part 
├─nvme0n2p3                        259:7    0  500M  0 part 
└─nvme0n2p4                        259:8    0  500M  0 part 

3. Extend the VG
    vgextend <VG_name> <PV_name>

Check the PVs which are used:
sudo pvs
  PV             VG                   Fmt  Attr PSize    PFree  
  /dev/nvme0n1p3 rhel                 lvm2 a--   <39.00g      0 
  /dev/nvme0n2p1 my_new_volume_groups lvm2 a--  1020.00m 512.00m

If try to use the used one, will get:
Physical volume '/dev/nvme0n2p1' is already in volume group 'my_new_volume_groups'
  Unable to add physical volume '/dev/nvme0n2p1' to volume group 'my_new_volume_groups'
  /dev/nvme0n2p1: physical volume not initialized.

Then use the right volume
    sudo vgextend my_new_volume_groups /dev/nvme0n2p2
Check with sudo pvs
Output:
  PV             VG                   Fmt  Attr PSize    PFree  
  /dev/nvme0n1p3 rhel                 lvm2 a--   <39.00g      0 
  /dev/nvme0n2p1 my_new_volume_groups lvm2 a--  1020.00m 512.00m
  /dev/nvme0n2p2 my_new_volume_groups lvm2 a--   496.00m 496.00m

And now check with vgs
  VG                   #PV #LV #SN Attr   VSize   VFree   
  my_new_volume_groups   2   1   0 wz--n-   1.48g 1008.00m
  rhel                   1   2   0 wz--n- <39.00g       0

We can see that the total free size of the VG was increased. 

4. Check the size of the current LV file system
    df -h
Output:
/dev/mapper/rhel-root                        36G  7.2G   28G  21% /
devtmpfs                                    1.8G     0  1.8G   0% /dev
tmpfs                                       1.8G   84K  1.8G   1% /dev/shm
tmpfs                                       725M  1.8M  723M   1% /run
tmpfs                                       1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
/dev/nvme0n1p2                              960M  592M  369M  62% /boot
/dev/sr0                                    9.5G  9.5G     0 100% /repo
tmpfs                                       363M  144K  362M   1% /run/user/1000
/dev/mapper/my_new_volume_groups-my_new_lv  466M   14K  437M   1% /files
tmpfs                                       363M   56K  363M   1% /run/user/0

5. Add 50% of free space to the LV.
    lvextend -r -l +50%FREE /dev/my_new_volume_groups/my_new_lv
Output:
File system ext4 found on my_new_volume_groups/my_new_lv mounted at /files.
  Size of logical volume my_new_volume_groups/my_new_lv changed from 508.00 MiB (127 extents) to 1012.00 MiB (253 extents).
  Extending file system ext4 to 1012.00 MiB (1061158912 bytes) on my_new_volume_groups/my_new_lv...
resize2fs /dev/my_new_volume_groups/my_new_lv
resize2fs 1.47.1 (20-May-2024)
Filesystem at /dev/my_new_volume_groups/my_new_lv is mounted on /files; on-line resizing required
old_desc_blocks = 4, new_desc_blocks = 8
The filesystem on /dev/my_new_volume_groups/my_new_lv is now 1036288 (1k) blocks long.

resize2fs done
  Extended file system ext4 on my_new_volume_groups/my_new_lv.
  Logical volume my_new_volume_groups/my_new_lv successfully resized.

6. Check with: vgs, lvs, pvs, df -h

vgs
sudo vgs
  VG                   #PV #LV #SN Attr   VSize   VFree  
  my_new_volume_groups   2   1   0 wz--n-   1.48g 504.00m
  rhel                   1   2   0 wz--n- <39.00g      0 

Free space was reduced in twice

lvs
 LV        VG                   Attr       LSize    Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  my_new_lv my_new_volume_groups -wi-ao---- 1012.00m                                                    
  root      rhel                 -wi-ao----   35.08g                                                    
  swap      rhel                 -wi-ao----    3.91g

Can see that the LV size was increased

pvs
  PV             VG                   Fmt  Attr PSize    PFree  
  /dev/nvme0n1p3 rhel                 lvm2 a--   <39.00g      0 
  /dev/nvme0n2p1 my_new_volume_groups lvm2 a--  1020.00m   8.00m
  /dev/nvme0n2p2 my_new_volume_groups lvm2 a--   496.00m 496.00m

Can see there is less Free space now in the PV /dev/nvme0n2p1

df -h
/dev/mapper/my_new_volume_groups-my_new_lv  938M   14K  889M   1% /files

see that now the size was increased
Note: compare all these with the above output before resizing

### Exercise 15-4 Removing a VG from a PV

1. Can clean the previous partitions and mounts, from previous tasks
2. Create 2 partitions with the allowed/desire size. 
nvme0n2       259:4    0    3G  0 disk 
├─nvme0n2p1   259:5    0    1G  0 part 
└─nvme0n2p2   259:6    0    1G  0 part
3. Create a VG
    vgcreate vg_for_resize_demo /dev/nvme0n2p1
4. Create a LV with 100% space of VG
    sudo lvcreate -L 1G -n lv_for_resize_demo_vg_group vg_for_resize_demo
5. Extend a VG
    vgextend vg_for_resize_demo /dev/nvme0n2p2
6. Check with vgs
    VG                 #PV #LV #SN Attr   VSize   VFree   
    rhel                 1   2   0 wz--n- <39.00g       0 
    vg_for_resize_demo   2   1   0 wz--n-   1.99g 1020.00m
pvs
    /dev/nvme0n2p1 vg_for_resize_demo lvm2 a--  1020.00m       0 
    /dev/nvme0n2p2 vg_for_resize_demo lvm2 a--  1020.00m 1020.00m

We can see that the extents on the /dev/nvme0n2p2 are not used: 1020.00m

7. Now, let's extend the logical volume of the volume group by taking the space from the PV
    sudo lvextend -L +500MiB /dev/vg_for_resize_demo/lv_for_resize_demo_vg_group /dev/nvme0n2p2

Check with pvs
  PV             VG                 Fmt  Attr PSize    PFree  
  /dev/nvme0n1p3 rhel               lvm2 a--   <39.00g      0 
  /dev/nvme0n2p1 vg_for_resize_demo lvm2 a--  1020.00m      0 
  /dev/nvme0n2p2 vg_for_resize_demo lvm2 a--  1020.00m 520.00m

we can see the nvme0n2p2 was half reduced
Now with vgs
VG                 #PV #LV #SN Attr   VSize   VFree  
  rhel                 1   2   0 wz--n- <39.00g      0 
  vg_for_resize_demo   2   1   0 wz--n-   1.99g 520.00m

Can see that less free space for extend available across VG. 

and with lvs
  LV                          VG                 Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync 
  lv_for_resize_demo_vg_group vg_for_resize_demo -wi-a-----  1.48g

We can see that the size of the vg_for_resize_demo was increased
before was as:
  lv_for_resize_demo_vg_group vg_for_resize_demo -wi-a----- 1020.00m 

See with lsblk now
nvme0n2                                            259:4    0    3G  0 disk 
├─nvme0n2p1                                        259:5    0    1G  0 part 
│ └─vg_for_resize_demo-lv_for_resize_demo_vg_group 253:2    0  1.5G  0 lvm  
└─nvme0n2p2                                        259:6    0    1G  0 part 
  └─vg_for_resize_demo-lv_for_resize_demo_vg_group 253:2    0  1.5G  0 lvm  

7.1 Check the file system lsblk -f

8. Create a file system
    mkfs.ext4 /dev/vg_for_resize_demo/lv_for_resize_demo_vg_group
8.1 Check the file system now
    lsblk -f

9. Mount temporary LV, VG to /mnt-temp
    mount /dev/vg_for_resize_demo/lv_for_resize_demo_vg_group /mnt-temp

9.1 Check with lsblk if mounted
10. Check the disk space
    df -h
Output:
Filesystem                                                  Size  Used Avail Use% Mounted on
/dev/mapper/rhel-root                                        36G  7.2G   28G  21% /
devtmpfs                                                    1.8G     0  1.8G   0% /dev
tmpfs                                                       1.8G   84K  1.8G   1% /dev/shm
tmpfs                                                       725M  1.8M  723M   1% /run
tmpfs                                                       1.0M     0  1.0M   0% /run/credentials/systemd-journald.service
/dev/nvme0n1p2                                              960M  592M  369M  62% /boot
/dev/sr0                                                    9.5G  9.5G     0 100% /repo
tmpfs                                                       363M  140K  362M   1% /run/user/1000
/dev/mapper/vg_for_resize_demo-lv_for_resize_demo_vg_group  1.5G   24K  1.4G   1% /mnt-temp
tmpfs                                                       363M   56K  363M   1% /run/user/0

11. Now, if the one LV has the exact same free space as another one:
PV             VG                 Fmt  Attr PSize    PFree  
  /dev/nvme0n1p3 rhel               lvm2 a--   <39.00g      0 
  /dev/nvme0n2p1 vg_for_resize_demo lvm2 a--  1020.00m      0 
  /dev/nvme0n2p2 vg_for_resize_demo lvm2 a--  1020.00m 520.00m

We can move the one LV to another with 
    pvmove -v /dev/nvme0n2p1 /dev/nvme0n2p2

Otherwise it gives:
  activation/volume_list configuration setting not defined: Checking only host tags for vg_for_resize_demo/lv_for_resize_demo_vg_group.
  Moving 255 extents of logical volume vg_for_resize_demo/lv_for_resize_demo_vg_group.
  Insufficient free space: 255 extents needed, but only 130 available
  Unable to allocate mirror extents for vg_for_resize_demo/pvmove0.
  Failed to convert pvmove LV to mirrored

12. Check with pvs
13. Remove unused LV with vgreduce

# Chapter 16 - Basic Kernel Management

Kernel = core, and in Linux, more precisely, the kernel is the core of the operating system.

              USER
               ↓
        Applications / Shell
               ↓
       ┌───────────────┐
       │ Linux KERNEL  │  ← core of the OS
       └───────────────┘
          ↓    ↓    ↓
        CPU   RAM  Disk/NIC

The kernel's job is essentially to manage the hardware and provide controlled access to it for programs.
Example, when execute: cat /etc/hosts
what happens behind: cat -> system call -> Kernel -> storage driver -> disk

## For Analazying kernel activities
There are several utilities to analyze kernel.
    - dmesg utility (print or control the kernel ring buffer)
    - /proc file system
    - uname utility

A ring buffer is a small, fixed-size area of memory that works like a circular log.
[1] [2] [3] [4] [5]
 ↑
oldest
A new message arrives: messgae 6. Instead of growing forever, it overwrites the oldest message:
[6] [2] [3] [4] [5]
 ↑
new oldest

Modern kernels treat the ring buffer as a collection of log records/messages, not literally a simple array of fixed-size “cells.” A single kernel message can contain quite a bit of text. The kernel ring buffer is a fixed-size circular storage area containing kernel log records. When it fills, old records are discarded to make room for new ones.

**Example of utilities usage**
dsmeg OR journalctl --dmesg OR journactl -k

Where dmesg shows the relative kernel start time, e.g:
[ 7487.519446] EXT4-fs (dm-2): resizing filesystem from 520192 to 1036288 blocks
[ 7487.544915] EXT4-fs (dm-2): resized filesystem to 1036288
[ 7906.888069] vmxnet3 0000:03:00.0 ens160: intr type 3, mode 0, 3 vectors allocated
[ 7906.888674] vmxnet3 0000:03:00.0 ens160: NIC Link is Up 10000 Mbps
[ 8717.235762] EXT4-fs (dm-2): unmounting filesystem b22808de-16bf-4e07-8d43-847e02cbb661.
[ 9281.795290]  nvme0n2: p1 p2

But the journalctl shows the clock time
ep 11 11:24:53 localhost.localdomain kernel: EXT4-fs (dm-2): unmounting filesystem b22808de-16bf-4e07-8d43-847e02cbb661.
Sep 11 11:34:18 localhost.localdomain kernel:  nvme0n2: p1 p2
Sep 11 11:34:18 localhost.localdomain kernel:  nvme0n2: p1 p2
Sep 11 12:02:04 localhost.localdomain kernel: EXT4-fs (dm-2): mounted filesystem bc6b2032-9620-48dc-acb4-d49b5402e4f5 r/w with ordered data mode. Quota mode: none.
Sep 12 20:18:08 localhost.localdomain kernel: vmxnet3 0000:03:00.0 ens160: intr type 3, mode 0, 3 vectors allocated
Sep 12 20:18:08 localhost.localdomain kernel: vmxnet3 0000:03:00.0 ens160: NIC Link is Up 10000 Mbps

The /proc directory can give the clear information about kernel segments. E.g., memory segments: /proc/meminfo

**uname** **-a** **-r** useful command, gives name and information about current kernel
Note: to get the specific RHEL info try: cat /etc/redhat-release
**hostnamectl** **status**

**What is the driver?**
A hardware driver is software that knows how to communicate with a particular piece of hardware on behalf of the operating system.
Example:
Firefox
   ↓
Linux kernel: "send this network packet"
   ↓
Network driver: "I know how to talk to this specific NIC"
   ↓
Network card
   ↓
Ethernet/Wi-Fi

## Managing Kernel Modules
Linux modules normally loaded automatically. But in rare cases in can be done manually. To customize and load modules manually can be done from /etc/modules-load.d directory. 

**lsmod** command to be used when working with kernel modules

What is the **module**? - A kernel module is a separately developed piece of code that extends what the kernel can do.
Kernel starts
    │
    ├── core functionality ─── always there
    │
    └── modules
          ├── network driver
          ├── USB driver
          ├── filesystem support
          └── etc.
                ↑
          loaded when needed