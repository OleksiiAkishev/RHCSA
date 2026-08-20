# Exam Objectives

## Chapter 1. Essential Tools
1) Searching the right man pages:
    man -k <key_word>
2) Update manpages:
    mandb
Task 1. Modify your shell environment so that on every subshell that is started, a variable is set. The name of the variable should be COLOR, and the value should be set to red. Verify that it is working.

Note:
 - Shell: main interactive command line interface
 - Subshell: a new child command line session created inside or by the main shell
 - .bashrc it is a file where environment configurations are stored and loaded on the startup
Solution:
a. check the if .bashrc exists in the home dir
    less ~/.bashrc
b. append to the end of the existing file, required command
    echo "export COLOR=red" >> ~/.bashrc  
Note: the export is important here, it give possibility to be available it in the child shells
c. test in the current shell
    echo $COLOR
d. test in the subshell
    bash -c 'echo $COLOR'

Task 2. Use the appropriate tools to find the command that you can use to change a user password. Do you need root permissions to use this command?

a. find command by keywords with man
    man -k "change user password"
b. identify the command
    passwd # works for current unprivileged user (No roor needed)
    passwd jack # fails for regular users --> requires root/sudo
It means that the user on the current session can update personal password (if login is set to correct directory and not to non login), but cannot chage the password if not root for other users.

Task 3. From your home directory, type the command ls -al wergihl * and ensure that errors as well as regular output are redirected to a file with the name /tmp/lsoutput.

Notes:
    File descriptors:
        - 1 = Standard Output (stdout)
        - 2 = Standard Error (stderr)
* standard output matches all the file from the used location

a. Navigate to home dir
    cd ~
b. Modern way to ouput errors and standard output to the required file
    ls -al wergihl * &> /tmp/lsoutput
c. Traditional way. Redirect stdout then redirect errors(stderr) to the same as stdout
    ls -al wergihl * > /tmp/lsoutput 2>&1
d. cat /tmp/lsoutput

## Chapter 2. File Management tools

Task 1. Log in as user student and use sudo -i to open a root shell. In the home directory of root, create one archive file that contains the contents of the /home directory and the /etc directory. Use the name /root/essentials.tar for the archive file.

Notes: 
    - Always place the target archive path immediately after the -f flag (e.g., tar -cvf /path/to/archive.tar /source1 /source2). Reversing the order is a common exam mistake that corrupts the syntax!

    - tar Flag Reference:
        c: Create a new archive.
        f: Specifies the archive filename (this flag must immediately precede the filename)
        v: Verbose mode (optional, shows files as they are added).

a. elevate to root:
    sudo -i
b. create archive containing both directories
    tar -cvf /root/essentials.tar /home /etc
c. list in human readable to see the size of the archive
    ls -lh /root

Task 2. Copy this archive to the /tmp directory. Also create a hard link to this file in the /directory.
Notes:
    Copying (cp): Duplicates the file to a new location. Changes to one copy will not affect the other.
    Hard Link (ln): Creates a direct pointer to the exact same physical data (inode) on disk.
        It acts like an additional name for the exact same file.
        Editing through /essentials.tar updates /tmp/essentials.tar instantly.
        Deleting one name does not delete the underlying data until all hard links pointing to it are removed.

a. copy archive to tmp
    cp /root/essentials.tar /tmp
b. Create a hard link in the / targeting the copy in /tmp
    ln  /tmp/essentials.tar /essentials.tar 
c. Verify the hard link (check that the count column - shows 2)
    ls -l /tmp/essentials.tar /essentials.tar
d. Check that both files share same inode number
    ls -i /tmp/essentials.tar /essentials.tar

Task 3. Rename the file /essentials.tar to /archive.tar. Create a symbolic link in the home directory of the user root that refers to /archive.tar. Use the name link.tar for the symbolic link.

Note: 
    - Renaming (mv): Moving a file to a new path in the same filesystem simply renames its link without moving the underlying data. Because /essentials.tar was a hard link to /tmp/essentials.tar, renaming it to /archive.tar keeps that hard link completely intact.
    - Symbolic Link (ln -s): Creates a "shortcut" or pointer to a target file path (like a Windows shortcut).
        Unlike a hard link, it points to the filename/path, not the underlying disk data (inode).
        If the target path is moved or deleted, the symlink breaks (turns red/blinking in terminal).

a. Rename /essentials.tar to /archive.tar
    mv /essentials.tar /archive.tar
b. Create the symbolic link in /root pointing to /archive.tar
    ln -s /archive.tar /root/link.tar
c. Verify the symbolic link
    ls -l /root/link.tar

Task 4. Remove the file /archive.tar and see what happens to the symbolic link. Remove the symbolic link also.

Note: Broken Symlink (Dangling Link): Since a symbolic link points to a path name (/archive.tar) rather than the underlying disk inode, deleting the target file breaks the link. The symlink file itself still exists on disk, but pointing to nothing.

a. Remove the target file
    rm-f /archive.tar
b. Check the symbolic link state
    ls -l /root/link.tar
c. Clean up the broken symbolic link
    rm -f /root/link.tar

Task 5. Compress the /root/essentials.tar file.

a. Compress the originla archive in /root
    gzip /root/essentials.tar
b. Verify the size of the archive after compressing
    ls -lh /root/essentials.tar.gz

## Chapter 3. Text files

Task 1. Describe two ways to show line 5 from the /etc/passwd file.

a. Method 1: using head and tail
    head -n 5 /etc/passwd | tail -n 1
b. Method 2: using sed (Stream Editor)
    sed -n '5p' /etc/passwd
c. Method 3: using awk
    awk 'NR==5' /etc/passwd


# Additional exam objectives --> try to search in the book for the key words as Exam objectives/tips

## Archive

a.Create an archive 
b.List the contents of an archive 
c.Extract an archive 
d.Compress and uncompress archives

