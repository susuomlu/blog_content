---
title: "Find your way around the Linux terminal"
datePublished: Thu Sep 14 2023 09:17:45 GMT+0000 (Coordinated Universal Time)
cuid: clmiyjhu2000809mmaw7hf1y6
slug: find-your-way-around-the-linux-terminal
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/TVxYoWzqdjs/upload/74cebf557408f11b8664f9c0ed624117.jpeg
tags: linux, linux-for-beginners, linux-basics

---

# **What is the Linux command line?**

The Linux command line is a text-based interface that allows you to interact with the operating system. It is a lot like the DOS prompt in Windows, but it is much more powerful.

There are several different shells on Linux, these are just a few popular ones:

* Bourne-again shell (Bash)
    
* C shell (csh or tcsh, the enhanced csh)
    
* Korn shell (ksh)
    
* Z shell (zsh)
    

On Linux, the most common one is the Bash shell. We will mainly focus on Bash in this series.

To access the Linux command line, open a terminal window. On most Linux distributions, you can do this by pressing Ctrl+Alt+T shortcut.

Once you are in the terminal window, you can start typing commands.

# Root and non-root shell

The main difference between root and non-root shells is the level of privileges they have. The root shell has full access to the system, while the non-root shell has limited access.

The easiest way to tell if you are in a root shell or a non-root shell is to look at the prompt. The prompt for a root shell starts with a number sign (`#`), while the prompt for a non-root shell starts with a dollar sign (`$`).

On Ubuntu or Debian GNU/Linux, the prompt for a regular user will likely look like this:

```bash
nguyenducchinh@VM:~$
```

If you are logged in as root, your prompt will look like this:

```bash
root@VM:~#
```

You can also use the `whoami` command to check your current user ID.

```bash
nguyenducchinh@VM:~$ whoami
nguyenducchinh
```

If you are logged in as root, the output of the `whoami` command will be `root`.

```bash
root@VM:~# whoami
root
```

It is important to note that the root shell can be dangerous if used incorrectly. For this reason, it is important to only use the root shell when necessary and to take precautions to avoid making mistakes.

# Structure of a Linux command

The structure of a Linux command is as follows:

```bash
~$ command [options] [arguments]
```

* **Command:** The command is the name of the program or function that you want to run.
    
* **Options:** Options are modifiers that can be used to change the behavior of the command.
    
* **Arguments:** Arguments are the data that the command needs to work.
    

In the below case, `filename` is the argument needed to specify which file you will delete.

```bash
~$ rm filename
```

Some commands can accept multiple arguments.

```bash
~$ rm filename1 filename2 filename3
```

Here are some acceptable options you can add for `rm`:

* **\-i** prompts system confirmation before deleting a file.
    
* **\-f** allows the system to remove without a confirmation.
    
* **\-r** deletes files and directories recursively.
    

Options can be accessed in a short and a long form. For example, `-l` is identical to `--format=long` and `-a` is the same as `--all`.

Multiple options can be combined as well and for the short form, the letters can usually be typed together. For example, the following commands all do the same:

```bash
~$ ls -la 
~$ ls -l -a
~$ ls --format=long --all
```

# Variables

In Linux shell, a variable is a named location in memory that can be used to store data. Variables can be used to store text, numbers, or other types of data.

## Local Variables

To declare a local variable you use the following syntax:

```bash
~$ variable_name=value
```

For example, the following command declares a variable called `my_variable` and assigns it the value `123`:

```bash
nguyenducchinh@VM:~$ my_variable=123
```

Once a variable has been declared, you can use it in commands by preceding its name with a dollar sign ($). You can display any variable using the `echo` command.

```bash
nguyenducchinh@VM:~$ echo $my_variable
123
```

To remove a variable, use the command `unset`:

```bash
clayton@VM:~$ unset my_variable
clayton@VM:~$ echo $my_variable

clayton@VM:~$
```

The problem is, that when you open another terminal window, this variable is not accessible.

Local variables only work in the instance they were declared. If want to access it across the shell environment, you need to turn it into a global variable.

## Global Variables

Turning local to global variables is done by the command `export`. When it is invoked with the variable name, this variable is added to the shell’s environment:

```bash
clayton@VM:~$ greeting=hello
clayton@VM:~$ export greeting
```

## The `PATH` Variable

The `PATH` variable is a colon-separated list of directories that tells the shell where to look for executable programs. When you type a command in the shell, the shell searches the directories in the PATH variable for an executable file with the same name.

```bash
clayton@VM:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/snap/bin
clayton@VM:~$
```

Let's say you have an executable file called `my_script.sh` in your `/home` directory. By default, the shell will not be able to find this file, because it is not in one of the directories in the PATH variable.

To make the shell find this file, you can add the home directory to the PATH variable. You can do this by editing your shell configuration file, typically `~/.bashrc` or `~/.profile`. In the file, add the following line:

