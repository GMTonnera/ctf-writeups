
> [!note]
>
> - **Challenge:** Oopsie
> - **Dificuldade:** Very easy
> - **Plaraforma:** HackTheBox
> - **Link:** https://app.hackthebox.com/machines/Oopsie?sort_by=created_at&sort_type=desc
> - **Autor:** hi im kvothe

# Writeup

## 1. Etapa de Reconhecimento

### 1.1 Enumeração de Portas

Ao spawnar máquina e receber o endereço IP `<TARGET_IP>` da máquina alvo, foi feito uma enumeração dos serviços que estão hospedados na máquina, por meio do comando:

```bash
nmap -T4 --open -sV <TARGET_IP>
```
obtendo o seguinte output
```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2026-04-13 16:29 UTC
Nmap scan report for 10.129.108.17
Host is up (0.17s latency).
Not shown: 986 closed tcp ports (reset), 12 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.35 seconds
```
O output indica que a máquina está hospedando um servidor **SSH** na porta 22 e um servidor **HTTP** na porta 80.

### 1.3 Servidor HTTP

Ao realizar uma enumeração de diretórios no servidor HTTP com o comando
```bash
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirb/common.txt -t 50 -x txt,php,js
```
obtemos o output
```bash
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://<TARGET_IP>
[+] Method:                  GET
[+] Threads:                 50
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Extensions:              txt,php,js
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htaccess.php        (Status: 403) [Size: 278]
/.htpasswd.js         (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.htpasswd.php        (Status: 403) [Size: 278]
/.hta.js              (Status: 403) [Size: 278]
/.htaccess.txt        (Status: 403) [Size: 278]
/.htaccess.js         (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.hta.txt             (Status: 403) [Size: 278]
/.hta.php             (Status: 403) [Size: 278]
/.htpasswd.txt        (Status: 403) [Size: 278]
/css                  (Status: 301) [Size: 312] [--> http://<TARGET_IP>/css/]
/fonts                (Status: 301) [Size: 314] [--> http://<TARGET_IP>/fonts/]
/images               (Status: 301) [Size: 315] [--> http://<TARGET_IP>/images/]
/index.php            (Status: 200) [Size: 10932]
/index.php            (Status: 200) [Size: 10932]
/js                   (Status: 301) [Size: 311] [--> http://<TARGET_IP>/js/]
/server-status        (Status: 403) [Size: 278]
/themes               (Status: 301) [Size: 315] [--> http://<TARGET_IP>/themes/]
/uploads              (Status: 301) [Size: 316] [--> http://<TARGET_IP>/uploads/]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```
Ao analisar o output, não descobrimos nenhum endpoint relevante, uma vez que todos os endpoints encontrados estão protegidos. Porém, ao analisar o código fonte da página `index.php`, encontramos a tag `<script src="/cdn-cgi/login/script.js"></script>` na linha 474, a qual da indícios de uma página de login no endpoint `/cdn-cgi/login`. Ao acessar esse diretório pelo navegador, conclui-se que o endpoint em questão é de fato uma página de login. Além disso, essa página de login nos permite realizar o login no modo visitante (usuário `guest`).

## 2. Etapa de Exploração

### 2.1 Parâmetros da URL
Ao efetuar o login na página no modo visitante, o usuário somos redirecionados para uma página de backoffice com vários abas. Na aba "Account", conseguimos visualizar informações sobre o usuário usado no login. Entretanto, ao alterar os parâmetros de query da URL da página, é possível visualizar as informações de outros usuários. Dessa forma, ao alterar o valor so parâmetro `id` para 1, temos acesso às informações do usuário `admin`, dentre elas o ID `<ADMIN_ID>`.

### 2.2 Cookies
Ao acessar a aba "Uploads", recebemos uma mensagem indicando que é necessário privilégios de administrador para acessar a funcionalidade da página. Entretanto, ao alterar o valor do ID do usuário `guest` para o id do usuário `admin` nos cookies do site pelo navegador, é possível acessar a funcionalidade de upload de arquivos da aba.

### 2.3 Reverse shell
Com a funcionalidade de upload de arquivos acessível, verificamos que é possível enviar arquivos `.php`, porém também verificamos que não é possível acessar os arquivos submetidos pelo endpoint `/uploads`. Para acessá-los, é necessário acessar o arquivo diretamente com `/uploads/<FILENAME>`.

Assim, utilizamos o script (reverse-shell.php)[https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php] para executar uma shell reversa e  obter acesso à máquina alvo.  

## 3. Escalação de Privilégio

Uma vez na máquina, ao explorar os arquivos do endpoint `/cdn-cgi/login` do servidor HTTP, nos deparamos com o arquivo `db.php`. Esse arquivo contem credenciais que podem ser usadas para acessar o usuário `<USERNAME>`.  Ao acessar o usuário `<USERNAME>` com as credenciais encontradas via SSH, encontramos a flag de usuaŕio no diretório `/home/<USERNAME>`

Para escalar privilégios, utilizamos o comando 
```bash
find / -user root -perm -4000 2>/dev/null
```
afim de verificar quais arquivos estão com SUID setado. O output do comando
```bash
/snap/core/11420/bin/mount
/snap/core/11420/bin/ping
/snap/core/11420/bin/ping6
/snap/core/11420/bin/su
/snap/core/11420/bin/umount
/snap/core/11420/usr/bin/chfn
/snap/core/11420/usr/bin/chsh
/snap/core/11420/usr/bin/gpasswd
/snap/core/11420/usr/bin/newgrp
/snap/core/11420/usr/bin/passwd
/snap/core/11420/usr/bin/sudo
/snap/core/11420/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/11420/usr/lib/openssh/ssh-keysign
/snap/core/11420/usr/lib/snapd/snap-confine
/snap/core/11420/usr/sbin/pppd
/snap/core/11743/bin/mount
/snap/core/11743/bin/ping
/snap/core/11743/bin/ping6
/snap/core/11743/bin/su
/snap/core/11743/bin/umount
/snap/core/11743/usr/bin/chfn
/snap/core/11743/usr/bin/chsh
/snap/core/11743/usr/bin/gpasswd
/snap/core/11743/usr/bin/newgrp
/snap/core/11743/usr/bin/passwd
/snap/core/11743/usr/bin/sudo
/snap/core/11743/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/11743/usr/lib/openssh/ssh-keysign
/snap/core/11743/usr/lib/snapd/snap-confine
/snap/core/11743/usr/sbin/pppd
/bin/fusermount
/bin/umount
/bin/mount
/bin/ping
/bin/su
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/newuidmap
/usr/bin/passwd
/usr/bin/bugtracker
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/traceroute6.iputils
/usr/bin/newgidmap
/usr/bin/gpasswd
/usr/bin/sudo
```
indica um programa incomum: `bugtracker`. Ao executar esse programa, somos solicitados a inserir um `ID` e o programa retorna um relatório do bug referente ao `ID` especificado. Porém, quando especificamos um `ID` inexistente, ele retorna a mensagem de erro 
```bash
cat: /root/reports/<ID>: No such file or directory
```
Assim, se passarmos como `ID` o payload `../root.txt`, o programa retona a flag de root.
