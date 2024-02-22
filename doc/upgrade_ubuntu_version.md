# Upgrade to Ubuntu 22.04

> [!Note]
> This process upgrades from one LTS version to the following one, i.e. you should repeat the process twice if you are upgrading from 18.04 to 22.04.

## 1. Upgrade all existing packages

First upgrade all installed packages:

```console
sudo apt update && sudo apt -y upgrade
```

and reboot to use the freshly installed latest kernel version:

```console
reboot
```

## 2. :warning: Open a fallback TCP port if upgrading via SSH

If you are connected to your Ubuntu instance via SSH, the upgrade tool will open another SSH port (1022) as a fallback port in case the SSH connection drops on port 22. 

```console
sudo ufw allow 1022/tcp && sudo ufw reload
```

Verify that the port is open:

```console
fferretti@iiticubws050:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere                  
1022/tcp                   ALLOW IN    Anywhere                  
22/tcp (v6)                ALLOW IN    Anywhere (v6)             
1022/tcp (v6)              ALLOW IN    Anywhere (v6)             
```

## 3. Launch the upgrade
Then do the release update using the default command (if not installed, use `sudo apt install update-manager-core`):

```console
sudo do-release-upgrade
```

During the upgrade, the upgrade tool will walk you through a series of prompts.

First, it will detect your SSH connection and notify you that an additional SSH service will be started on port 1022. 

Next, it will notify you to open port 1022, which will be used as an alternative SSH port in case of an SSH connection interruption on the default port. Since you already opened the port, just hit ENTER.

Next, you will be prompted to update the “sources.list” file from ‘focal’ to ‘jammy’ entries. To proceed with the upgrade, press “Y” and press ENTER.

The update tool will calculate all the changes and provide a summary of the following:

- Installed packages that are no longer supported by Canonical.
- The number of packages to be removed.
- The number of new packages that will be installed.
- The number of packages that will be upgraded.
- Total download size and how long the download will take.

To continue, once again, press “Y” and press ENTER.

Here you can choose whether to maintain your old package versions or to upgrade them and make them compatible with the version you are installing. In this case, I chose `Y`:

```console
Configuration file '/etc/security/limits.conf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** limits.conf (Y/I/N/O/D/Z) [default=N] ? 
```

## (3a.) :warning: If the SSH connection is lost during upgrade

If you login back into the server and again re-run `sudo apt upgrade`, it will tell you that another process is holding `dpkg`.

```console
fferretti@iiticubws050:~$ sudo apt upgrade
Citing for cache lock: Could not get lock /var/lib/dpkg/lock-frontend. It is held by process 22772 (focal)
```

In this case, you need to find out which program handles the lock-frontend file:

```console
fferretti@iiticubws050:~$ sudo lsof /var/lib/dpkg/lock-frontend
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
focal   22772 root    8uW  REG    8,5        0 1311626 /var/lib/dpkg/lock-frontend
focal   31781 root    8u   REG    8,5        0 1311626 /var/lib/dpkg/lock-frontend
```

and then kill the corresponding processes:

```console
fferretti@iiticubws050:~$ sudo kill 22772 31781
```

and remove the lock-frontend files:

```console
sudo rm /var/lib/dpkg/lock-frontend
```

Now manually configure the pending packages:

```console
sudo dpkg --configure -a
```

and run `sudo apt upgrade` to retrieve the release upgrade.

## 4. :warning: Close TCP port 

Close the TCP that we have opened before by modifying the firewall settings:

```console
sudo ufw delete allow 1022/tcp
```

## 5. Check the freshly installed Ubuntu and kernel version

Ubuntu release:

```console
lsb_release -a
```

Kernel version:

```console
uname -mrs
```

## 5. Enable third-party repositories

During the update, third-party repositories for `apt` (e.g. gazebo, nvidia-docker, ...) are disabled. In order to see them you can run:

```console
ls -l etc/apt/sources.list.d/
```

To enable them, simply open each file and uncomment the entries by deleting the `#` sign at the beginning of each line.

Finally, free up the disk space by removing all the unnecessary packages as follows:

```console
sudo apt autoremove --purge
```
