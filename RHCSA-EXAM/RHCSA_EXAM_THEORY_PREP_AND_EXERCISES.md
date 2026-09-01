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
        fstab (filesystem table) is a configuration file that tells Linux about filesystems that should be mounted. And lies under /etc/fstab, it is a basic instructions list. 

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
        subscription-namger register
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

### 

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

