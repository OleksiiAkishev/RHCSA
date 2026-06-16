# RHCSA

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

# Archive

-   Create archive: tar -cf archivename.tar /files-you-want-to-archive
-   Add existing file to arhive: tar -rvf <archive_destination> <source_of_what_to_add>
-   List archive content: tar -tvf <archive>
-   Extract a content from archive tar -xvf <archive>; 
    note: either use cd before to go to the destination repo, either use -C option,
        e.g., tar -xrf <archive> -C <detination_path>
-   Extract one file from archive: tar -xvf <archive> <file-name_you_want_to_extract>
    Example: tar -xvf my_archive.tar ./DevOps/RHCSA/.README.md.swp
    Note: by default ./DevOps/RHCSA/.README.md.swp it is path where tar is looking for the file in archive + it is the path where the file will be extracted.
    To be able to extract the file to the current localtion, use --strip-components, e.g., tar -xvf my_archive.tar --strip-components=3  ./DevOps/RHCSA/.README.md.swp
        This will find ./DevOps/RHCSA/.README.md.swp and then it will put it under ./.README.md.swp (current location with the same file name) 
-   To compress archive file use a gzip command: gzip <file_name> <destination>; note tar did do it when the archive was initially created. 
-   To uncompress: gunzip <file_name>
