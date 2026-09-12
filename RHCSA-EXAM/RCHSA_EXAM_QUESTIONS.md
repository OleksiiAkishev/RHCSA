## CHAPTER 1

1. Which of the following commands enables you to redirect standard output as well as standard error to a file?
   - a. `1&2> file`
   - b. `&> file`
   - c. `>1&2 file`
   - d. `1>2& file`
   - **Answer:**

2. You want to set a local variable that will be available for every user in a login shell as well as a subshell. Which of the following files should you use?
   - a. `/etc/profile`
   - b. `/etc/bashrc`
   - c. `~/.bash_profile`
   - d. `~/.bashrc`
   - **Answer:**

3. A user has created a script with the name `myscript`. The user tries to run the script using the command `myscript`, but it does not start. The user has verified that the script permissions are set as executable. Which of the following is the most likely explanation?
   - a. An internal command is preventing the startup of the script.
   - b. Users are not allowed to run scripts.
   - c. The directory that contains the script is not in the `$PATH` variable.
   - d. The script does not have appropriate permissions.
   - **Answer:**

4. You need the output of the `ls` command to be used as input for the `less` command. Which of the following examples will do that for you?
   - a. `ls > less`
   - b. `ls >> less`
   - c. `ls >| less`
   - d. `ls | less`
   - **Answer:**

5. A user accidentally typed a password, which now shows as item 299 in history. Which of the following do you recommend to ensure the password is not stored in history?
   - a. Remove the `~/.bash_history` file and type `history -c`.
   - b. Type `history -c`.
   - c. Remove the `~/.bash_history` file.
   - d. Type `history -d 299`.
   - **Answer:**

6. Which of the following is not a valid method to repeat a command from history?
   - a. Press `Ctrl-R` and start typing a part of the command.
   - b. Type `!` followed by the first letters in the command.
   - c. Type `!` followed by the number of the command as listed in history.
   - d. Press `Ctrl-X` followed by the number in history.
   - **Answer:**

7. For which of the following items can Bash completion be used?
   - a. Commands
   - b. Files
   - c. Variables
   - d. All of the above
   - **Answer:**

8. Which of the following commands enables you to replace every occurrence of `old` with `new` in a text file that is opened with `vim`?
   - a. `:%s/old/new/g`
   - b. `:%r/old/new/`
   - c. `:%s/old/new/`
   - d. `r:/old/new`
   - **Answer:**

9. Which approach works best if during the login process you want to show a message to all users who have just logged in to a shell session on your server?
   - a. Put the message in `/etc/issue`.
   - b. Put the message in `/etc/motd`.
   - c. Put the message in `/etc/profile`.
   - d. Put the message in `/etc/bashrc`.
   - **Answer:**

10. You are using `man -k user`, but you get the message "nothing appropriate." Which of the following solutions is most likely to fix this issue for you?
   - a. Type `sudo updatedb` to update the mandb database.
   - b. Type `sudo makewhatis` to update the mandb database.
   - c. Type `sudo mandb` to update the mandb database.
   - d. Use `man -K`, not `man -k`.
   - **Answer:**


## CHAPTER 9 — Managing Software

1. Which of the following is not a mandatory component in a `.repo` file that is used to indicate which repositories should be used?
   - a. `[label]`
   - b. `name=`
   - c. `baseurl=`
   - d. `gpgcheck=`
   - **Answer:**

2. Which installation source is used on RHEL if a server is not registered with Red Hat?
   - a. The installation medium is used.
   - b. No installation source is used.
   - c. The base Red Hat repository is used, without updates.
   - d. You have full access to Red Hat repositories, but the software you are using is not supported.
   - **Answer:**

3. Which of the following should be used in the `.repo` file to refer to a repository that is in the directory `/repo` on the local file system?
   - a. `file=/repo`
   - b. `baseurl=file://repo`
   - c. `baseurl=file:///repo`
   - d. `file=http:///repo`
   - **Answer:**

4. Which command enables you to search the package that contains the file `seinfo`?
   - a. `dnf search seinfo`
   - b. `dnf search all seinfo`
   - c. `dnf provides seinfo`
   - d. `dnf whatprovides */seinfo`
   - **Answer:**

