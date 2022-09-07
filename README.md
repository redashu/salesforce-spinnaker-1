## Training schedule 

<img src="sch.png">

### application platfor setup understanding 

<img src="app_plat.png">

### Understanding CI CD 

<img src="cicd.png">

### Spinnaker Installation benefits

<img src="install.png">


## Spinnaker Installation -- NEEd 

### Resources 

<img src="res.png">

### creating Ubuntu vm -- to install spinnaker 

```
fire@ashutoshhs-MacBook-Air ~ % ls -l Downloads/ashu-spinnaker-key.pem 
-rw-r--r--@ 1 fire  staff  1700 Sep  7 10:29 Downloads/ashu-spinnaker-key.pem
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % chmod 400 Downloads/ashu-spinnaker-key.pem 
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % ssh  -i Downloads/ashu-spinnaker-key.pem  ubuntu@34.215.91.255                           
The authenticity of host '34.215.91.255 (34.215.91.255)' can't be established.
ECDSA key fingerprint is SHA256:+62b9iuVba5SkXboBqxYNoqjjB89vGk4K/xJozCd11U.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '34.215.91.255' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 22.04 LTS (GNU/Linux 5.15.0-1011-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Sep  7 05:22:03 UTC 2022

  System load:  0.00146484375     Processes:             118
  Usage of /:   1.5% of 96.75GB   Users logged in:       0
  Memory usage: 2%                IPv4 address for eth0: 172.31.4.161
  Swap usage:   0%

```

### Now creating user 

```
ubuntu@ip-172-31-4-161:~$ sudo adduser  spinnaker  
Adding user `spinnaker' ...
Adding new group `spinnaker' (1001) ...
Adding new user `spinnaker' (1001) with group `spinnaker' ...
Creating home directory `/home/spinnaker' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for spinnaker
Enter the new value, or press ENTER for the default
	Full Name []: 
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
ubuntu@ip-172-31-4-161:~$ 

```

### adding user to sudo group 

```
ubuntu@ip-172-31-4-161:~$ sudo ls /etc/sudoers.d/
90-cloud-init-users  README
ubuntu@ip-172-31-4-161:~$ sudo vim  /etc/sudoers.d/90-cloud-init-users 
ubuntu@ip-172-31-4-161:~$ 
ubuntu@ip-172-31-4-161:~$ sudo  cat  /etc/sudoers.d/90-cloud-init-users
# Created by cloud-init v. 22.2-0ubuntu1~22.04.1 on Wed, 07 Sep 2022 04:58:54 +0000

# User rules for ubuntu
ubuntu ALL=(ALL) NOPASSWD:ALL
spinnaker ALL=(ALL) NOPASSWD:ALL
```

### login to spinnaker user 

```
ubuntu@ip-172-31-4-161:~$ su  - spinnaker 
Password: 
spinnaker@ip-172-31-4-161:~$ 
spinnaker@ip-172-31-4-161:~$ whoami
spinnaker
spinnaker@ip-172-31-4-161:~$ 

```

## Installing halyard on your VM 

[Link](https://spinnaker.io/docs/setup/install/halyard/)

### Installing halyard 

```
spinnaker@ip-172-31-4-161:~$ curl -O https://raw.githubusercontent.com/spinnaker/halyard/master/install/debian/InstallHalyard.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7898  100  7898    0     0  80749      0 --:--:-- --:--:-- --:--:-- 81422
spinnaker@ip-172-31-4-161:~$ ls
InstallHalyard.sh
spinnaker@ip-172-31-4-161:~$ sudo bash InstallHalyard.sh 
Halyard version will be 1.50.0 
Halyard will be downloaded from the spinnaker-community repository 
Halconfig will be stored at /home/spinnaker/.hal/config
Uninstall script is located at /usr/local/bin/uninstall-halyard.sh
Installing Java...
Hit:1 http://us-west-2.ec2.archive.


Would you like to configure halyard to use bash auto-completion? [default=Y]: 

Where is your bash RC? [default=/root/.bashrc]: /home/spinnaker/.bashrc
Bash auto-completion configured.
To use the auto-completion either restart your shell, or run
. /home/spinnaker/.bashrc
Halyard version: 1.50.0
```


### checking halyard version 

```
spinnaker@ip-172-31-4-161:~$ hal -v
1.50.0
```

### Understanding more about Halyard --

### log directory 

```
spinnaker@ip-172-31-4-161:~$ ls  /var/log/
alternatives.log  auth.log  cloud-init-output.log  dmesg     journal    lastlog    syslog                      wtmp
amazon            btmp      cloud-init.log         dmesg.0   kern.log   private    ubuntu-advantage-timer.log
apt               chrony    dist-upgrade           dpkg.log  landscape  spinnaker  unattended-upgrades
spinnaker@ip-172-31-4-161:~$ ls  /var/log/spinnaker/
halyard
spinnaker@ip-172-31-4-161:~$ ls  /var/log/spinnaker/halyard/
spinnaker@ip-172-31-4-161:~$ 


```

### configuration details 

```
spinnaker@ip-172-31-4-161:~$ ls  /opt/
halyard  spinnaker
spinnaker@ip-172-31-4-161:~$ ls  /opt/halyard/
bin  config  lib
spinnaker@ip-172-31-4-161:~$ ls  /opt/halyard/config/
halyard.yml
spinnaker@ip-172-31-4-161:~$ ls  /opt/spinnaker/
config
spinnaker@ip-172-31-4-161:~$ ls  /opt/spinnaker/config/
halyard-user  halyard.yml
spinnaker@ip-172-31-4-161:~$ cat  /opt/spinnaker/config/halyard-user 
spinnaker
spinnaker@ip-172-31-4-161:~$ 
```

### choose env to install spinnaker components 

```
spinnaker@ip-172-31-4-161:~$ hal config deploy  edit --type localdebian 
+ Get current deployment
  Success
+ Get the deployment environment
  Success
- No changes supplied.
spinnaker@ip-172-31-4-161:~$ 

```

### choosing spinnaker storage 

<img src="storage.png">

### configure halyard to add s3 

```
hal config storage s3 edit  --access-key-id  AKIA25YZWFYDTRSR6V6K  --secret-access-key  --region  us-west-2  --bucket ashutoshhspinnaker-storage 
Your AWS Secret Key.: 
+ Get current deployment
  Success
+ Get persistent store
  Success
+ Edit persistent store
  Success
Validation in default.persistentStorage:
- WARNING Your deployment will most likely fail until you configure
  and enable a persistent store.

Validation in default:
- WARNING You have not yet selected a version of Spinnaker to
  deploy.
? Options include: 
  - 1.28.1
  - 1.27.1
  - 1.26.7
  - 1.25.7
  - 1.24.6
  - 1.23.7

+ Successfully edited persistent store "s3".
spinnaker@ip-172-31-4-161:~$ 

```



