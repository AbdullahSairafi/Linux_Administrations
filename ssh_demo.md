# 1 Objective

- Working with Ubuntu and Redhat Linux.
- Basic user management.
- Understanding `ssh` login.
- Passwordless login using key-based authentication.

--- 

# 2 Project Description

For this project, we will work with two machines, the first is an Ubuntu VM, and the second is a Redhat VM. 

## Ubuntu VM :
- Has five users named `dsuser{1..5}` (assign them passwords accordingly).
- The five users mentioned above are part of the `dsgroup`.
- Disable `root` login through `ssh`.

## Redhat VM :
- Has five users named `mcuser{1..5}` (assign them passwords accordingly).
- The five users mentioned above are part of the `mcgroup`.
- Disable `root` login through `ssh`.

## Other requirements :

- `dsuser{1,2}` can use key-based authentication to login to `mcuser{1,2}` and vice-versa.
- `ssh` can only be used between the two VMs and disabled for all other machines.

---

# 3 Implementation

## 3.1 Ubuntu VM

1. Create users and assign passwords

    ```bash
    $ groupadd dsgroup 
    # Note: in Ubuntu we must explicitly create home directory using -m
    $ useradd -G dsgroup -m dsuser1
    $ passwd dsuser1
    ```

2. Download and enable `ssh` server

    ```bash
    $ apt-get install openssh-server
    $ systemctl enable ssh
    $ systemctl start ssh
    ```

3. Allow only redhat VM to login to this machine using ssh
    
    1. Using tcp wrappers
    
        - Add **permitted** IPs to `/etc/hosts.allow`
        - Add **denied** IPs to `/etc/hosts.deny`
    

    ```bash
    # Method 1
    # add the IP of redhat VM to /etc/hosts.allow
    $ vim /etc/hosts.allow
    sshd: 192.168.1.2

    # deny all other IPs in /etc/hosts.deny
    $ vim /etc/hosts.deny
    sshd: ALL

    ```

    2. Using ssh daemon configuration
        - Add **permitted** IPs to `/etc/ssh/sshd_config`

    ```bash
    # Method 2
    $ vim /etc/ssh/sshd_config
    Match Address 192.168.1.2
        PasswordAuthentication yes
    ```

## 3.2 Redhat VM

1. Create users and assign passwords

    ```bash
    $ groupadd dsgroup 
    # Note: in Ubuntu we must explicitly create home directory using -m
    $ useradd -G dsgroup mcuser1
    $ passwd mcuser1
    ```

2. Download and enable `ssh` server

    ```bash
    $ yum install openssh-server
    $ systemctl enable ssh
    $ systemctl start ssh
    ```

3. Allow only Ubuntu VM to login to this machine using ssh
    
   1. Using ssh daemon configuration
        - Add **permitted** IPs to `/etc/ssh/sshd_config`

    ```bash
    # Method 1
    $ vim /etc/ssh/sshd_config
    Match Address 192.168.1.3
        PasswordAuthentication yes
    ```

    
    2. Using firewall
    
         We can limit `ssh` access to our machine by moving the `ssh` service to the internal zone of the firewall. Next, we specifiy the IPs we want to allow to use `ssh`.

        - Add `ssh` service to `internal` zone
        - Add allowed IPs to the `internal` zone
        - Remove `ssh` service from `public` zone

    ```bash
    $ firewall-cmd --zone=internal --add-service=ssh --permanent
    $ firewall-cmd --zone=internal --add-source=192.168.1.3 --permanent
    $ firewall-cmd --zone=public --remove-service=ssh --permanent
    $ firewall-cmd --reload
    ```


## 3.2 Key-based Authentication

```bash
# generate ssh key-pair
$ ssh-keygen
# generated a key-pair named "key" and "key.pub"
# copy the public key to the remote host
$ ssh-copy-id -i .ssh/key.pub remoteuser@remotehost
# key-based authentication login
$ ssh -i .ssh/key remoteuser@remotehost
```

## 3.3 Disabling root login using ssh

```bash
$ vim /etc/ssh/sshd_config
# change root login parameter to "no"
PermitRootLogin no
```