5. Which of the following would show all packages that have a name starting with `selinux`?
   - a. `dnf list selinux`
   - b. `dnf list selinux*`
   - c. `dnf search selinux`
   - d. `dnf provides selinux`
   - **Answer:**

6. What do you need to do to ensure that the local system has a list of the latest versions of packages available in the repositories?
   - a. Use the `dnf update` command before upgrading any packages.
   - b. Use the `dnf refresh` command before upgrading any packages.
   - c. Use the `dnf cache` command before upgrading any packages.
   - d. Nothing, it happens automatically.
   - **Answer:**

7. Which command enables you to find the RPM package a specific file belongs to?
   - a. `rpm -ql /my/file`
   - b. `rpm -qlf /my/file`
   - c. `rpm -qf /my/file`
   - d. `rom -qa /my/file`
   - **Answer:**

8. Which command enables you to analyze whether there are scripts in an RPM package file that you have just downloaded?
   - a. `rpm -qs packagename.rpm`
   - b. `rpm -qps packagename.rpm`
   - c. `rpm -qp --scripts packagename.rpm`
   - d. `rpm -q --scripts packagename.rpm`
   - **Answer:**

9. Which of the following is not a benefit of using Flatpak?
   - a. It provides an easy interface to install applications on servers as well as workstations.
   - b. It allows the use of different versions of the same software on a computer.
   - c. It allows users to install their own applications.
   - d. It is based on container technology.
   - **Answer:**

10. Which of the following statements is true about Flatpak user applications?
   - a. To install an application for a user, the administrator must use the `--user` option with the `flatpak install` command.
   - b. Users can install Flatpak applications if they are a member of the `flatpak` group.
   - c. To install Flatpak applications, users must have administrative privileges.
   - d. If a user installs a Flatpak application, it will be accessible for that user only.
   - **Answer:**


## CHAPTER 10 — Managing Processes

1. Which of the following are not generally considered a type of process? (Choose two.)
   - a. A shell job
   - b. A cron job
   - c. A daemon
   - d. A thread
   - **Answer:**

2. Which of the following can be used to move a job to the background?
   - a. Press `&`
   - b. Press `Ctrl-Z` and then type `bg`
   - c. Press `Ctrl-D` and then type `bg`
   - d. Press `Ctrl-Z`, followed by `&`
   - **Answer:**

3. Which key combination enables you to cancel a current interactive shell job?
   - a. `Ctrl-C`
   - b. `Ctrl-D`
   - c. `Ctrl-Z`
   - d. `Ctrl-Break`
   - **Answer:**

4. Which of the following statements are true about threads? (Choose two.)
   - a. Threads cannot be managed individually by an administrator.
   - b. Multithreaded processes can make the working of processes more efficient.
   - c. Threads can be used only on supported platforms.
   - d. Using multiple processes is more efficient, in general, than using multiple threads.
   - **Answer:**

5. Which of the following commands is most appropriate if you’re looking for detailed information about the command and how it was started?
   - a. `ps ef`
   - b. `ps aux`
   - c. `ps`
   - d. `ps fax`
   - **Answer:**

6. Of the following nice values, which will increase the priority of the selected process?
   - a. `100`
   - b. `20`
   - c. `-19`
   - d. `-100`
   - **Answer:**

7. Which of the following shows correct syntax to change the priority for the current process with PID 1234?
   - a. `nice -n 5 1234`
   - b. `renice 5 1234`
   - c. `renice 5 -p 1234`
   - d. `nice 5 -p 1234`
   - **Answer:**

8. Which of the following commands cannot be used to send signals to processes?
   - a. `kill`
   - b. `Ctrl-Break`
   - **Answer:**


## CHAPTER 11 — Working with systemd

1. Which command shows all service unit files on your system that are currently loaded?
   - a. `systemctl -t service`
   - b. `systemctl -t service --all`
   - c. `systemctl --list-services`
   - d. `systemctl --show-units | grep services`
   - **Answer:**

2. Which statement about Systemd wants is not true?
   - a. You can create wants by using the `systemctl enable` command.
   - b. The target to which a specific want applies is agnostic of the associated wants.
   - c. Wants are always administered in the `/usr/lib/systemd/system` directory.
   - d. Each service knows to which target it wants to be added.
   - **Answer:**

