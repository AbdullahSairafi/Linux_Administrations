# Managing Files from CLI

## Command-line File Management
| Activity | Syntax |
| ----------- | ----------- |
| Create a directory | `mkdir directory` |
| Copy a file | `cp file new-file` |
| Copy a directory and its contents | `cp -r directory new-directory` |
| Move or rename a file or director | `mv file new-file` |
| Remove a file | `rm file` |
| Remove a directory containing files | `rm -r directory` |
| Remove an empty directory | `rmdir directory` |

## Making Links Between Files

### Hard Links

`$ ln <src_file> <dst_file>`

*Note: `<dst_file>` is created by ln command and shouldn't exist beforehand*

Example:

`$ ln newfile.txt /tmp/newfile-hlink2.txt`

### Soft Links

`$ ln -s <src_file> <dst_file>`

*Note: `<dst_file>` is created by ln command and shouldn't exist beforehand*

Example:

`$ ln -s /home/user/newfile-link2.txt /tmp/newfile-symlink.txt`

---

# Creating, Viewing, and Editing Text Files

## Redirecting Output to a File or Program

| Usage | Explanation |
| ----------- | ----------- |
| > file | redirect **stdout** to overwrite `file` |
| >> file | redirect **stdout** to append to `file` |
| 2> file | redirect **stderr** to overwrite `file` |
| 2> /dev/null | discard **stderr** error messages by redirecting to `/dev/null` |
| file 2>&1 | redirect **stdout** and **stderr** to  overwrite the same `file`
| &> file | redirect **stdout** and **stderr** to  overwrite the same `file` |
| >> file 2>&1 | redirect **stdout** and **stderr** to append to the same `file` |
| &>> file | redirect **stdout** and **stderr** to append to the same `file` |

## Constructing Pipelines

Example:

`$ ls -l /usr/bin | less`

### Pipes with `tee` Command

Example:

`$ ls -l | tee /tmp/saved-output | less`

---

# Managing Local Users and Groups

the `id <user>` command displays information about a user.

Example:

`$ id root`

By defaults, users are stored in `/etc/passwd` file.

| Entry | File |
| ----------- | ----------- |
| Users | `/etc/passwd` |
| Groups | `/etc/group` |
| Passwords  *encrypted* | `/etc/shadow` |

### Switching Users
the `su - <user>` command allows users to switch to a different account.

Example:

`$ su - user1`

*Note*: if user is ommited, the `su` command will switch to `root` user by default.

*Note*: the `-` (dash option) sets up the shell environment to the target user.

### Running Commands with Sudo
It is possible gain superuser previliges using `sudo` command instead of logging into `root` user.


### configuring Sudo

To enable full `sudo` access for the user `user01`, you could create `/etc/sudoers.d/user01` with the following content:

`user01 ALL=(ALL) ALL`

To enable full sudo access for the group group01, you could create `/etc/sudoers.d/group01` with the following content:

`%group01 ALL=(ALL) ALL`

It is also possible to set up sudo to allow a user to run commands as another user without entering their password:

`user01 ALL=(ALL) NOPASSWD:ALL`
## Managing Local Users

### Creating Users

The following command creates a user named `username`. At this point, cannot log in until a password is set.

`$ useradd username`

Run the following command to display basic options with `useradd` command

`$ useradd --help`

### Modifying Existing Users

Use the following command to display basic options that can be used to modify an account

`$ usermod --help`

| `usermod` options | Usage |
| ----------- | ----------- |
| `-c, --comment COMMENT` | Add the user's real name to the comment field. |
| `-g, --gid GROUP` | Specify the primary group for the user account. |
| `-G, --groups GROUPS` | Specify a comma-separated list of supplementary groups for the user account. |
| `-a, --append` | Used with the `-G` option to add the supplementary groups to the user's current set of groups.  |
| `-d, --home HOME_DIR` | Specify a particular home directory for the user account. |
| `-m, --move-home` | Move the user's home directory to a new location. Must be used with the `-d` option. |
| `-s, --shell SHELL` | Specify a particular login shell for the user account. |
| `-L, --lock` | Lock the user account. |
| `-U, --unlock` | Unlock the user account. |


### Deleting Users
The `$ userdel username` command removes the details of `username` from `/etc/passwd`, but leaves the user's home directory intact.

The `userdel -r username` command removes the details of `username` from `/etc/passwd` and also deletes the user's home directory.

### Setting Passwords
The `$ passwd username` command sets the initial password or changes the existing password of `username`.

The `root` user can set a password to any value. A message is displayed if the password does not meet the minimum recommended criteria, but is followed by a prompt to retype the new password and all tokens are updated successfully.

**Exercise:**

1. Create the operator1 user and confirm that it exists in the system.


