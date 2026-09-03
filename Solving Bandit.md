ssh bandit0@bandit.labs.overthewire.org -p 2220
# Lv1
use cat to see the contents in the file  
```
cat readme
```
password: 6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR  
# Lv2
```
cat ./-
```
password: PK8fYLZg2hnHSz83plBL1iEPKdD3QToB
# Lv3
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
