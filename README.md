# RHCSA



## Inode
A structure that contains all properties about file except its name. Every Linux file has an inode. 

## Hard Link
A name associcated with inode. At initial stage each new file has except one hard link. One same file can have multiple hard links, in order to have it available from different places. File without a hard link, considered as deleted. 

## Symbolic link (soft link) 
It referes to the name of the file and not to the inode.

symbolic link --> hard link --> inode --> data blocks

to create a link use ln command. 

The link count belongs to the inode, not the filename; after ln newfile linkedfile, the same inode is referenced by two names (newfile and linkedfile), so both show a link count of 2.

### ls Common Command-Line Options
a) ls -l: long listing with the persmissions on the files. Not showing hidden files
b) ls -a: show all files, including hidden.
c) ls -lrt: sort the files as per modification date, the most recent at the bottom. 


### mv 
mv is used for moving files/dirs and renaming (note: renaming it is nothing else but copying and deleting original file)



### VIM Cheet Sheet
Esc
Switches from input mode to command mode. Press this key before typing any command.

i, a
Switches from command mode to input mode at (i) or after (a) the current cursor position.

o
Opens a new line below the current cursor position and goes to input mode.

:wq
Writes the current file and quits.

:q!
Quits the file without applying any changes. The ! forces the command to do its work. Add the ! only if you really know what you are doing.

:w filename
Writes the current file with a new filename.

dd
Deletes the current line and places the contents of the deleted line into memory.

yy
Copies the current line.

p
Pastes the contents that have been cut or copied into memory.

v
Enters visual mode, which allows you to select a block of text using the arrow keys. Use d to cut the selection or y to copy it.

u
Undoes the last command. Repeat as often as necessary.

Ctrl-R
Redoes the last undo. (Cannot be repeated more than once.)

gg
Goes to the first line in the document.

G
Goes to the last line in the document.

/text
Searches for text from the current cursor position forward.

?text
Searches for text from the current cursor position backward.

^
Goes to the first position in the current line.

$
Goes to the last position in the current line.

!ls
Adds the output of ls (or any other command) in the current file.

:%s/old/new/g
Replaces all occurrences of old with new.

## Archive
