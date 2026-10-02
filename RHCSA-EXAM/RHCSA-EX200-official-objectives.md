# RHCSA (EX200) — Official Objectives

Reference: https://www.redhat.com/en/services/training/ex200-red-hat-certified-system-administrator-rhcsa-exam

RHCSA candidates should be able to accomplish the tasks below without assistance. Items are grouped by category and kept concise for study reference.

## Understand and use essential tools
- Access a shell prompt and issue commands with correct syntax
- Use input/output redirection (`>`, `>>`, `|`, `2>`, etc.)
- Use `grep` and regular expressions to analyze text
- Access remote systems using SSH
- Log in and switch users in multi-user targets
- Archive, compress, unpack, and uncompress files (`tar`, `gzip`, `bzip2`)
- Create and edit text files
- Create, delete, copy, and move files and directories
- Create hard and soft links
- List, set, and change standard ugo/rwx permissions
- Locate, read, and use system documentation (`man`, `info`, `/usr/share/doc`)

## Manage software
- Configure access to RPM repositories
- Install and remove RPM packages
- Configure access to Flatpak repositories
- Install and remove Flatpak packages
- Create simple shell scripts
	- Conditional execution (`if`, `test`, `[]`)
	- Loop constructs (`for`, etc.)
	- Process script inputs (`$1`, `$2`, ...)
	- Use command substitution and process command output in scripts

## Operate running systems
- Boot, reboot, and shut down a system normally
- Boot systems into different targets manually
- Interrupt the boot process to gain access to a system
- Identify CPU/memory intensive processes and kill processes
- Adjust process scheduling (nice/renice)
- Manage tuning profiles
- Locate and interpret system log files and `journalctl` output
- Preserve system journals
- Start, stop, and check the status of network services
- Securely transfer files between systems

## Configure local storage
- List, create, and delete partitions on GPT disks
- Create and remove physical volumes (LVM)
- Assign physical volumes to volume groups
- Create and delete logical volumes
- Configure systems to mount file systems at boot by UUID or label
- Add partitions and logical volumes non-destructively
- Create and configure file systems
- Create, mount, unmount, and use VFAT, ext4, and XFS file systems
- Mount and unmount network file systems using NFS
- Configure `autofs`
- Extend existing logical volumes
- Diagnose and correct file permission problems

## Deploy, configure, and maintain systems
- Schedule tasks using `at`, `cron`, and systemd timer units
- Start and stop services and configure services to start automatically at boot
- Configure systems to boot into a specific target automatically
- Configure time service clients (chrony/ntp)
- Install and update software packages from CDN, remote repos, or local filesystem
- Modify the system bootloader (grub2)

## Manage basic networking
- Configure IPv4 and IPv6 addresses
- Configure hostname resolution
- Configure network services to start automatically at boot
- Restrict network access using `firewalld` and `firewall-cmd`

## Manage users and groups
- Create, delete, and modify local user accounts
- Change passwords and adjust password aging for local accounts
- Create, delete, and modify local groups and group memberships
- Configure privileged access (sudo)

## Manage security
- Configure firewall settings using `firewall-cmd`/`firewalld`
- Manage default file permissions (umask)
- Configure key-based SSH authentication
- Set SELinux enforcing and permissive modes
- List and identify SELinux file and process contexts
- Restore default file contexts
- Manage SELinux port labels
- Use SELinux Boolean settings to modify system behavior

> Note: As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.



# Tips from Book

## Tip 1
On the RHCSA exam, you are likely to enable a couple of services. It is a good idea to read through the exam questions, identify the services that need to be enabled, and enable them all at once to make sure that they are started automatically when you restart. This approach prevents your being so focused on configuring the service that you completely forget to enable it.

## Tip 2
On the exam, you want to know immediately if something does not work out well. To accomplish this, it is a good idea to remove the rhgb and quiet boot options. Without them, you will not have to guess why your server takes a long time after a restart; you’ll just be able to see.

## Tip 3
You should know how to apply changes to the GRUB configuration, but you should also know that the default GRUB 2 configuration works fine as it is for almost all computers. So, you will probably never have to apply any changes at all!

## Tip 4
Troubleshooting has always been a part of the RHCSA exam. If you encounter an issue, make sure that you can identify in which phase of the boot procedure it occurs and what you can do to fix it.

## Tip 5 
According to the RHCSA objectives, you should not have to work with a rescue disk on the exam. However, as a Linux administrator, you should expect the unexpected, which is why it is a good idea to ensure that you can handle common as well as less common troubleshooting scenarios.

## Tip 6
Make sure to master this topic very well. File system–related topics have a heavy weight in the RHCSA objectives, and it is likely that you will need to create partitions and/or logical volumes and put them in /etc/fstab for automatic mounting. That also makes it possible that something will go wrong, and if that happens on the exam, you’d better make sure that you know how to fix it!