&emsp;&emsp;&emsp;`$ useradd operator1`
  
&emsp;&emsp;&emsp;`$ tail /etc/passwd`

2. Set the password for operator1 to redhat.

&emsp;&emsp;&emsp;`$ passwd operator1`

&emsp;&emsp;&emsp;New password: `redhat`


3. Add a secondary group `wheel` to operator1

&emsp;&emsp;&emsp;`$ usermod -aG wheel operator1`

4. Delete the `operator1` user along with any personal data of the user. Confirm that the user is successfully deleted.

&emsp;&emsp;&emsp;`$ userdel -r operator1`

&emsp;&emsp;&emsp;`$ tail /etc/passwd`
## Managing Local Groups

### Creating Groups

The `groupadd` command creates groups. Without options the groupadd command uses the
next available GID from the range specified in the `/etc/login.defs` file while creating the groups.

The `-g` option specifies a particular GID for the group to use.

Example: 

`$ sudo groupadd group01`

or 

`$ sudo groupadd -g 10000 group01`

The `-r` option creates a system group using a GID from the range of valid system GIDs listed in the /etc/login.defs file. The SYS_GID_MIN and SYS_GID_MAX configuration items in `/ etc/login.defs` define the range of system GIDs.

Example:

`$ sudo groupadd -r group02`

### Modifying Existing Groups

The `groupmod` command changes the properties of an existing group. The `-n` option specifies a new name for the group.


### Deleting Groups

The `groupdel` command removes groups.

`$ sudo groupdel group0022`

### Changing Group Membership

The membership of a group is controlled with user management. Use the `usermod -g`
command to change a user's primary group.

`$ sudo usermod -g group01 user02`

Use the `usermod -aG` command to add a user to a supplementary group.

`$ sudo usermod -aG group01 user03`

## Manging User Passwords

### Password Aging

`$ sudo chage -m 0 -M 90 -W 7 -I 14 user03`

The preceding chage command uses the following options

| Option | Usage |
| ----------- | ----------- |
| `-d` | Set the number of days since January 1st, 1970 when the password was last changed. The date may also be expressed in the format YYYY-MM-DD (or the format more commonly used in your area). |
| `-E` | Set the date or number of days since January 1, 1970 on which the user's account will no longer be accessible. The date may also be expressed in the format YYYY-MM-DD.| 
| `-I` | Set the number of days of inactivity after a password has expired before the account is locked. |
| `-l` | Show account aging information. |
| `-m` | Set the minimum number of days between password changes to MIN_DAYS. A value of zero for this field indicates that the user may change his/her password at any time. |
| `-M` | Set the maximum number of days during which a password is valid. |
| `-W` | Set the number of days of warning before a password change is required. |

### Nologin Shell

the following command disables interactive shell for `user1`

`$ usermod -s /sbin/nologin user1`

---

# Controlling Access to Files

## Changing File and Directory Permissions

### Changing Permissions with the Symbolic Method

`$ chmod WhoWhatWhich file|directory`

- Who is u, g, o, a (for user, group, other, all)
- What is +, -, = (for add, remove, set exactly)
- Which is r, w, x (for read, write, execute)

Example:

`$ chmod u=rwx, g+x, o-rwx file1`

Note:

The chmod command supports the -R option to recursively set permissions on the files in an entire directory tree.

### Changing Permissions with the Numeric Method

`$ chmod ### file|directory` 
- Each digit represents permissions for an access level: *user, group, other*.
- The digit is calculated by adding together numbers for each permission you want to add, 4 for
read, 2 for write, and 1 for execute.

Example:

Set read and write permissions for user, read permission for group and other, on `samplefile`

`$ chmod 644 samplefile`

## Changing File and Directory User or Group Ownership

Note: 

Only `root` can change the user that owns a file. However, *Group* ownership can be set by `root` or by the file's *owner*

`# chown owner:group file|directory`

Example:

change `test_file` owner to `student`

`# chown student test_file` 

chown can be used with the -R option to recursively change the ownership of an entire directory
tree.

`# chown -R student test_dir`

## Special Permissions

| Special permission | Effect on files | Effect on directories |
| ----------- | ----------- | ----------- |
| `u+s` (suid) | File executes as the user that owns the file, not the user that ran the file. |  No effect. | 
| `g+s` (sgid) | File executes as the group that owns the file. | Files newly created in the directory have their group owner set to match the group owner of the directory. |
| `o+t` (sticky) | No effect. | Users with write access to the directory can only remove files that they own; they cannot remove or force saves to files owned by other users.

### Setting Special Permissions

- Symbolically: setuid = u+s; setgid = g+s; sticky = o+t

- Numerically (fourth preceding digit): setuid = 4; setgid = 2; sticky = 1

&emsp;&emsp;

Examples:

Add the setgid bit on directory:

