---
layout: post
title: Please secure your server
---

lot's of people seems to overlook the importance of security.

This "guide" should give you an idea of the minimum requirements when it comes to linux server security.

I will focus on remote vulnerabilities and will not talk about physical attack vector.

You will find the sources at the end.

---

## 0. Table of content

1. ssh Security 101
2. Ensure software is up to date
3. Password policy
4. Firewalls
5. Lynis

## 1. ssh Security 101

the ssh daemon config file is situated in : `/etc/ssh/sshd_config`

### Change default ssh port

> Bots might try to access your server on port 22. Changing port will allow you to distinguish them from actual attackers.

Head into your sshd config file

```bash
#from Port 22
Port <your port>
```

### Disable root login

> Root login into your server is a fatal vulnerability and should be disabled at all cost (make sure you've created another account first)

Head into your sshd config file

```bash
#from AllowRootLogin yes to
PermitRootLogin no
```



### Terminate afk users

> afk users with an ssh session opened on there desktop is a vulnerability as malicious users could access your server while your away

Head into your sshd config file

```bash
ClientAliveInterval 300
```



### Add a legal disclaimer banner

> Give a last warning to any intruders is a good idea, it might even scare off some people.

Edit the login banner file :

```bash
vim /etc/issue
```

this banner is from the [cisco security baseline](https://cisco.com/c/en/us/td/docs/solutions/Enterprise/Security/Baseline_Security/securebasebook/appendxA.html) but feel free to choose another one.

```
UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED                                   
You must have explicit, authorized permission to access or configure this device.        
Unauthorized attempts and actions to access or use this system may result in civil and/or
criminal penalties.                                                                      
All activities performed on this device are logged and monitored. 
```

 

### Disable password login

> Password can be cracked, ssh keys can't

If your not familiar with how to create an ssh key pair [read this first](https://ssh.com/ssh/keygen/)

with your key working head into your sshd config file

```bash
PasswordAuthentication no

#we might as well disable empty passwords
PermitEmptyPassword no
```



## 2. Ensure software is up to date

I think you can figure that one on your own. just remember that the more software you add the more vulnerable your server.



## 3. Password policy

Having a strong pasword policy is key to secure your linux device.

### Set password expiration

>to enforce a strong password policy it is recomended to change passwords regularly.

edit `/etc/login.defs`

```bash
#set password warning age
PASS_WARN_AGE 7

#set password minimum age
PASS_MIN_DAYS 7

#set password maximum age
PASS_MAX_DAYS 90
```

You could also set an account expiration limit in `/etc/default/useradd` 

```bash
INACTIVE=30
```



### Set password requirements

> Minimum password quality requierments are an essential factor to prevent from bruteforce attacks.

Password quality settings should be located in `/etc/security/pwquality.conf`

```bash
#set password minimum lenght
minlen = 14

#enforce digits
dcredit = -1

#enforce uppercase
ucredit = -1

#enfore lowercase
lcredit = -1

#enforce special caracter
ocredit = -1
```

 

## 4. Firewall

You should use a firewall to block unused ports. The default firewall solution on linux is **Iptables** but users generaly don't configure iptables directy and instead use another firewall.

Ubuntu has a preinstalled firewall called **ufw** (uncomplicated firewall) check [this link](http://doc.ubuntu-fr.org/ufw) on how to config.

On Centos the main option is **Firewalld** it is preinstalled on RHEL and fedora. check [this link](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-8) on how to config.



## 5. Use Lynis

[Lynis](https://cisofy.com/lynis/) allow you to audit your system for security flaws.

### Install and run Lynis

```bash
git clone https://github.com/CISOfy/lynis

cd lynis

./lynis audit system
```

Lynis will scan your device and prompt you with advice.

***

## sources

- [CIS Benchmark](https://cisecurity.org/cis-benchmarks/) (Center of Internet Security)
- [DevSec Hardening Framework](https://dev-sec.io/)