3. What is the best solution to avoid conflicts between incompatible units?
   - a. Nothing; the unit files have defined for themselves which units they are not compatible with.
   - b. Disable the service using `systemctl disable`.
   - c. Unmask the service using `systemctl unmask`.
   - d. Mask the service using `systemctl mask`.
   - **Answer:**

4. Which of the following is not a valid status for Systemd services?
   - a. `Active(running)`
   - b. `Active(exited)`
   - c. `Active(waiting)`
   - d. `Running(dead)`
   - **Answer:**

5. Which of the following statements is not true about socket units?
   - a. A socket unit requires a service unit with the same name.
   - b. Socket units can listen on ports and activate services only when activity occurs on a port.
   - c. Socket units cannot contain the name of the associated binary that should be started.
   - d. Socket units may react upon path activity.
   - **Answer:**

6. Which of the following is not a valid Systemd unit type?
   - a. `service`
   - b. `udev`
   - c. `mount`
   - d. `socket`
   - **Answer:**

7. You want to find out which other Systemd units have dependencies to a specific unit. Which command would you use?
   - a. `systemd list-dependencies --reverse`
   - b. `systemctl list-dependencies --reverse`
   - c. `systemctl status my.unit --show-deps`
   - d. `systemd status my.unit --show-deps -r`
   - **Answer:**

8. How do you change the default editor that Systemd uses to `vim`?
   - a. `export EDITOR=vim`
   - b. `export SYSTEMD_EDITOR=vim`
   - c. `export EDITOR=//usr/bin/vim`
   - d. `export SYSTEMD_EDITOR=/usr/bin/vim`
   - **Answer:**

9. Which of the following keywords should you use to define a Systemd dependency if you want to ensure that the boot procedure doesn’t fail if the dependency fails?
   - a. `Required`
   - b. `Requisite`
   - c. `Before`
   - d. `Wants`
   - **Answer:**

10. Which of the following is not a valid command while working with units in `systemctl`?
   - a. `systemctl unit start`
   - b. `systemctl status -l unit`
   - c. `systemctl mask unit`
   - d. `systemctl disable unit`
   - **Answer:**

## Chapter 12 - Scheduling tasks

. What is the default solution for scheduling recurring jobs in RHEL 10? a. Systemd timers b. cron c. anacron d. at   . How do you configure a timer to start at a specific time? a. Use a cron-style starting time notation in the [Timer] section. b. Use OnCalendar in the [Timer] section. c. Use OnTime in the [Timer] section. d. Schedule it through cron.   . You want a timer to be started 1 minute after starting of the Systemd service. Which option do you use? a. OnCalendar b. OnUnitActiveSec c. OnBootSec d. OnStartupSec   . You want a Systemd user unit to be started 2 minutes after the user has logged in. Which of the following would you use? a. OnCalendar b. OnUserLogin c. OnUserActiveSec d. OnStartupSec   . Which of the following would run a cron task Sunday at 11 a.m.? a. * 11 7 * * b. 0 11 * 7 * c. 0 11 * * 7 d. 11 0 * 7 *   . Which of the following launches a job every 5 minutes from Monday through Friday? a. */5 * * * 1-5 b. */5 * 1-5 * * c. 0/5 * * * 1-5 d. 0/5 * 1-5 * *   . How do you create a cron job for a specific user? a. Log in as that user and type crontab -e to open the cron editor. b. Open the crontab file in the user home directory and add what you want to add. c. As root, type crontab -e username. d. As root, type crontab -u username -e. Which of the following is not a recommended way to specify jobs that should be executed with cron? a. Modify /etc/crontab. b. Put the jobs in separate scripts in /etc/cron.d. c. Use crontab -e to create user-specific cron jobs. d. Put scripts in /etc/cron.{hourly|daily|weekly|monthly} for automatic execution.   . After you enter commands in the at shell, which command enables you to close the at shell? a. Ctrl-V b. Ctrl-D c. exit d. :wq    . Which command enables you to see current at jobs scheduled for execution? a. atrm b. atls c. atq d. at

# Chapter 13 Configuring logging