&emsp;`$ chmod g+s directory`

Set the setgid bit and add read/write/execute permissions for user and group with no access for others, on directory:

&emsp;`$ chmod 2770 directory`

## Default File Permissions

The `umask` command prints the default file permissoins

A umask of 027 ensures that new files have read and write permissions for user and read
permission for group. New directories have read and write access for group and no permissions for
other

Note: to presistently update the umask value, write it to the `~/.bashrc` file.

Example:

`$ echo "umask 007" >> ~/.bashrc`

---

# Monitoring and Managing Linux Processes

## Listing Processes

Two common commands to inspect running processes

1. `$ top`
2. `$ ps -aux`

## Controlling Jobs

### Running Jobs in background

Syntax:  `$ <command> &`

Example:

`$ sleep 10000 &`

the `$ jobs` command lists current jobs

A background `job` can be brought to the foreground by using the `fg` command with its job ID *(%job number).*

Example: 

`$ fg %1`

Similarly, a job can be brought to the background using the `bg` command with its job ID *(%job number)*

Example: 

`$ bg %1`


## Killing Processes

### Process control using signals


You can use the
`$ kill -l` command to list the names and numbers of all available signals.

To kill a process

Syntax: 

1. `$ kill <PID>`
2. `$ kill -SIGKILL <PID>`
3. `$ kill -9 <PID>`

Note: use `top` or `ps -aux` to find the process id.

---

# Controlling Services and Daemons



## Listing Service Units

`$ systemctl list-units --type=service`

## Viewing Service States

Syntax:

`$ systemctl status <service>`

Example: 

`$ systemctl status sshd.service`

## Verifying the Status of a Service

Syntax: 

`$ systemctl is-active <service>`

`$ systemctl is-enabled <service>`

`$ systemctl is-failed <service>`

Example:

`$ systemctl is-active sshd.service`


## Summary of systemctl Commands

| Task | Command |
| ----------- | ----------- |
View detailed information about a unit state. | `$ systemctl status  <UNIT>` |
| Stop a service on a running system. |`$ systemctl stop  <UNIT>` | 
| Start a service on a running system. | `$ systemctl start  <UNIT>` |
| Restart a service on a running system. | `$ systemctl restart  <UNIT>` |
| Reload the configuration file of a running service. | `$ systemctl reload  <UNIT>` |
| Completely disable a service from being started, both manually and at boot. | `$ systemctl mask  <UNIT>` |
| Make a masked service available. | `$ systemctl unmask  <UNIT>` |
| Configure a service to start at boot time. | `$ systemctl enable  <UNIT>` |
| Disable a service from starting at boot time. | `$ systemctl disable  <UNIT>` |
| List units required and wanted by the specified unit. | `$ systemctl list-dependencies  <UNIT>` |

---

# Configuring and Securing SSH

## Configuring SSH Key-based Authentication

### Generating SSH Keys

Use the `$ ssh-keygen` command to generate a private and public key pair.

Note: use a custom name when generating a key otherwise it will use the default files `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` respectively. If default keys were used before, they will be overwritten.

### Sharing the Public Key

Syntax:

`$ ssh-copy-id -i <public_key> username@server`

Example:

`$ ssh-copy-id -i .ssh/key-with-pass.pub user@remotehost`

Note: if the remote server was not in the `/etc/hosts` file, then connect using the IP address.

### Logging in with Key-based Authentication

Use the public key to log into the remote host.

`$ ssh -i .ssh/key-with-pass user@remotehost`


## Customizing OpenSSH Service Configuration

OpenSSH service is provided by a daemon called sshd. Its main configuration file is `/etc/ssh/sshd_config`

### Prohibit the Superuser From Logging in Using SSH

The OpenSSH server uses the PermitRootLogin configuration setting in the `/etc/ssh/sshd_config` configuration file to allow or prohibit users logging in to the system as root.

in the above file, by defualt, the parameter :

`PermitRootLogin yes`

To prevent root from loggin in set the parameter to **no**

`PermitRootLogin no`

### Prohibiting Password-Based Authentication for SSH

Set the `PasswordAuthentication` parameter to **no**

`$ PasswordAuthentication no`

Note: after modifyin the `/etc/ssh/sshd_config` file, reload the `sshd` daemon.

`$ systemctl reload sshd`

---

# Analyzing and Storing Logs

Coming Soon

# Managing Networking

## Configuring Networking from the Command Line

### Describing NetworkManager Concepts

*Note: Command-line and graphical tools talk to **NetworkManager** and save configuration files in the `/etc/sysconfig/network-scripts` directory.*

- A device is a network interface.
- A connection is a collection of settings that can be configured for a device.
- The nmcli utility is used to create and edit connection files from the command line.

### Adding a network connection

Example1:

