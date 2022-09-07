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





