# Below is a list of common terminal commands (mainly for Linux and macOS) and their functions, categorized for easier understanding:

## File and Directory Commands

1. ls - Lists files and directories in the current location.

  ls -l: Detailed list view.

  ls -a: Includes hidden files.



2. cd - Changes the current directory.

   Example: cd /home/user



3. pwd - Prints the current working directory.


4. mkdir - Creates a new directory.

Example: mkdir new_folder



5. rmdir - Removes an empty directory.

Example: rmdir empty_folder



6. rm - Removes files or directories.

rm file.txt: Deletes a file.

rm -r folder: Deletes a folder and its contents.



7. cp - Copies files or directories.

cp file1.txt file2.txt: Copies file1 to file2.

cp -r folder1 folder2: Copies folder1 to folder2.



8. mv - Moves or renames files or directories.

Example: mv oldname.txt newname.txt




## File Viewing and Editing

1. cat - Displays the content of a file.

Example: cat file.txt



2. less - Views a file page by page.

Example: less largefile.txt



3. nano - Opens a simple text editor.

Example: nano file.txt



4. vim - Opens a powerful text editor.

Example: vim file.txt



5. touch - Creates a new, empty file.

Example: touch newfile.txt




## Permissions and Ownership

1. chmod - Changes file or directory permissions.

Example: chmod 755 file.sh



2. chown - Changes ownership of a file or directory.

Example: chown user:group file.txt



3. sudo - Runs a command with administrator (root) privileges.

Example: sudo apt update




## Process and System Information

1. ps - Displays running processes.

Example: ps aux



2. top - Shows real-time system stats and running processes.


3. kill - Terminates a process.

Example: kill 1234 (where 1234 is the process ID).



4. df - Displays disk space usage.

Example: df -h



5. du - Shows the size of directories and files.

Example: du -sh folder



6. free - Displays memory usage.

Example: free -h



7. uname - Displays system information.

uname -a: Detailed info about the OS.




## Networking Commands

1. ping - Tests connectivity to another network device.

Example: ping google.com



2. ifconfig - Shows or configures network interfaces (deprecated, use ip command).


3. ip - Modern command to manage network interfaces.

Example: ip addr show



4. curl - Fetches data from a URL.

Example: curl https://example.com



5. wget - Downloads files from a URL.

Example: wget https://example.com/file.zip




## Package Management

1. apt - Manages software packages (Debian/Ubuntu).

Example: sudo apt install package_name



2. yum or dnf - Manages packages on Red Hat-based systems.

Example: sudo yum install package_name



3. pip - Installs Python packages.

Example: pip install requests



4. npm - Installs Node.js packages.

Example: npm install express




## Archiving and Compression

1. tar - Archives files.

Example: tar -cvf archive.tar folder



2. gzip - Compresses files.

Example: gzip file.txt



3. unzip - Extracts zip files.

Example: unzip file.zip




## Other Useful Commands

1. echo - Displays text or outputs to a file.

Example: echo "Hello, World!"



2. history - Shows the history of commands run in the terminal.


3. clear - Clears the terminal screen.


4. alias - Creates shortcuts for commands.

Example: alias ll="ls -l"



5. whoami - Displays the current user.


6. man - Displays the manual for a command.

Example: man ls




This is just a summary. Let me know if you want detailed examples for any of these commands!