The following command adds a connection profile with the following settings:

- connection name `eno2`
- connection type is `ethernet`
- interface/device `enp0s3`
- Takes *ipv4* and *ipv6* information from **DHCP**

`$ nmcli con add con-name eno2 type ethernet ifname enp0s3`

Example2: 
 

`$ nmcli con add con-name eno2 type ethernet ifname enp0s3 
ipv4.address 192.168.0.5/24 ipv4.gateway 192.168.0.254 ipv4.method manual`

### Summary of Commands

| Command | Usage | 
| ----------- | ----------- |
| `$ nmcli dev status` | Show the NetworkManager status of all network interfaces. |
| `$ nmcli con show` | List all connections. |
| `$ nmcli con show name` | List the current settings for the connection name. |
| `$ nmcli con add con-name name` | Add a new connection named name. |
| `$ nmcli con mod name` | Modify the connection name. | 
| `$ nmcli con reload` | Reload the configuration files (useful after they have been edited by hand). |
| `$ nmcli con up name` | Activate the connection name. | 
| `$ nmcli dev dis dev` | Deactivate and disconnect the current connection on the network interface dev. |
| `$ nmcli con del name` | Delete the connection name and its configuration file. |


*Note: add help after the command in question and help menu will be displayed*

`$ nmcli con add help`

### Editing Network Configuration Files

By default, changes made with `nmcli con mod name` are automatically saved to `/etc/sysconfig/network-scripts/ifcfg-name`. That file can also be manually edited with a text
editor. After doing so, `run nmcli con reload` so that `NetworkManager` reads the configuration
changes.

## Configuring Host Names and Name Resolution

### Changing Host Name

`$ hostnamectl set-hostname host.example.com`

---

# Archiving and Transferring Files

## Managing Compressed tar Archives

### Summary of tar Commands

| Command | Usage |
| ----------- | ----------- |
| `$ tar -cf archive.tar file1 file2` | Create an `archive.tar` with contents `file1` and `file2` |
| `$ tar -tf archive.tar` | list the contents of the archive file. |
| `$ tar -xf archive.tar` | extract files from archive file |
| `$ tar -czf archive.tar.gz /etc` | Create a `gzip` compressed archive file with contents of `/etc` |
| `$ tar -cjf archive.tar.bz2 /etc` | Create a `bzip2` compressed archive file with contents of `/etc` |
| `$ tar -cJf archive.tar.xz /etc` | Create a `xz` compressed archive file with contents of `/etc` |
| `$ tar -xzf archive.tar.gz` | Extracts `gzip` compressed archive. |
| `$ tar -xjf archive.tar.bz2` | Extracts `bzip2` compressed archive. |
| `$ tar -xJf archive.tar.xz` | Extracts `xz` compressed archive. |

## Transferring Files Between Systems Securely

### Summary of scp command

| Command | Usage |
| ----------- | ----------- |
| `$ scp ~/file1 remoteuser@remotehost:/home` | copy `file1` from local host to remote host `/home` directory. | 
| `$ scp remoteuser@remotehost:~/file1 /home` | copy `file1` from remote host to local host `/home` directory. |

*Note: to copy a directory, use `-r` option with `scp` command.*

Another option to transfer files is `sftp` command.

- use `put` command to send files or directories from **local** host to **remote** host.
- use `get` command to send files or directories from **remote** host to **local** host. 

--- 

# Accessing Linux File System

Coming Soon :)

--- 

# Scheduling Future Tasks

## Scheduling a Deferred User Job

`$ echo "date >> /home/student/myjob.txt" | at now +3min`

- Use `$ atq` to list scheduled jobs.
- Use `$ atrm <JOB_NUMBER>` to remove a job.


## Scheduling Recurring User Jobs

`Crontab` options.

| Command | Usage | 
| ----------- | ----------- | 
| `$ crontab -l` | List the jobs for current user. |
| `$ crontab -r` | Remove all jobs from current user. |
| `$ crontab -e` | Edit jobs for the current user. |
| `$ crontab filename` | Remove all jobs, and replace with the jobs read from `filename`. If no file is specified, **stdin** is used. |

*Note: the fields in a `crontab` file appear in the following order:

`MINS HRS DAY_OF_MONTH MONTH DAY_OF_WEEK COMMAND`

Syntax rules for all five fields in `crontab` file.

| Rule | Description | 
| ----------- | ----------- |
| `*` | Always | 
|  `NUMBER` | Specify the number of mins, hrs or weekdays. |
| `x-y` | range from `x` to `y` inclusive. both `x` & `y` are numbers.|
|`x,y` | for lists. Lists can include ranges as well, for example, 5,10-13,17. | 
| `*/x` | to indicate an interval of x. `*/7` in the Mins column runs a job every 7 minutes. |

