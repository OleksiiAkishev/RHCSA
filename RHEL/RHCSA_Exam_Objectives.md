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

## Archive

a.Create an archive 
b.List the contents of an archive 
c.Extract an archive 
d.Compress and uncompress archives

