The Linux Operating System can be found in a lot of places. While it might not be as easy to use as Windows or macOS, it has its own set of advantages that make its use widespread. It is found in the Web servers you interact with, in your smartphone, and maybe, even in the entertainment unit of your car. One of the reasons for this versatility is that Linux is an open-source Operating System with many different flavors. It is also very lightweight and can run on very low resources.
### <span style="color:rgb(255, 192, 0)">Linux Distros</span>
![[Pasted image 20241218100122.png]]
- Ubuntu
- Red-hat
- Arch Linux
- Open SUSE
- Linux Mint
- CentOS
- Debian

### <span style="color:rgb(255, 192, 0)">OS and account information</span>
For a Linux system, everything is stored in a file. Therefore, to identify forensic artifacts, we will need to know the locations of these files and how to read them.

![[Pasted image 20241218124905.png]]
##### /bin: 
Binaries (executable files of many basic shell commands cd, ls, etc)
##### /dev: 
Device file (Special files related to devices, files virtual not physical)
##### /etc: 
Configuration files (core configuration files, use primarily by sys admins and services such as password files and network files)
if you need to make any changes in system config (for example hostname) the etc. folder has this files.
##### /usr: 
the executable files, libraries, source of most of the system programs. For this reason, most of the files contained therein is read­only (for the normal user)
- ‘/usr/bin’ contains basic user commands
- ‘/usr/sbin’ contains additional commands for the administrator
- ‘/usr/lib’ contains the system libraries
- ‘/usr/share’ contains documentation or common to all libraries, for example ‘/usr/share/man’ contains the text of the manpage
##### /home: 
User personal data

##### /lib – Shared libraries
Libraries are basically codes that can be used by the executable binaries. The /lib directory holds the libraries needed by the binaries in /bin and /sbin directories.

##### /sbin – System binaries
This is similar to the /bin directory. The only difference is that is contains the binaries that can only be run by root or a sudo user. You can think of the ‘s’ in ‘sbin’ as super or sudo.

##### /tmp – Temporary files
As the name suggests, this directory holds temporary files. Many applications use this directory to store temporary files. Even you can use directory to store temporary files.

##### /var – Variable data files
Var, short for variable, is where programs store runtime information like system logging, user tracking, caches, and other files that system programs create and manage.

##### /boot – Boot files
The ‘/boot’ directory contains the files of the kernel and boot image, in addition to LILO and Grub. It is often advisable that the directory resides in a partition at the beginning of the disc.

##### /proc – Process and kernel files
The ‘/proc’ directory contains the information about currently running processes and kernel parameters. The content of the proc directory is used by a number of tools to get runtime system information.

##### /opt – Optional software
Traditionally, the /opt directory is used for installing/storing the files of third-party applications that are not available from the distribution’s repository.

##### /root – The home directory of the root
There is /root directory as well and it works as the home directory of the root user. So instead of /home/root, the home of root is located at /root. Do not confuse it with the root directory (/).

##### /media – Mount point for removable media
When you connect a removable media such as USB disk, SD card or DVD, a directory is automatically created under the /media directory for them. You can access the content of the removable media from this directory.

##### /mnt – Mount directory
This is similar to the /media directory but instead of automatically mounting the removable media, mnt is used by system administrators to manually mount a filesystem.

##### /srv – Service data
The /srv directory contains data for services provided by the system. For example, if you run a HTTP server, it’s a good practice to store the website data in the /srv directory.

____
#### OS release information
To find the OS release information, we can use the `cat` utility to read the file located at `/etc/os-release`

```bash
cat /etc/os-release
```

#### User accounts
The `/etc/passwd` file contains information about the user accounts that exist on a Linux system.

We can use the `cat` utility to read this file
The output contains 7 colon-separated fields, describing username, password information, user id (uid), group id (gid), description, home directory information, and the default shell that executes when the user logs in.

```bash
cat /etc/passwd| column -t -s :
```

its password information field shows `x`, which signifies that the password information is stored in the `/etc/shadow`

![[Pasted image 20241218103205.png]]

Command Output:
- **Username**: This is the name of the user account used to log in, such as `root` or `ubuntu`.

- **Password Placeholder**: The letter `x` indicates that the actual password is stored securely in `/etc/shadow`.

- **User ID (UID)**: A unique numerical identifier assigned to the user, with `0` reserved for the root account.

- **Group ID (GID)**: A unique numerical identifier for the user’s primary group, determining group-level permissions.

- **User Description (GECOS field)**: A description or full name of the user, often used for administrative purposes.

- **Home Directory**: The directory where the user’s files, settings, and personal data are stored.

- **Login Shell**: The program or shell that runs when the user logs in, such as `/bin/bash` for an interactive session.

#### Group Information
The `/etc/group` file contains information about the different user groups present on the host. It can be read using the cat utility. 

```bash
cat /etc/group
```