*Note: 3-letter abbreviations can be used with days and months, for example, `Jan, Feb, Mon, Wed`, etc.* 

Example1:

The following executes the command `echo "hello world` at exactly
9 a.m. on February 2nd, every year.

`$ 0 9 2 2 * echo "hello world"`

Example2: 

The following executes the command `echo "hello world` every five
minutes between 9 a.m. and 5 p.m., on every Friday in July.

`$ */5 9-16 * Jul 5 echo "hello world"`

Example3: 

`$ */2 08-20 * * Mon-Fri /usr/bin/date >> /home/student/my_first_cron_job.txt`

---

# Tuning System Performance

## Adjusting Tuning Profiles

### Selecting Tuning Profiles

Tuned commands 

| Command | Usage | 
| ----------- | ----------- |
| `$ tuned-adm list` | lists all available tuning profiles. |
| `$ tuned-adm active` | Display currently active profile. |
| `$ tuned-adm profile profilename` | change active profile. | 
| `$ tuned-adm recommend` | display recommended profile. | 

Example:

change active profile to `throughput-performance`

`$ tuned-adm profile throughput-performance`

## Influencing Process Scheduling


### Summary of nice and renice commands

| Command Syntax | Usage |
| ----------- | ----------- |
| `$ nice -n <NICE_LVL> COMMAND` | Start a process with custom nice level. |
| `$ renice -n <NICE_LVL> <PID>` | update the renice value of a process. |

Examples:

`$ nice -n 15 sha1sum &`

`$ renice -n 19 3521`

--- 

# Controlling Access to Files with ACLs

## Interpreting File ACLs

### Summary of ACLs Commands

| Command | Usage | 
| ----------- | ----------- |
| `$ getfacl file` | View the ACL for `file`. | 
| `$ setfacl -m u:name:rX file` | Add or modify a user or named user ACL. If `name` is blank, then rule applies to file *owner*. |
| `$ setfacl -m g:name:rw file` | o add or modify a group or named group ACL. If `name` is blank, then rule applies to group *owner*. |
| `$ setfacl -m o::- file` | add or modify the other ACL. |
| `$ getfacl file-A \| setfacl --set-file=- file-B` | use the output from getfacl as input to setfacl. The --set-file option accepts input from a file or from stdin. The dash character (-) specifies the use of stdin. In this case, file-B will have the same ACL settings as file-A.
| `$ setfacl -x u:name,g:name file` | Deleting specific ACL entries. |
| `$ setfacl -m d:u:name:rx directory` | Adds a default named user. |

--- 

# Managing SELinux Security

Coming Soon ;)

# Managing Basic Storage

## Adding Partitions, File Systems, and Persistent Mounts

### Summary of parted commands

| Command | Usage | 
| ----------- | ----------- |
| `$ parted /dev/sdb` | Open interactive session to mangage `sdb` |
| `$ mklabel msdos` | Write an MBR disk label. |
| `$ mklabel gpt` | Write a GPT disk label. |
| `$ mkpart` | Create a partiotion interactively. |
| `$ udevadm settle` | Wait for the system to detect the new partition. |
| `$ rm <PARTITION_NUMBER>` | Delete a partition. |
| `$ mkfs.xfs /dev/sdb1` | Create `xfs` file system on `/dev/sdb1` |
| `$ mkfs.ext4 /deb/sdb1` | Create `ext4` file system on `/dev/sdb1` |
| `$ mount /dev/sdb1 /mnt` | Manually mount `/dev/sdb1` to `/mnt` directory. |

*Note: you can explore parted commands more with help option. For example,* `$ help mkpart` *will display how to use* `mkpart`

### Persistantly Mounting File Systems

Add the device using its `UUID` to the `/etc/fstab`. Use `lsblk --fs` to display `UUID` for block devices. 

Entry fields in `/etc/fstab` file:

1- UUID of the device, alternatively, you can use device file such as `/dev/sdb1`

2- The directory mount point(it must exist beforehand).

3- File system type such as `xfs` or `ext4`

4- comma-separated list of options to apply to the device. `defaults` is a set of commonly used options.

5- Used by the `dump` command to back up the device. Other backup applications do not usually use this field.

6- The last field, the fsck order field, determines if the fsck command should be run at system boot to verify that the file systems are clean. The value in this field indicates the order in which fsck should run. For XFS file systems, set this field to 0 because XFS does not use fsck to check its file-system status. For ext4 file systems, set it to 1 for the root file system and 2 for the other ext4 file systems. This way, fsck processes the root file system first and then checks file systems on separate disks concurrently, and file systems on the same disk in sequence.

Example of an entry in the `/etc/fstab` file:

`UUID=7a20315d-ed8b-4e75-a5b6-24ff9e1f9838 /dbdata xfs defaults 0 0`

