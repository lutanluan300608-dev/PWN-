# ID
**id** -> id người tham gia  
**id root** -> id admin  
# LIST
**ls** -> list, show the contents of current dir  
**ls -R** -> list all of files and subdirectories of current dir  
**ls -a** -> list all file  
**ls -l** -> see the information of files: file permissions, owner, size, modification date  
**ls -al** -> same to the ls -l but list all of files
# CHANGE DIRECTORY
**cd** -> change directory, change to home  
**cd --** ->last accessed dir  
**cd ..** ->previous dir  
**cd /** -> root dir   
# TAB
**TAB** -> automatically complete the command  
**Double TAB** -> show list of all matching commands
# CREATE FILE AND DIR
**touch** -> create empty file    
**echo "Hello"**-> print  Hello  
**echo "Hello" > file.txt** -> chuyển output vào file.txt, nếu chưa có nó sẽ tạo file. Nên dùng >> để ko bị ghi đè nd      
**echo "Hello" > .hiddenfile** -> any file or directory name starts with dot(.) is considered hidden    
**mkdir** -> make directory  
**mkdir dad/son** -> create subdir named son of dir dad if dir dad exists
# COPY
**cp** -> create a copy a file  
**cp -r** -> copy directories recursively  
**cp -a** 
# REMOVE
**mv** -> move files to directory or rename files  
**rm** -> remove, delete file   
**rm -i** -> confirm   
**rmdir** -> remove empty directory  
**rm -r** -> remove directory and everything inside  
# READ FILE
**cat** -> read the file and output ALL the contents to the terminal  
**less** -> read the file, can move up and down and can quit by pressing q
**head** -> read 10 first lines of the file  
**head -n x** -> read x first lines  
**tail** -> same to the **head command** but read up to from the end  
**tail -f** -> can use to read the file log   
