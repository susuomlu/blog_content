---
title: "[Automation] Automate your terminal with Tera Term Macro"
datePublished: Thu Aug 31 2023 04:46:13 GMT+0000 (Coordinated Universal Time)
cuid: cllyooe43000y09mj6ylgfdho
slug: automation-automate-your-terminal-with-tera-term-macro
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/p1m4B-lhS9Y/upload/5290ab9103a7552978515b48a0081d4c.jpeg
tags: automation, networking, teraterm

---

# What are Tera Term and Teraterm Macro?

Tera Term is a terminal emulator software that is free and open-source. It supports Serial, TCP/IP, and named pipe connections. Tera Term Macro is a scripting language that comes with Tera Term.

It allows you to automate various tasks that you can do with a terminal, such as auto-login, auto-logging, auto-configuration, monitoring, and more.

Tera Term language (TTL) is the language used for writing macros. You can find the documentation at [MACRO Help Index](https://ttssh2.osdn.jp/manual/4/en/macro/). You can use any text editor to create your macros.

In this article, I will show you how to use TTL to automate anything that needs a terminal input.

# Connect to a Remote host

To begin working with TTL, you have to connect to a remote host first.

To connect to a serial port, you can:

```markdown
;Set the COM5 port
connect '/C=5'
```

Here is an example of how to SSH to a host using the password method:

```markdown
connect 'myserver /ssh /auth=password /user=username /passwd=password'
```

This code snippet connects to a remote host using SSH connection (port 22). You can replace `myserver` with the IP address or hostname of the remote host.

* The `/auth=password` parameter is used to indicate using the password method.
    
* The `/user=username` parameter is used to specify the username.
    
* The `/passwd=password` parameter is used to specify the password.
    

Here is an example of how to connect to SSH using public key method:

```markdown
connect 'myserver /ssh /auth=publickey /user=username /keyfile=private-key-file'
```

The `/auth=publickey` parameter is used to indicate that we are using public key authentication.

* The `/user=username` parameter is used to specify the username.
    
* The `/keyfile=private-key-file` parameter is used to specify the private key file.
    

You can find more information about other options for connecting to SSH using Tera Term macro in the [Tera Term documentation](https://ttssh2.osdn.jp/manual/4/en/usage/ssh.html).

After connecting to the remote host, you can then execute your automation script.

# Variables

There are two types of variables:

* Strings (limited to 255 characters)
    
* Integers (Limited to ~ +/-2 billion)
    

To create a variable in TTL, you can use the following syntax:

```haskell
set <variable_name> = <value>
```

For example, to create a variable named my\_var and assign it the value 123, you can use the following command:

```haskell
set my_var=123
```

You can then use this variable in your macro by enclosing it in `%` symbols. For example, to display the value of `my_var`, you can use the following command:

```haskell
messagebox "%my_var%"
```

This will display a message box with the value of my\_var (which is 123 in this case).

# **User input**

To get user input in Tera Term Macro, you can use the `input` command. Here is an example of how to use the `input` command:

```haskell
input 'Enter your name: ' Name
print 'Hello, ' + Name + '!'
```

This code snippet prompts the user to enter their name and stores the input in the variable `Name`. Then it outputs the text "Hello, " followed by the value of `Name` and the exclamation mark to the terminal.

# Special Statements

Tera Term Macro has many special statements that you can use to control Tera Term and automate your tasks.

Some of the special statements include `wait`, `sendln`, `connect`, `input`, `print`, `if`, `goto`, `label`, and more. You can find more information about these special statements in the [Tera Term documentation](https://ttssh2.osdn.jp/manual/4/en/).

I will introduce you to some of the most commonly used special statements and how to use them.

## wait and sendln

Some of the unique features of Tera Term are `wait` and `send/sendln` .

The `wait` statement reads through all of the terminal output and when one of the strings is found, it sets the result to the index of that string and moves on.

This can be useful to wait for a password prompt or wait for the result of the command.

A nice addition to the `wait` command is the `sendln` and `send` commands. These do what their names suggest: they write out commands to the terminal (with `sendln` adding a new line at the end).

For example, I can log in to my network device and then execute some commands without entering my password.

```haskell
;Set the COM5 port
connect '/C=5'

;Set the username and password's prompt and value
UsernamePrompt = 'login:'
PasswordPrompt = 'Password:'

;Set credentials value
Username ='admin'
Password = 'mypasword'

;Set the command to execute after
Command = 'show interface description'

;Wait for the UsernamePrompt
wait UsernamePrompt
;input Username here if UsernamePrompt is received
sendln Username

;Wait for the PasswordPrompt
wait PasswordPrompt
;input Password here if PasswordPrompt is received
sendln Password
```

This code snippet waits for a certain word and if the word is found then execute a command. The `sendln` command sends characters followed by a new-line character to the host.

With these two statements, you can send in a command, wait for the result, then execute another command. You can do pretty much anything that usually requires manual input from the terminal.

## strconcat and sprintf2

`strconcat` is a command in Tera Term macro that appends a copy of a string to the end of a string variable. For example:

```haskell
filename = "C:\\teraterm\\"
strconcat filename 'test.txt'
```

In the beginning, `filename` with the value `C:\\teraterm\\`, you can append `test.txt` to it using the `strconcat` command. The resulting value of `filename` would be `C:\\teraterm\\test.txt`

`sprintf2` is another command in Tera Term macro that returns formatted output. It works similarly to the `sprintf` command but with more options. The output string is stored in the string variable. For example:

```haskell
sprintf2 var \"%s/ USER NAME:%s\" '192.168.1.1' 'test user'
```

The above command formats the `var` to `192.168.1.1/ USER NAME:test user` by replacing the `%s` with the strings after it. This works kind of the same with popular programming languages like C or Powershell.

## Conditional statements

TTL supports conditional statements such as `if`, `then`, `elseif`, `else`, `endif`.

Here is how you can create a `if/else` statement in TTL:

```haskell
if <expression> then
  <statement>
else
  <statement>
elseif
  <statement>
endif
```

If the condition is true, the following `if` commands are executed. Otherwise, the following the `else` commands are executed.

## Loop statements

To use loop statements in Tera Term Macro, you can use the `do`, `while`, `until`, `for`, and `next` commands

### while

Here is how you can create a `while` statement in TTL:

```haskell
while <condition>
    <command>
endwhile
```

The following the while keyword is executed repeatedly as long as the condition is true.

### goto

To use `goto` in Tera Term Macro, you need to define a label using the `label` command.

Here is an example of how to use `goto` in Tera Term Macro:

```haskell
label start
input 'Enter your age: ' Age
if Age= ''
   goto start
endif
```

This code snippet defines a label called start. It prompts the user to enter their name and stores the input in the variable Age.

If the value of Age is empty, it will jump back to the label start.

### for and next

They repeat commands between `for` and `next` until the integer variable has the value at the `next` statement. After each loop the integer variable increases by 1.

Here is an example:

```haskell
for i = 1 to 10
  send "Hello World"
next i
```

### do and loop

One of the more difficult statements to understand is `do` and `loop` . They repeat the commands between them according to the conditions as follows:

* If `while` is specified, repeats while is non-zero.
    
* If `until` is specified, repeats while is zero.
    

Here is an example:

```haskell
; Send clipboard content to terminal
offset = 0
do
   clipb2var buff offset
if buff > 0
   send buff
offset = offset + 1
loop while result = 2
```

# Save the log

The `logopen` statement causes Tera Term to start logging and `logclose` statement tells it to stop. Anything in your terminal will be saved in the location you specified.

Here is an example of a macro that logs data received from the host:

```haskell
;Start logging
logopen "C:\log.txt" 0 1

;Wait for data from the host
wait ">"
sendln "show version"

;Wait for the command output

wait "show version"
wait ">"

; Stop logging
logclose
```

This macro logs the output of the “show version” command to a file named “log.txt” in the root directory of drive C. The logopen command starts logging and the logclose command stops logging.

# Conclusion

As a network engineer myself, working with a command line interface often requires the same set of commands to be executed many times.

The same is said for many technical-related work such as coder or tester.

Being able to sweep a set of commands to a terminal automatically is a great time-saver.

In this article, I introduced you to Tera Term Macro, a powerful language that can accelerate your workflow immensely.