*Note: run* `$ systemctl daemon-reload` *after modifying* `/etc/fstab` *file.*

## Creating Swap Space
Use the following commands to create and activate swap space.

| Command | Usage |
| ----------- | ----------- |
| `$ mkswap /dev/sdb2` | Apply swap signature to device.
| `$ swapon /dev/sdb2` | Activate a formatted swap space. |
| `$ swapoff /dev/sdb2` | deactivate a formatted swap space. |

### Persistantly Activating Swap Space

Add an entry in `/etc/fstab`

Example:

`UUID=39e2667a-9458-42fe-9665-c5c854605881 swap swap defaults 0 0`

Swap Priorities: Kernel uses swap space with higer priority first.

Example: 

`UUID=39e2667a-9458-42fe-9665-c5c854605881 swap swap pri=4 0 0`

--- 

# Managing Logical Volumes

## Creating Logical Volumes

1- prepare the physical device:
    
```
$ parted -s /dev/sdb mklabel gpt
$ parted -s /dev/sdb mkpart primary 1MiB 257MiB
$ parted -s /dev/sdb mkpart primary 258MiB 514MiB 
$ parted -s /dev/sdb set 1 lvm on
$ parted -s /dev/sdb set 2 lvm on 
$ udevadm settle
```

2- Create a physical volume:

```
$ pvcreate /dev/sdb1 /dev/sdb2
```

3- Create a volume groupe:

```
$ vgcreate vg01 /dev/sdb1 /dev/sdb2
```
4- Create a logical volume:

```
$ lvcreate -n lv01 -L 700M vg01
```

- lvcreate -L 128M: Size the logical volume to exactly 128 MiB.
- lvcreate -l 128 : Size the logical volume to exactly 128 extents. The total number of bytes
depends on the size of the physical extent block on the underlying physical volume.

5- Add the file system and mount it

```
$ mkfs.xfs /dev/vg01/lv01
$ mkdir /mnd/data 
```
add the entry into `/etc/fstab`

```
/dev/vg01/lv01 /mnt/data xfs defaults 1 2
```

and mount `/mnt/data`

```
$ mount /mnt/data
```

### Removing a Logical Volume

```
$ umount /mnt/data
$ lvremove /dev/vg01/lv01
$ vgremove vg01
$ pvremove /dev/sdb1 /dev/sdb2
```

### Reviewing LVM Status Information

| Command | Usage |
| ----------- | ----------- |
| `pvdisplay /dev/sdb1` | Display physical volume info. |
| `vgdisplay vg01` | Display volume group info. |
| `lvdisplay /dev/vg01/lv01` | Display logical volume info. |


## Extending Logical Volumes

1- Extending a Volume Group
```bash
# Create a partition and physical volume
$ parted -s /dev/vdb mkpart primary 1027MiB 1539MiB
$ parted -s /dev/vdb set 3 lvm on
$ pvcreate /dev/vdb3
# Extend volume group
$ vgextend vg01 /dev/vdb3
```
2- Reducing a Volume Group
```bash
# Move physical extents in vdb3 to other physical volumes
$ pvmove /dev/vdb3
# reduce volume group
$ vgreduce vg01 /dev/vdb3
# (Optional) Stop using device as a physical volume
$ pvremove /dev/vdb3
```
3- Extending a Logical Volume
``` bash
# Add additional 100MiB to logical volume.
$ lvextend -L +100M /dev/vg01/lv01
# Alternatively, you can physical extents
$ lvextend -l +100 /dev/vg01/lv01
# or you can set the size of logical volume 
$ lvextend -L 100M /dev/vg01/lv01
```
4- Extending File Systems (xfs, ext4)
```bash
# extending xfs file system
$ xfs_growfs /mnt/data
# Extending ext4 file system
$ resize2fs /dev/vg01/lv01
# Verify the new size of mounted file sysem
$ df -h /mnt/data
```
5- Extending Swap Space
```bash
# Deactivate swap space and extend lv 
$ swapoff -v /dev/vgname/lvname
$ lvextend -l +extents /dev/vgname/lvname
# Format the logical volume as swap space
$ swapon -va /dev/vgname/lvname
```

--- 

# Implementing Advanced Storage Features

## Managing Layered Storage with Stratis

1- Install and activate stratis daemon
```bash
$ yum install stratis-cli stratisd
$ systemctl enable --now stratisd
```