. Which of the following statements about systemd-journald is not true? a. systemd-journald logs kernel messages. b. systemd-journald writes to the journal, which by default does not persist between boots. c. systemd-journald is a replacement of rsyslogd. d. To read files from the Systemd journal, you use the journalctl command.   . Which log would you read to find messages related to authentication errors? a. /var/log/messages b. /var/log/lastlog c. /var/log/audit/audit.log d. /var/log/secure   . Which log would you read to find information that relates to SELinux events? a. /var/log/messages b. /var/log/lastlog c. /var/log/audit/audit.log d. /var/log/secure . Which directory is used to store the Systemd journal persistently? a. /var/log/journal b. /var/run/journal c. /run/log d. /run/log/journal   . What do you need to do to make the Systemd journal persistent? a. Create the directory /var/log/journal. b. Open /etc/sysconfig/journal and set the PERSISTENT option to yes. c. Open the /etc/systemd/journald.conf file and set the PERSISTENT option to yes. d. Create the /var/log/journal file and set appropriate permissions.   . After making the Systemd journal persistent, what should you do to immediately activate this change? a. Reboot your server. b. Nothing, it will be picked up automatically. c. Use systemctl daemon-reload. d. Use systemctl restart systemd-journal-flush.   . What is the name of the rsyslogd configuration file? a. /etc/rsyslog.conf b. /etc/sysconfig/rsyslogd.conf c. /etc/sysconfig/rsyslog.conf d. /etc/rsyslog.d/rsyslogd.conf   . In the rsyslog.conf file, which of the following destinations refers to a specific rsyslogd module? a. -/var/log/maillog b. /var/log/messages c. :omusrmsg:* d. *   . Which facility is the best solution if you want to configure the Apache web server to log messages through rsyslog? a. daemon b. apache c. syslog d. local0-7    . You want to maximize the file size of a log file to 10 MB. Where do you configure this? a. Create a file in /etc/logrotate.d and specify the maximal size in that file. b. Put the maximal size in the logrotate cron job. c. Configure the destination with the maximal size option. d. This cannot be done.

# Chapter 14 - Managing storage

. Which of the following is not an advantage of using a GUID partition table over using an MBR partition table? a. Access time to a directory is quicker. b. A total amount of 8 ZiB can be addressed by a partition. c. With GUID partitions, a backup copy of the partition table is created automatically. d. There can be up to 128 partitions in total.   . Which of the following statements about GPT partitions is not true? a. You can easily convert an existing MBR disk to GPT by using gdisk. b. You can use fdisk to write a GPT disk label. c. Partition types in GPT are four characters instead of two characters. d. GPT partitions can be created on MBR as well as EFI systems.   . Which partition type is commonly used to create a swap partition? a. 81 b. 82 c. 83 d. 8e   . What is the default disk device name you would expect to see in KVM virtual machines? a. /dev/sda b. /dev/hda c. /dev/vda d. /dev/xsda   . Which of the following statements is not true? a. You should not ever use gdisk on an MBR disk. b. fdisk also offers support to manage GPT partitions. c. Depending on your needs, you can create MBR and GPT partitions on the same disk. d. If your server boots from EFI, you must use GPT partitions.   . Which of the following file systems is used as the default in RHEL 10? a. Ext4 b. XFS c. btrfs d. Ext3   . Which command enables you to find current UUIDs set to the file systems on your server? a. mount b. df -h c. lsblk d. blkid   . What would you put in the device column of /etc/fstab to mount a file system based on its unique ID 42f419c4-633f-4ed7-b161-519a4dadd3da? a. 42f419c4-633f-4ed7-b161-519a4dadd3da b. /dev/42f419c4-633f-4ed7-b161-519a4dadd3da c. ID=42f419c4-633f-4ed7-b161-519a4dadd3da d. UUID=42f419c4-633f-4ed7-b161-519a4dadd3da   . Which command can you use to verify the contents of /etc/fstab before booting? a. fsck --fstab b. findmnt --verify c. mount -a d. reboot    . While you’re creating a Systemd mount unit file, different elements are required. Which of the following is not one of them? a. The mount unit filename corresponds to the mount point. b. An [Install] section is included to set the default runlevel. c. A what statement is included to indicate what should be mounted. d. A where statement is included to indicate where the device should be mounted.

