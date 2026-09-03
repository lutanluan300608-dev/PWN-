ssh bandit0@bandit.labs.overthewire.org -p 2220
# Lv0 -> 1
use cat to see the contents in the file  
```
cat readme
```
password: 6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR  
# Lv1 ->2
because the filename is "-" so i have to write "./" at the first.
```
cat ./-
```
password: PK8fYLZg2hnHSz83plBL1iEPKdD3QToB
# Lv2 -> 3
Because there are spaces in the file named "--spaces in this filename--", so i have to use a "\ " to replace the spaces, this will make Linux understand that those spaces are in the filename, not the regular spaces. 
```
cat -- --spaces\ in\ this\ filename--
```
password: 7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME

# Lv3 -> 4
The password for the next level is stored in the only human-readable file in the inhere directory.  
First, i cd to the inhere dir, then i use ls -la to see all the files, i found a hidden file and just read it to take the password.  
```
cd inhere
ls -la
cat ...Hiding-From-You
```
password: xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq  
# Lv4 -> 5
there are many files in inhere dir, i use "file ./*" to check the file format. and the password is in -file07
```
cd inhere
ls -la
file ./*
cat ./-file07
```
password: 6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG

# Lv5 ->6
There are too many dirs and files, so acording to the information i have been gave, i use "find" to find the exact file to read.
```
find . -type f ! -executable -size 1033c 
```
. means find all kind of files or dirs in current path  
-type f means the thing i am finding is file  
! means not  
-size means size of file