2- Assembling Block Storage into Stratis Pools
```bash
# Create pools of one or more block devices
$ stratis pool create pool1 /dev/vdb
# list all avaiable pools
$ stratis pool list
# add additional block devices to a pool.
$ stratis pool add-data pool1 /dev/vdc
# view the block devices of a pool.
$ stratis blockdev list pool1
```
3- Managing Stratis File Systems
```bash
# create a file system from a pool.
$ stratis filesystem create pool1 fs1
# view the list of available file systems.
$ stratis filesystem list
# create a snapshot of a Stratis-managed file system
$ stratis filesystem snapshot pool1 fs1 snapshot1
```
4- Persistently Mounting Stratis File Systems
```bash
# Find UUID of the file system
$ lsblk --fs
# Sample entry in the /etc/fstab file
UUID=31b9363b-add8-4b46-a4bf-c199cd478c55 /dir1 xfs defaults,x-systemd.requires=stratisd.service 0 0
```
&emsp; *Note: The x-systemd.requires=stratisd.service mount option delays mounting the file system until after systemd starts the stratisd.service during the boot process.*

## Compressing and Deduplicating Storage with VDO

1- Installing VDO
```bash
$ yum install vdo kmod-kvdo
```

2- Creating a VDO Volume
```bash
$ vdo create --name=vdo1 --device=/dev/vdd --vdoLogicalSize=50G
```
*Note: If you omit the logical size, the resulting VDO volume gets the same size as its physical device.*

3- Format the vdo1 volume with the XFS file system
```bash
$ mkfs.xfs -K /dev/mapper/vdo1
# mount the file system
$ mkdir /mnt/vdo1
$ mount /dev/mapper/vdo1 /mnt/vdo1
# Persistantly mound VDO (entry in /etc/fstab file)
UUID=ef8c...39b1 /mnt/vdo xfs defaults,x-systemd.requires=vdo.service 0 0
```
4- Analyzing a VDO Volume
```bash
# Display status of all VDO volumes
$ vdo status
# Display status of a specific VDO volume
$ vdo status --name=vdo1
# List all started VDO
$ vdo list
# Start and stop VDO volumes
$ vdo start -n vdo1
$ vdo stop -n vdo1
# View statistics and status of VDO volumes
$ vdostats --human-readable
```
---

# Accessing Network-Attached Storage

## Configuring NFS Server and Client

Please follow the steps in this link:
- https://www.thegeekdiary.com/centos-rhel-7-configuring-an-nfs-server-and-nfs-client/

- https://cloud.netapp.com/blog/azure-anf-blg-linux-nfs-server-how-to-set-up-server-and-client


## Mounting NFS
```bash
# mount using DNS
$ mount -t nfs -o rw,sync serverb:/share /mountpoint
# mount using IP of NFS server
$ mount -t nfs -o rw,sync 192.168.1.3:/share /mountpoint
# persistently mount NFS at boot time 
$ nano /etc/fstab
...
serverb:/share /mountpoint nfs rw,soft 0 0
```

## Automounting NFS
### Creating Indirect Maps
1- install autofs package
```bash
$ yum install autofs
```

2- Add a master map file to `/etc/auto.master.d` and must have **.autofs** extension
```bash
$ nano /etc/auto.master.d/demo.autofs
# Add the master map entry in the demo.autofs file
/shares /etc/auto.demo
```

The `/etc/auto.demo` file contains the mount details. Use an absolute file name. The `auto.demo` file needs to be created before starting the `autofs` service.

3- Create the mapping files. Each mapping file identifies the mount point, mount options, and source location to mount for a set of automounts.
```bash
$ nano /etc/auto.demo
# add the following entry into the file
work -rw,sync serverb:/shares/work
```
The mapping file-naming convention is `/etc/auto.name`

4- Start and enable the automounter service.
```bash
$ systemctl start autofs
$ systemctl enable --now autofs
```

### Direct Maps

1- Add a master map file to `/etc/auto.master.d` and must have **.autofs** extension
```bash
$ nano /etc/auto.master.d/demo.autofs
# Add the master map entry in the demo.autofs file
/- /etc/auto.direct
```

*Note: All direct map entries use `/-` as the base directory*

2- Create the mapping file `/etc/auto.direct` with the following content
```bash
/mnt/docs -rw,sync serverb:/shares/docs
```
---

# Controlling the Boot Process

## Rebooting & Shutting Down

```bash
$ systemctl poweroff # shut down
$ systemctl reboot # reboot
```

## Selecting a Systemd Target

Commonly Used Targets
| Target | purpose |
| ----------- | ----------- |
| `graphical.target` | System supports multiple users, graphical- and text-based logins. |
| `multi-user.target` | System supports multiple users, text-based logins only. |
| `rescue.target` | sulogin prompt, basic system initialization completed. |
| `emergency.target` | sulogin prompt, initramfs pivot complete, and system root mounted on / read only. | 

### Selecting a Target at Runtime

```bash
# switch to mult-user.target
$ systemctl isolate multi-user.target
```

### Set Default Target

```bash
$ systemctl get-default # get default target 
$ systemctl set-default graphical.target # set default target
```