# Chapter 15 - Managing logical volumes
. Which of the following is not a standard component in an LVM setup? a. Logical volume b. File system c. Volume group d. Physical volume   . Which of the following is not an LVM feature? a. Volume resizing b. Hot replacement of failing disk c. Copy on write d. Snapshots   . Which of the following is not typically used as a physical volume? a. SAN drive b. Partition c. File d. SATA disk . Which partition type do you need on a GPT partition to mark it with the LVM partition type? a. 83 b. 8e c. 8300 d. 8e00   . Which of the following commands shows correctly how to create a logical volume that uses 50 percent of available disk space in the volume group? a. vgadd -n lvdata -l +50%FREE vgdata b. lvcreate lvdata -l 50%FREE vgdata c. lvcreate -n lvdata -l 50%FREE vgdata d. lvadd -n lvdata -l 50% FREE /dev/vgdata   . Which commands show an overview of available physical volumes? (Choose two.) a. pvshow b. pvdisplay c. pvs d. pvlist   . Which of the following are correct device names for the logical volume lvdata in the volume group vgdata? a. /dev/vgdata-lvdata b. /dev/mapper/vgdata-lvdata c. /dev/mapper/vgdata/lvdata d. /dev/vgdata/lvdata   . Which statement about resizing LVM logical volumes is not true? a. The Ext4 file system can be increased and decreased in size. b. Use lvextend with the -r option to automatically resize the file system. c. The XFS file system cannot be resized. d. To increase the size of a logical volume, you need allocatable space in the volume group.   . You want to remove the physical volume /dev/sdd2 from the volume group vgdata. Which of the following statements about the removal procedure is not true? a. The file system must support shrinking. b. You need the amount of used extents on /dev/sdd2 to be available on remaining devices. c. Before you can use vgreduce, you have to move used extents to the remaining volumes. d. Use pvmove to move used extents.    . You have extended the size of a logical volume without extending the XFS file system it contains. Which of the following solutions can you use to fix it? a. Use lvresize again, but this time with the -r option. The command will resize just the file system. b. Bring the logical volume back to its original size and then use lvresize -r again. c. Use fsresize to resize the file system later. d. Use xfs_growfs to grow the file system to the size available in the logical volume.


# Chapter 16 - Basic Kernel Management
. What causes a tainted kernel? a. A kernel driver that is not available as an open source driver b. A driver that was developed for a different operating system but has been ported to Linux c. A driver that has failed d. An unsupported driver   . Which command shows kernel events since booting?
a. logger b. dmesg c. klogd d. journald   . Which command enables you to find the actual version of the kernel that is used? a. uname -r b. uname -v c. procinfo -k d. procinfo -l   . Which command shows the current version of RHEL you are using? a. uname -r b. cat /proc/rhel-version c. cat /etc/redhat-release d. uname -k   . What is the name of the process that helps the kernel to initialize hardware devices properly? a. systemd-udevd b. hwinit c. udev d. udevd   . Where does your system find the default rules that are used for initializing new hardware devices? a. /etc/udev/rules.d b. /usr/lib/udev/rules.d c. /usr/lib/udev.d/rules d. /etc/udev.d/rules   . Which command should you use to unload a kernel module, including all of its dependencies? a. rmmod b. insmod -r c. modprobe -r d. modprobe   . Which command enables you to see whether the appropriate kernel modules have been loaded for hardware in your server? a. lsmod b. modprobe -l c. lspci -k d. lspci   . Where do you specify a kernel module parameter to make it persistent? a. /etc/modules.conf b. /etc/modprobe.conf c. /etc/modprobe.d/somefilename d. /usr/lib/modprobe.d/somefilename    . Which statements about updating the kernel are not true? a. The dnf update kernel command will install a new kernel and not update it. b. The dnf install kernel command will install a new kernel and keep the old kernel. c. The kernel package should be set as a dnf-protected package to ensure that after an update the old kernel is still available. d. After you have installed a new kernel version, you must run the grub2-mkconfig command to modify the GRUB 2 boot menu so that it shows the old kernel and the newly installed kernel.