```bash
PATH=$HOME/bin:$PATH
```

This will tell the shell to look for executable files in the home directory, in addition to the directories that are already in the PATH variable.

If you don't want to edit can also set the `PATH` variable temporarily by using the `export` command.

Now, you can run the `my_script.sh` file by just typing the following command:

```bash
clayton@VM:~$ my_script.sh
```

The shell will find the file in the home directory and execute it.

By setting the `PATH` variable to include the directories where your executable files are located, you can avoid having to type the full path to the file every time you want to run it.

# **M**ost used **Linux commands**

Here are some of the top Linux commands frequently used by developers and system administrators:

### 1\. `ls` – List directory contents

* Displays files and directories in the current directory.
    

```bash
$ ls -l
total 32
drwxr-xr-x 2 user user 4096 Sep 22 10:00 Documents
drwxr-xr-x 3 user user 4096 Sep 21 09:00 Downloads
-rw-r--r-- 1 user user  614 Sep 22 11:00 example.txt
```

### 2\. `cd` – Change directory

* Used to navigate between directories.
    

```bash
$ cd Documents
```

### 3\. `pwd` – Print working directory

* Displays the current directory you are in.
    

```bash
$ pwd
/home/user/Documents
```

### 4\. `cp` – Copy files or directories

* Copies files from one location to another.
    
* Example: `cp file.txt /path/to/destination`
    

```bash
$ cp example.txt backup.txt
```

### 5\. `mv` – Move or rename files

* Moves or renames files or directories.
    

```bash
$ mv example.txt example_old.txt
```

### 6\. `rm` – Remove files or directories

* Deletes files or directories.
    
* Example: `rm file.txt` (use `rm -r` for directories)
    

```bash
$ rm example_old.txt
```

### 7\. `touch` – Create an empty file

* Creates an empty file or updates the timestamp of an existing file.
    

```bash
$ touch newfile.txt
```

### 8\. `mkdir` – Make a directory

* Creates a new directory.
    

```bash
$ mkdir newfolder
```

### 9\. `rmdir` – Remove directory

* Deletes an empty directory.
    

```bash
$ rmdir newfolder
```

### 10\. `cat` – Concatenate and display file content

* Displays the content of a file.
    

```bash
$ cat example.txt
This is an example file.
```

### 11\. `grep` – Search text using patterns

* Searches for a pattern in files.
    
* Example: `grep "search_text" file.txt`
    

```bash
$ grep "example" example.txt
This is an example file.
```

### 12\. `find` – Search for files or directories

* Searches for files based on name, type, or other attributes.
    

```bash
$ find /home/user -name "*.txt"
/home/user/Documents/example.txt
```

### 13\. `chmod` – Change file permissions

* Modifies the permissions of a file or directory.
    

```bash
$ chmod 755 script.sh
```

### 14\. `chown` – Change file owner and group

* Changes the owner or group of a file.
    

```bash
$ chown user:group file.txt
```

### 15\. `top` – Display active processes

* Shows real-time system processes, CPU, and memory usage.
    

```bash
$ top
```

(You’ll see an interactive display of processes.)

### 16\. `ps` – Report process status

* Displays currently running processes.
    

```bash
$ ps aux
```

* Output:
    

```sql
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user      1234  0.1  1.2 123456 12345 ?        Ssl  10:00   0:01 /usr/bin/python3
```

### 17\. `kill` – Terminate a process

* Kills processes by process ID.
    

```bash
$ kill 1234
```

### 18\. `df` – Disk space usage

* Shows available disk space on file systems.
    

```bash
$ df -h
```

* Output:
    

```sql
system      Size  Used Avail Use% Mounted on
/dev/sda1       100G   60G   40G  60% /
```

### 19\. `du` – Disk usage

* Shows the size of directories and files.
    

```bash
$ du -sh *
```

* Output:
    

```sql
5.0M    Documents
10M     Downloads
```

### 20\. `tar` – Archive files

* Archives and compresses files.
    

```bash
$ tar -czvf archive.tar.gz /path/to/folder
```

### 21\. `wget` – Download files from the web

* Downloads files from a URL.
    

```bash
$ wget https://example.com/file.zip
```

### 22\. `curl` – Transfer data from or to a server

* Used to make network requests and download files.
    

```bash
$ curl https://example.com
```

### 23\. `man` – Manual pages for commands

* Displays the manual for a command.
    

```bash
$ man ls
```

### 24\. `echo` – Display a line of text

* Outputs text or variables.
    

```bash
$ echo "Hello World"
Hello World
```

### 25\. `sudo` – Execute commands with superuser privileges

* Runs commands as the root user or another user.
    

```bash
$ sudo apt update
```

# **Conclusion**

These are just the essentials of Linux command line that you should know. With these, you will be able to do basic file management, navigate the file system, and run programs.