#### Sudoers List
A Linux host allows only those users to elevate privileges to `sudo`, which are present in the Sudoers list. This list is stored in the file `/etc/sudoers`

![[Pasted image 20241218105046.png]]

```bash
sudo cat /etc/sudoers
```

The `/etc/sudoers` file contains **both users/groups and policies** to manage who can use `sudo` and what they can do with elevated privileges.

- **`Defaults env_reset`**: Resets the environment variables for security when using `sudo`.

- **`Defaults mail_badpass`**: Sends an email to the administrator if an incorrect password is entered for `sudo`.

- **`Defaults secure_path="..."`**: Defines the secure path for `sudo` commands, ensuring users can only execute binaries from trusted directories.

- **`root ALL=(ALL:ALL) ALL`**: Grants the `root` user full privilege to execute any command as any user or group on any host.

- **`%admin ALL=(ALL) ALL`**: Allows all users in the `admin` group to execute any command with `sudo`.

- **`%sudo ALL=(ALL:ALL) ALL`**: Allows all users in the `sudo` group to run any command with `sudo` as any user or group.

- **`#includedir /etc/sudoers.d`**: Includes additional configuration files located in the `/etc/sudoers.d` directory for better modular management.

```bash
sudo cat /etc/sudoers.d
```

This Directory Contains:

##### kali-grant-root

![[Pasted image 20241218105721.png]]

A configuration file specific to Kali Linux that manages root access permissions for users.

- `%kali` indicates the **kali group**, granting its members `sudo` privileges without requiring a password.
- **`ALL=(ALL)`**: Grants permission to run commands as any user.
- **`NOPASSWD`**: Allows the command to run without requiring a password.
- This ensures smooth usability in Kali Linux for penetration testing while avoiding constant password prompts.

##### ospd-openvas

![[Pasted image 20241218110048.png]]

This file configures `sudo` privileges for the **`ospd-openvas`** service, which runs the OpenVAS vulnerability scanner.

- **`ospd-openvas`**: The user or service account running the OpenVAS scanner.
- **`ALL=(ALL)`**: Grants permission to run commands as any user.
- **`NOPASSWD`**: Allows the command to run without requiring a password.
- **`/usr/sbin/openvas`**: Limits `sudo` access to only the specified command (in this case, OpenVAS-related).

In the **`ospd-openvas`** file under `/etc/sudoers.d`, elevated access is typically limited to **OpenVAS-related commands** that are essential for the proper functioning of the OpenVAS scanner.

#### Login information

![[Pasted image 20241218112241.png]]

In the /var/log directory, we can find log files of all kinds including `wtmp` and `btmp`. 
The `btmp` file saves information about failed logins, while the `wtmp` keeps historical data of logins. 
These files are not regular text files that can be read using `cat`, `less` or `vim`; instead, they are binary files, which have to be read using the `last` utility

```bash
sudo last -f /var/log/wtmp
```

do the same as last
```bash
utmpdump /var/log/wtmp
```

display who logged in now
```bash
who -a
```

##### Authentication logs

Every user that authenticates on a Linux host is logged in the auth log. The auth log is a file placed in the location `/var/log/auth.log`

```bash
cat /var/log/auth.log |tail
```

if doesn't exist use
```bash
journalctl _COMM=sudo
```

for ssh use
```bash
journalctl _COMM=sshd
```

To view all authentication-related events:
```bash
journalctl | grep "authentication"
```

____
# System Configuration

Once we have identified the OS and account information, we can start looking into the system configuration of the host.

### Hostname
The hostname is stored in the `/etc/hostname` file on a Linux Host. It can be accessed using the `cat` utility.

```bash
cat /etc/hostname
```

### TimeZone

Time-Zone information is a significant piece of information that gives an indicator of the general location of the device or the time window it might be used in. 
Time-Zone information can be found at the location `/etc/timezone` and it can be read using the `cat` utility.

```bash
cat /etc/timezone
```

### Network Configuration

To find information about the network interfaces, we can `cat` the `/etc/network/interfaces` file. The output on your machine might be different from the one shown here, depending on your configuration.

```bash
cat /etc/network/interfaces
```

Similarly, to find information about the MAC and IP addresses of the different interfaces, we can use the `ip` utility. To learn more about the `ip` utility, we can see its `man` page.

`man ip`

The below terminal shows the usage of the `ip` utility.

```bash
ip address show
```

```bash
ifconfig
```
### Active network connections

On a live system, knowing the active network connections provides additional context to the investigation. We can use the `netstat` utility to find active network connections on a Linux host. We can learn more about the `netstat` utility by reading its `man` page.

`man netstat`

The below terminal shows the usage of the `netstat` utility.

```bash
netstat -natp
```

### Running processes

If performing forensics on a live system, it is helpful to check the running processes. The `ps` utility shows details about the running processes. To find out about the `ps` utility, we can use the `man` page.

`man ps`

```bash
ps aux
```

### DNS Information

