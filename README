
===========================
dedup: a deduplication tool
===========================

Find and delete duplicated files.

Use cases
---------

Find duplicates in a specific folder and create a script to delete them:

    dedup find -o byedups.sh /your/folder

Find duplicated mp3 in a folder (and subfolders) and create a script to delete them:

    dedup find --include *.mp3 -r -o byedups.sh /your/folder

Find duplicates except jpg in two folders and create a script to delete them:

    dedup find --exclude *.jpg -o byedups.sh /your/folder /your/other/folder


How does it work?
-----------------

Finding duplicates is a two steps process:

    1. Indexing: The full path of each file is stored in a database together
       with a hash of the file. Currently the following hashes are implemented:
        a. size: size of the file in bytes.
        b. md5partial: md5 hex digest of the first 8Kb of the file.
        c. md5: md5 hex diggest of the file.
 
       Hashing allows narrowing down the list of potetial duplicates. 
       While is very likely that two different files have the same size, it is 
       unlikely that they have the same hash (`birthday problem`_). 

       .. birthday problem : http://en.wikipedia.org/wiki/Birthday_problem

    2. Comparing: the potential duplicates (i.e. files with the same hash) are 
       compared to detect true duplicates.

Which is the right hash method depends on the number of files to check, their 
size and the expected number of duplicates. Remember that is necessary to read
the complete file to generate a full hash and therefore it is time consuming to 
do it for files in which the first 8k are already different.

The default choice is md5partial which provides a good tradeoff between speed 
and reliability.


Why do you generate a script instead of directly deleting the files?
--------------------------------------------------------------------

Is good to have a way to check (and double-check) before deleting lots of files,
and being dedup a command line tool generating a script which you can open, edit
and check was the simplest and yet most powerful.

In addition this tools was concieved to delete duplicates across computers. 
Generating scripts allows you to do this easily.


Deduplicating in different computers
------------------------------------

First create an index for each computer that you want to deduplicate::

    # In computer 1
    dedup index -o c1.sqlite -r /your/folder

    # In computer 2
    dedup index -o c2.sqlite -r /your/other/folder

Then transfer the two databases (c1.sqlite and c2.sqlite) to a single computer
and generate the script::

    dedup script --pot c1.sqlite c2.sqlite

You will see two files (c1.sqlite.sh and c2.sqlite.sh, extensions are .bat if 
you are in a Windows computer). ** WARNING **: As the files are in different
computer they cannot be compared. Therefore the script show the potential 
duplicates acording to md5partial hash.

You can get less false positives by doing a full hashing but this might be 
* VERY * time consuming if you have a lot of large files. An alternative is to 
hash by md5partial, remove singles (those which we are sure are not duplicated), 
and rehash the rest::

    # In computer 1
    dedup index -o c1.sqlite -r /your/folder

    # In computer 2
    dedup index -o c2.sqlite -r /your/other/folder  
    
    # Bring the two files to the same computer
    dedup nosingle c1.sqlite c2.sqlite  
    # Copy back the files to the originating computer

    # In computer 1
    dedup index -o refined1.sqlite --by-md5 --db c1.sqlite

    # In computer 2
    dedup index -o refined2.sqlite --by-md5 --db c2.sqlite

    # Bring the two refined files to the same computer
    dedup script --pot refined1.sqlite refined2.sqlite  

Hopefully there will be now only a few potential duplicates which you can 
transfer from computer to the other and do a real comparison (or you can take
your chances and delete them!).


From a group of duplicates, which file is kept?
-----------------------------------------------

The first file appearing in the index is kept. You may force from which folder
are these files by specifiying it first in the list of folders::

    dedup find -o byedups.sh /your/main/folder /your/other/folder

If you are deduplicating from multiple computers, put first in the list the 
index generated in the computer where you want to keep the files.


Other commands
--------------

You can inspect a index database file with the following commands::

    - list: Tab separated list the content of an index.
    - info: Display indexing method and duplication statistics.
    - doc: Print this help


