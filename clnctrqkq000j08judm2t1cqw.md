---
title: "Linux User management"
datePublished: Thu Oct 05 2023 06:57:16 GMT+0000 (Coordinated Universal Time)
cuid: clnctrqkq000j08judm2t1cqw
slug: linux-user-management
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/kuJkUTxR0z4/upload/4f6dc9fc31c05b5d3ea8f45301901926.jpeg
tags: linux, linux-for-beginners, linux-basics

---

# **What is a Linux user account?**

A user account is a set of credentials that allow a user to log in to a Linux system and access its resources. Each user account has a unique username and password, as well as a set of permissions that determine what files and directories the user can access and modify.

# **Why is user management important?**

User management is important for several reasons:

* **Security:** It helps to protect the system from unauthorized access and malicious activity.
    
* **Resource management:** It allows system administrators to control how resources are allocated to users.
    
* **Collaboration:** It facilitates collaboration between users by allowing them to share files and directories.
    

# Types of users

There are three main types of users in Linux:

* **Root:** The root user is the superuser, with full administrative privileges over the system.
    
* **Regular users:** Regular users have limited access to the system, but can gain administrative privileges by using the sudo command.
    
* **Service users:** Service users are used by system services to run in the background. They typically have limited access to the system and cannot log in directly.
    

## **Root user**

The root user is the most powerful user on a Linux system. It has full access to all files and directories and can run any command. The root user is typically used for system administration tasks, such as installing and configuring software, managing users and groups, and troubleshooting problems.

## **Regular users**

Regular users are used by everyday users to access the system. They have limited access to files and directories, and cannot run certain commands that could damage the system. Regular users can gain administrative privileges by using the sudo command. The sudo command allows regular users to run commands as the root user.

## **Service users**

Service users are used by system services to run in the background. System services are programs that provide essential functionality for the system, such as networking, file sharing, and printing. Service users typically have limited access to the system and cannot log in directly.

# How to create, delete, and change users password

**To change your own password**, type the following command:

```haskell
passwd
```

You will be prompted to enter your current password and then your new password twice.

**To change the password of another user,** replace `<username>` with the name of the user whose password you want to change:

```haskell
sudo passwd <username>
```

**To create a new user account,** use `adduser` and replace `<username>` with the name of the new user account:

```haskell
sudo adduser <username>
```

You will be prompted to enter additional information about the new user account, such as a full name and a password.

**To delete a user account:**

1. Open a terminal window.
    
2. Type the following command, replacing `<username>` with the name of the user account you want to delete:
    

```haskell
sudo deluser <username>
```

**Important:** Be careful when changing or resetting user passwords. If you make a mistake, you could lock yourself or other users out of the system.

# User ID

There are standard user ID (UID) ranges for each type of user in Linux:

* **Root user:** UID 0
    
* **Regular users:** UIDs from 1000 onwards
    
* **Service users:** UIDs from 100 to 999
    

You can check UID it using `cat /etc/passwd | grep <username>`

```haskell
nguyenducchinh@VM:~$ cat /etc/passwd | grep nguyenducchinh
nguyenducchinh:x:1000:1000:Nguyen Duc Chinh,,,:/home/nguyenducchinh:/bin/bash
nguyenducchinh@VM:~$
```

# Understand /etc/passwd file

In the above example, you can see the different fields of `/etc/passwd`, separated by a colon.