The file `/etc/hosts` contains the configuration for the DNS name assignment. We can use the `cat` utility to read the hosts file. To learn more about the hosts file, we can use the `man` page.

`man hosts`

```bash
cat /etc/hosts
```

The information about DNS servers that a Linux host talks to for DNS resolution is stored in the resolv.conf file. Its location is `/etc/resolv.conf`
We can use the `cat` utility to read this file.

```bash
cat /etc/resolv.conf
```

____
# Persistence Mechanisms

Knowing the environment we are investigating, we can then move on to finding out what persistence mechanisms exist on the Linux host under investigation. Persistence mechanisms are ways a program can survive after a system reboot. 
This helps malware authors retain their access to a system even if the system is rebooted. 
Let's see how we can identify persistence mechanisms in a Linux host.

### Cron Jobs

Cron jobs are commands that run periodically after a set amount of time. A Linux host maintains a list of Cron jobs in a file located at `/etc/crontab`. 
We can read the file using the `cat` utility.

```bash
cat /etc/crontab
```

The above terminal output shows the contents of a sample `/etc/crontab` file. 
As can be seen, the file contains information about the time interval after which the command has to run, the username that runs the command, and the command itself. 
It can also contain scripts to run, where the script that needs to be run will be placed on the disk, and the command to run it will be added to this file.

### Service Startup

Like Windows, services can be set up in Linux that will start and run in the background after every system boot. 
A list of services can be found in the `/etc/init.d` directory. 
We can check the contents of the directory by using the `ls` utility.

```bash
ls /etc/init.d/
```

### .Bashrc

When a bash shell is spawned, it runs the commands stored in the `.bashrc` file. This file can be considered as a startup list of actions to be performed. Hence it can prove to be a good place to look for persistence.
The following terminal shows an example .bashrc file.

```bash
cat ~/.bashrc
```

____
# Evidence of Execution

Knowing what programs have been executed on a host is one of the main purposes of performing forensic analysis. On a Linux host, we can find the evidence of execution from the following sources.
### Sudo Execution history

All the commands that are run on a Linux host using `sudo` are stored in the auth log. We already learned about the auth log in Task 3. 
We can use the `grep` utility to filter out only the required information from the auth log.

```bash
cat /var/log/auth.log* | grep -i COMMAND|tail
```

The above terminal shows commands run by the user ubuntu using `sudo`.
### Bash History

Any commands other than the ones run using `sudo` are stored in the bash history. 
Every user's bash history is stored separately in that user's home folder. Therefore, when examining bash history, we need to get the bash_history file from each user's home directory. It is important to examine the bash history from the root user as well, to make note of all the commands run using the root user as well.

```bash
cat ~/.bash_history
```

### Files accessed using vim

The `Vim` text editor stores logs for opened files in `Vim` in the file named `.viminfo` in the home directory. This file contains command line history, search string history, etc. for the opened files. We can use the `cat` utility to open `.viminfo`

```bash
cat ~/.viminfo
```

____
# Log Files

One of the most important sources of information on the activity on a Linux host is the log files. These log files maintain a history of activity performed on the host and the amount of logging depends on the logging level defined on the system. Let's take a look at some of the important log sources. Logs are generally found in the `/var/log` directory.

## Syslog 

The Syslog contains messages that are recorded by the host about system activity. The detail which is recorded in these messages is configurable through the logging level. 
We can use the `cat` utility to view the Syslog, which can be found in the file `/var/log/syslog`. 
Since the Syslog is a huge file, it is easier to use `tail`, `head`, `more` or `less` utilities to help make it more readable.

```bash
cat /var/log/syslog* | head
```

The terminal output above shows the **system time**, **system name**, the **process** that generated the log (along with its **process ID**), and the **log details**.
### Observations:

1. **Cron Jobs:**  
    The logs display a couple of cron jobs being executed, along with some additional system activities.
    
2. **Asterisk (*) in syslog:**  
    The asterisk (*) after `syslog` is used to include **rotated log files** as well. Over time, Linux systems automatically rotate older logs into files like `syslog.1`, `syslog.2`, etc., to prevent the `syslog` file from growing too large.
    
3. **Using Wildcards:**  
    To search across all rotated syslog files, the asterisk (*) wildcard is used, enabling you to query logs across `syslog`, `syslog.1`, `syslog.2`, and so on.

## Auth Logs

We have already discussed the auth logs in the previous tasks. The auth logs contain information about users and authentication-related logs. The below terminal shows a sample of the auth logs.

```bash
cat /var/log/auth.log* |head
```

## Third-Party Logs

Similar to the syslog and authentication logs, the `/var/log/` directory contains logs for third-party applications such as webserver, database, or file share server logs. We can investigate these by looking at the `/var/log/` directory.

```bash
ls /var/log
```

As is obvious, we can find the apache logs in the apache2 directory and samba logs in the samba directory.

```bash
ls /var/log/apache2/
```

Similarly, if any database server like MySQL is installed on the system, we can find the logs in this directory.

____
## End of This Section