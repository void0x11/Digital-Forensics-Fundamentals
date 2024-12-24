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

