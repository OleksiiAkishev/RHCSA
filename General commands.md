su switches the user to the specified account, while sudo executes a single command with elevated privileges without switching the user. sudo is considered as more secure command


# Seach for patterns in shell

Pattern	Meaning
j* in the shell	filename beginning with j
j* in grep	zero or more j characters
^j in grep	starts with j
j$ in grep	ends with j
.*j.* in grep	contains j anywhere

to search the file fith find
    find /var/log -maxdepth 1 -name 'j*' -printf '%f\n'