### Selecting different Target at Boot Time

1- Boot or reboot the system.

2- Interrupt the boot loader menu countdown by pressing any key

3- Move the cursor to the kernel entry that you want to start.

4- Press **e** to edit the current entry.

5- Move the cursor to the line that starts with linux. This is the kernel command line.

6- Append `systemd.unit=target.target`. For example, `systemd.unit=emergency.target`.

7- Press **Ctrl+x** to boot with these changes.

## Resetting the Root Password

1- Edit the kernel parameters following steps 1-5 above.

2- Move the cursor to the kernel command line (the line that starts with **linux**).

3- Append `rd.break`

4- Press **Ctrl+x** to boot with the changes.

Use the following procedure to reset `root` password

1- Remount `/sysroot` as read/write.
```bash
$ mount -o remount,rw /sysroot
```

2- Switch into a **chroot** jail, where /sysroot is treated as the root of the file-system tree.
```bash
$ chroot /sysroot
```

3- Set a new password for `root`
```bash
$ passwd root
```
4- Make sure that all unlabeled files, including /etc/shadow at this point, get relabeled during boot.
```bash
$ touch /.autorelabel
```
5- type `exit` twice and the system will continue the booting.

---

# Managing Network Security

Coming Soon :)

---

# Running Containers

## Basic Containers

1- Installing Container Packages
```bash
$ yum install container-tools
```

2- Pull container image from a registry
```bash
$ podman pull registry.lab.example.com/rhel8/httpd-24:latest
# list available container images
$ podman images
```

3- Run the container image
```bash
# run container image and name it myweb and assign it a /bin/bash shell. 
$ podman run --name myweb -it registry.lab.example.com/rhel8/httpd-24 /bin/bash
```
Common options when running a container image:

- `-t` pseudo-terminal is
allocated for the container.
- `-i` the container accepts standard input
- `-d` run container in the background (detatched)

4- Run a command in a container and remove it
```bash
# run httpd -v command and remove container afterwards
podman run --rm registry.lab.example.com/rhel8/httpd-24 httpd -v
```

## Finding and Managin Containers

1- Find a container image
```bash
# find container images that include "rhel8"
$ podman search registry.redhat.io/rhel8
```

2- Inspecting container images
```bash
#inspect a remote container image
$ skopeo inspect docker://registry.redhat.io/rhel8/python-36

#inspect a local container image
$ podman inspect registry.redhat.io/rhel8/python-36 
```

3- Removing local container images
```bash
$ podman rmi registry.redhat.io/rhel8 python-36:latest
# confirm the image has been deleted
$ podman images
```

## Advanced Container Management

- Mapping container host ports to container

```bash
# map host port 8000->8080 port in the container
$ podman run -d -p 8000:8080 registry.redhat.io/rhel8/httpd-24
# list all port mappings
$ podman port -a
# open host firewall port to allow connections
$ firewall-cmd --add-port=8000/tcp
```

*Note: It is necessary to run `podman` as `root` when opening host ports below **1024**.*

- Passing Environment Variables to configure a container

```bash
# use inspect to get information about variables and what they do
$ podman inspect registry.redhat.io/rhel8/mariadb-103:1-102

# use -e option to pass environment variables
$ podman run -d --name container_name -e MYSQL_USER=user_name -e MYSQL_PASSWORD=user_password -e MYSQL_DATABASE=database_name -e MYSQL_ROOT_PASSWORD=mysql_root_password -p 3306:3306 registry.redhat.io/rhel8/mariadb-103:1-102
```

## Managing Containers

| Command | Usage |
| ----------- | ----------- |
| `$ podman ps` | List running containers. |
| `$ podman ps -a` | List all containers. |
| `$ podman stop <Container_Name>` | Stop running container. |
| `$ podman rm <Container_Name>` | Remove stopped container from host. |
| `$ podman restart <Container_Name>` | Restart stopped container. |
| `$ podman kill <Container_Name>` | Terminate container. |

## Running Commands in a Container

- Run a command in a running container
```bash
$ podman exec my_webserver cat /etc/redhat-release
```
- Attach an interactive shell to a running container
```bash
$ podman exec -it my_webserver /bin/bash
```

## Attaching Pesistant Storage to a Container

The following command mounts `/home/user/dbfiles` host directory to `/var/lib/mysql` directory in the container. the `Z` option applies SELinux context type to host directory.

```bash
# -v option to mount a volume
$ podman run -d --name mydb -v /home/user/dbfiles:/var/lib/mysql:Z -e MYSQL_USER=user -e MYSQL_PASSWORD=redhat -e MYSQL_DATABASE=inventory registry redhat.io/rhel8/mariadb-103:1-102
```

## Managing Containers as Services

Coming soon :)

