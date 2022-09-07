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


### changing storage type 

```
spinnaker@ip-172-31-4-161:~$ hal config storage edit --type s3
+ Get current deployment
  Success
+ Get persistent storage settings
  Success
+ Edit persistent storage settings
  Success
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

+ Successfully edited persistent storage.
```

### choosing spinnaker version 

```
spinnaker@ip-172-31-4-161:~$ hal version list 
+ Get current deployment
  Success
+ Get Spinnaker version
  Success
+ Get released versions
  Success
+ You are on version "", and the following are available:
 - 1.23.7 (Hemlock Grove):
   Changelog: https://gist.github.com/spinnaker-release/94280a2b615adccd975eed73359023ac
   Published: Fri Feb 19 01:25:30 UTC 2021
   (Requires Halyard >= 1.32.0)
 - 1.24.6 (Queen's Gambit):
   Changelog: https://gist.github.com/spinnaker-release/25abcd046795c6f34bb2d8d4977f0415
   Published: Thu Jul 01 20:40:34 UTC 2021
   (Requires Halyard >= 1.32)
 - 1.25.7 (Chilling Adventures of Sabrina):
   Changelog: https://gist.github.com/spinnaker-release/83c1a4069029bfd79d11ad8e8bf1d326
   Published: Fri Jul 02 21:23:23 UTC 2021
   (Requires Halyard >= 1.41)
```

### setting version and deploy spinnaker components
```
spinnaker@ip-172-31-4-161:~$ hal  config version edit --version 1.27.1
+ Get current deployment
  Success
+ Edit Spinnaker version
  Success
+ Spinnaker has been configured to update/install version "1.25.7".
  Deploy this version of Spinnaker with `hal deploy apply`.
spinnaker@ip-172-31-4-161:~$ sudo hal  deploy apply  
```

### if we face package level error then we can use 

```
dpkg configure -a 
sudo hal  deploy apply 
```

### halyard installed all the components that we can check using 

```
 59  sudo systemctl status  apache2
   60  sudo systemctl status  gate
   61  sudo systemctl status  orca
   62  history 
   63  sudo systemctl status  igor
   64  sudo systemctl status  rosco 
```

### By default deck and other services are configure in localhost so we need to edit this 

```
 73  hal config security ui edit --override-base-url "http://34.215.91.255:9000"
   74  hal config security api  edit --override-base-url "http://34.215.91.255:8084"
   75  sudo hal deploy apply 

```

###  restart daemon 

```
sudo systemctl daemon-reload 
sudo systemctl restart halyard 
```

## Note: in few version of spinnaker security edit changes may not work so we have to change at component level it self 

### in some case these are the things 

```
spinnaker@ip-172-31-4-161:~$ cd /opt/
spinnaker@ip-172-31-4-161:/opt$ ls
clouddriver  deck  echo  front50  gate  halyard  igor  orca  rosco  spinnaker  spinnaker-monitoring
spinnaker@ip-172-31-4-161:/opt$ cd  spinnaker
spinnaker@ip-172-31-4-161:/opt/spinnaker$ ls
config
spinnaker@ip-172-31-4-161:/opt/spinnaker$ cd config/
spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ ls
clouddriver.yml  front50.yml  halyard-user  igor.yml  rosco.yml
echo.yml         gate.yml     halyard.yml   orca.yml  spinnaker.yml
spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ sudo vim spinnaker.yml 

==== Only change Deck part 

----
spinnaker@ip-172-31-4-161:~$ cd /opt/spinnaker
spinnaker@ip-172-31-4-161:/opt/spinnaker$ ls
config
spinnaker@ip-172-31-4-161:/opt/spinnaker$ cd config/
spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ ls
clouddriver.yml  front50.yml  halyard-user  igor.yml  rosco.yml
echo.yml         gate.yml     halyard.yml   orca.yml  spinnaker.yml
spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ vim spinnaker.yml 
spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ head -20 spinnaker.yml 
## WARNING
## This file was autogenerated, and _will_ be overwritten by Halyard.
## Any edits you make here _will_ be lost.

services:
  clouddriver:
    host: localhost
    port: 7002
    baseUrl: http://localhost:7002
    enabled: true
  deck:
    host: 0.0.0.0
    port: 9000
    baseUrl: http://34.215.91.255:9000
    enabled: true


----


spinnaker@ip-172-31-4-161:/opt/spinnaker/config$ cd /etc/apache2/
spinnaker@ip-172-31-4-161:/etc/apache2$ ls
apache2.conf    conf-enabled  magic           mods-enabled  ports.conf       sites-enabled
conf-available  envvars       mods-available  passphrase    sites-available
spinnaker@ip-172-31-4-161:/etc/apache2$ vim ports.conf 
spinnaker@ip-172-31-4-161:/etc/apache2$ sudo vim ports.conf 
spinnaker@ip-172-31-4-161:/etc/apache2$ cd sites-enabled/
spinnaker@ip-172-31-4-161:/etc/apache2/sites-enabled$ ls
spinnaker.conf
spinnaker@ip-172-31-4-161:/etc/apache2/sites-enabled$ sudo vim spinnaker.conf 
spinnaker@ip-172-31-4-161:/etc/apache2/sites-enabled$ cd
spinnaker@ip-172-31-4-161:~$ 
spinnaker@ip-172-31-4-161:~$ sudo systemctl restart  apache2 gate orca igor rosco.service  front50 echo  clouddriver.service   redis-server.service
spinnaker@ip-172-31-4-161:~$ sudo netstat -nlpt
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:9000            0.0.0.0:*               LISTEN      30498/apache2       
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      26692/sshd: /usr/sb 
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      26731/systemd-resol 
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN      30489/redis-server  
tcp6       0      0 :::22                   :::*                    LISTEN      26692/sshd: /usr/sb 
tcp6       0      0 :::8064                 :::*                    LISTEN      3818/java           
tcp6       0      0 ::1:6379                :::*                    LISTEN      30489/redis-server  
spinnaker@ip-172-31-4-161:~$  sudo systemctl daemon-reload
```