```haskell
username:password:UID:GID:'Description',,,:Home_directory:shell
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696487020896/4deff32d-d4cc-454a-87f0-bf3d3ee551ab.png align="center")

The fields are:

* **Username:** The string that the user types when logging in. Usernames must be unique and can be up to 32 characters long.
    
* **Password:** In older Linux systems, the user's encrypted password was stored in the /etc/passwd file. However, newer systems store the password in the /etc/shadow file. The password field in /etc/passwd is typically set to the character `x`.
    
* **User ID (UID):** A unique number assigned to each user. The UID is used by the operating system to identify the user.
    
* **Group ID (GID):** The primary group of the user. The primary group is the group that the user belongs to by default.
    
* **GECOS (comment field):** A field that can contain additional information about the user, such as their full name, office phone number, and department.
    
* **Home directory:** The absolute path to the user's home directory. The home directory is the directory where the user's files are stored.
    
* **Shell:** The absolute path to the user's default shell. The shell is the program that the user uses to interact with the operating system. You can read about different shell in Linux [here in this blog post.](https://blog.nguyenducchinh.com/linux-find-your-way-around-the-terminal)
    

# Understand /etc/shadow file

The `/etc/shadow` file is a critical file in Linux systems that contains encrypted passwords for all user accounts. It is owned by the root user and only readable by the root user and the shadow group. It is used by the system to authenticate users and to prevent unauthorized access.

Let's see what's in it:

```haskell
nguyenducchinh@VM:~$ sudo cat /etc/shadow | grep nguyenducchinh
nguyenducchinh:$y$j9T$AESKh04Gs0cVsWhla1K1B/$Xx2PgDjaheI7VUviYM3OiT6k3loXAA2wGfhL6rzNe3.:19618:0:99999:7:::
nguyenducchinh@VM:~$
```

The `/etc/shadow` file is a text file that contains one entry per user account. Each entry is made up of nine colon-separated fields:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696488177587/ee33a692-52ee-4f05-9c12-adaed70f115e.png align="center")

* **Username:** The name of the user account.
    
* **Password hash:** The one-way transformation of the user's password.
    
* **Last password change:** The date on which the user's password was last changed, in days since January 1, 1970.
    
* **Minimum password age:** The minimum number of days that a user must keep their password before they can change it.
    
* **Maximum password age:** The maximum number of days that a user can keep their password before they must change it.
    
* **Password inactive days:** The number of days that a user's account will be locked if they do not change their password before the expiration date.
    
* **Account expiry date:** The date on which the user's account will expire.
    
* **Reserved field:** This field is currently unused.
    
* **Encrypted password salt:** This field is used to add randomness to the password hash.
    

The password hash field is the most important field in the `/etc/shadow` file. It is used to authenticate the user when they log in.

The other fields in the file are used to control password aging and account lockout policies. These policies can help to prevent unauthorized access to the system by making it more difficult for attackers to guess or steal user passwords.

The `/etc/shadow` file is owned by the root user and has permissions of 0600. This means that only the root user can read or write to the file. This helps to protect the file from unauthorized access.

# User group

A user group in Linux is a collection of users who share common access privileges. Each group has a unique Group ID (GID) that is used to identify the group to the system and determine the group's permissions and privileges.

User groups are important because they make it easier to manage access control. By assigning users to groups, you can grant them specific permissions and privileges without having to manually configure permissions for each individual user.

# **How to create, manage, and delete user groups in Linux**

To create a new user group in Linux, you can use the `groupadd` command. For example, to create a group called `developers`, you would run the following command:

```haskell
sudo groupadd developers
```

To add a user to a group, you can use the `usermod` command. For example, to add the user `nguyenducchinh` to the `developers` group, you would run the following command:

```haskell
sudo usermod -aG developers nguyenducchinh
```

To remove a user from a group, you can use the `gpasswd` command. For example, to remove the user `nguyenducchinh` from the `developers` group, you would run the following command:

```haskell
sudo gpasswd -d nguyenducchinh developers
```

To delete a user group, you can use the `groupdel` command. For example, to delete the `developers` group, you would run the following command:

```haskell
sudo groupdel developers
```

Here are some examples of common user groups in Linux:

* `admin`: The administrator group. Users in this group have full administrative privileges.
    
* `root`: The root user group. The root user is the most powerful user on the system and has unrestricted access to all files and resources.
    
* `sudo`: The sudo group. Users in this group can run commands with root privileges.
    
* `developer`: The developer group. Users in this group typically have access to development tools and resources.
    
* `qa`: The quality assurance group. Users in this group typically have access to testing tools and resources.
    
* `ops`: The operations group. Users in this group typically have access to production systems and resources.
    

You can also create your own custom user groups to meet the specific needs.

# Understand /etc/group file

he `/etc/group` file is a text file that contains information about the user groups on a Linux system. Each line in the file represents a single group, and the fields are separated by a colon.

```haskell
group_name:password:GID:user_list
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1696489672390/d131d616-b69b-4616-8294-7b78fc9cca7a.png align="center")

* **group\_name:** The name of the group.
    

* **password:** The group password. This is optional and is not typically used.
    
* **GID:** The group ID (GID). This is a unique number that identifies the group to the system.
    
* **user\_list:** A list of users who belong to the group, separated by commas.
    

For example, the following line in the `/etc/group` file represents the `admin` group:

```haskell
admin::0:root,wheel
```

This line indicates that the `admin` group has GID 0, and that the users `root` and `wheel` are members of the group.

# **Best practices for user management**

* **Use strong passwords:** Users should be required to use strong passwords that are difficult to guess.
    
* **Limit user permissions:** Users should only be given the permissions they need to perform their job duties.
    
* **Regularly audit user accounts:** System administrators should regularly audit user accounts to ensure that they are still necessary and that permissions are assigned appropriately.
    
* **Delete unused user accounts:** Unused user accounts should be deleted to reduce the attack surface of the system.
    

# **Conclusion**

User management is an important part of Linux security. By understanding how it works and how to protect it, you can help to keep your system and your data safe.