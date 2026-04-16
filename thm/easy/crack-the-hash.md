> [!note]
>
> - **Challenge:** Crack the hash
> - **Dificuldade:** Easy
> - **Plaraforma:** TryHackMe
> - **Link:** https://tryhackme.com/room/crackthehash 
> - **Autor:** hi im kvothe

# Writeup

Essa room consiste em vários que hashes que devem ser quebrados. Para isso, foram usadas as ferramentas `hashid` para identificação dos algoritmos e `hashcat` e `john` para a quebra. Além disso, a documentação do hashcat foi usada na identificação dos algorítmos que não foram possíveis de seremidentificados apenas com o hashid.

## Level 1

### Hash 1: 48bb6e862e54f2a795ffc4e541caed4d

- **Algoritmo:** MD5

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl1-hash1.txt                    
--File 'lvl1-hash1.txt'--
Analyzing '48bb6e862e54f2a795ffc4e541caed4d'
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
--End of file 'lvl1-hash1.txt'-- 
```

- **Mensagem:** easy

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt lvl1-hash1.txt 
Created directory: /home/kali/.john
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
easy             (?)     
1g 0:00:00:00 DONE (2026-04-16 22:24) 20.00g/s 3448Kp/s 3448Kc/s 3448KC/s florida69..eagames
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 
```

### Hash 2: CBFDAC6008F9CAB4083784CBD1874F76618D2A97 

- **Algoritmo:** SHA-1

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl1-hash2.txt                                                     
--File 'lvl1-hash2.txt'--
Analyzing 'CBFDAC6008F9CAB4083784CBD1874F76618D2A97'
[+] SHA-1 [Hashcat Mode: 100][JtR Format: raw-sha1]
[+] Double SHA-1 [Hashcat Mode: 4500]
[+] RIPEMD-160 [Hashcat Mode: 6000][JtR Format: ripemd-160]
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn [Hashcat Mode: 190][JtR Format: raw-sha1-linkedin]
[+] Skein-256(160) 
[+] Skein-512(160) 
--End of file 'lvl1-hash2.txt'-- 
```

- **Mensagem:** password123

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt lvl1-hash2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA1 [SHA1 256/256 AVX2 8x])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
password123      (?)     
1g 0:00:00:00 DONE (2026-04-16 22:25) 33.33g/s 46133p/s 46133c/s 46133C/s jesse..password123
Use the "--show --format=Raw-SHA1" options to display all of the cracked passwords reliably
Session completed. 

```

### Hash 3: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

- **Algoritmo:** SHA-256

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl1-hash3.txt                                                      
--File 'lvl1-hash3.txt'--
Analyzing '1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032'
[+] Snefru-256 [JtR Format: snefru-256]
[+] SHA-256 [Hashcat Mode: 1400][JtR Format: raw-sha256]
[+] RIPEMD-256 
[+] Haval-256 [JtR Format: haval-256-3]
[+] GOST R 34.11-94 [Hashcat Mode: 6900][JtR Format: gost]
[+] GOST CryptoPro S-Box 
[+] SHA3-256 [Hashcat Mode: 5000][JtR Format: raw-keccak-256]
[+] Skein-256 [JtR Format: skein-256]
[+] Skein-512(256) 
--End of file 'lvl1-hash3.txt'--  
```

- **Mensagem:** letmein

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=raw-sha256 --incremental=Lower --min-length=7 --max-length=7 lvl1-hash3.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=16
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (?)     
1g 0:00:00:00 DONE (2026-04-16 22:28) 8.333g/s 13107Kp/s 13107Kc/s 13107KC/s brytuar..lyziesi
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed. 
```

### Hash 4: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

- **Algoritmo:** bcrypt

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl1-hash4.txt                                                               
--File 'lvl1-hash4.txt'--
Analyzing '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom'
[+] Blowfish(OpenBSD) [Hashcat Mode: 3200][JtR Format: bcrypt]
[+] Woltlab Burning Board 4.x 
[+] bcrypt [Hashcat Mode: 3200][JtR Format: bcrypt]
--End of file 'lvl1-hash4.txt'-- 
```

- **Mensagem:** bleh

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=bcrypt --incremental=Lower --min-length=4 --max-length=4 lvl1-hash4.txt
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 4096 for all loaded hashes
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
bleh             (?)     
1g 0:00:00:07 DONE (2026-04-16 22:29) 0.1312g/s 132.2p/s 132.2c/s 132.2C/s mere..jeeh
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

### Hash 5: 279412f945939ba78ce0758d3fd83daa

- **Algoritmo:** MD4

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl1-hash5.txt                                                          
--File 'lvl1-hash5.txt'--
Analyzing '279412f945939ba78ce0758d3fd83daa'
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
--End of file 'lvl1-hash5.txt'--
```

- **Mensagem:** Eternity22

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=raw-md4 --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 lvl1-hash5.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD4 [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
Eternity22       (?)     
1g 0:00:00:02 DONE (2026-04-16 22:31) 0.4901g/s 19739Kp/s 19739Kc/s 19739KC/s Fgsltw..Estercita
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

## Level 2


### Hash 1: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

- **Algoritmo:** SHA-256

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl2-hash1.txt                                                                   
--File 'lvl2-hash1.txt'--
Analyzing 'F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85'
[+] Snefru-256 [JtR Format: snefru-256]
[+] SHA-256 [Hashcat Mode: 1400][JtR Format: raw-sha256]
[+] RIPEMD-256 
[+] Haval-256 [JtR Format: haval-256-3]
[+] GOST R 34.11-94 [Hashcat Mode: 6900][JtR Format: gost]
[+] GOST CryptoPro S-Box 
[+] SHA3-256 [Hashcat Mode: 5000][JtR Format: raw-keccak-256]
[+] Skein-256 [JtR Format: skein-256]
[+] Skein-512(256) 
--End of file 'lvl2-hash1.txt'--
```

- **Mensagem:** paule

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=raw-sha256 --incremental=Lower --min-length=5 --max-length=5 lvl2-hash1.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=16
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
paule            (?)     
1g 0:00:00:00 DONE (2026-04-16 22:34) 20.00g/s 5242Kp/s 5242Kc/s 5242KC/s angel..nuven
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed.
```

### Hash 2: 1DFECA0C002AE40B8619ECF94819CC1B

- **Algoritmo:** NTML

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl2-hash2.txt                                                               
--File 'lvl2-hash2.txt'--
Analyzing '1DFECA0C002AE40B8619ECF94819CC1B'
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
--End of file 'lvl2-hash2.txt'--
```

- **Mensagem:** n63umy9lkf4i

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 lvl2-hash2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (NT [MD4 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
n63umy8lkf4i     (?)     
1g 0:00:00:00 DONE (2026-04-16 23:33) 1.538g/s 8060Kp/s 8060Kc/s 8060KC/s n6546442..n6014340431p
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed.
```

### Hash 3: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02

- **Salt:** aReallyHardSalt

- **Algoritmo:** SHA-512 Crypt

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl2-hash3.txt                                                                    
--File 'lvl2-hash3.txt'--
Analyzing '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'
[+] SHA-512 Crypt [Hashcat Mode: 1800][JtR Format: sha512crypt]
--End of file 'lvl2-hash3.txt'--  
```

- **Mensagem:** waka99

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ john --format=sha512crypt --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 lvl2-hash3.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
waka99           (?)     
1g 0:00:05:58 DONE (2026-04-16 23:43) 0.002787g/s 7896p/s 7896c/s 7896C/s walayon..waite2010
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

### Hash 4: e5d8870e5bdd26602cab8dbe07a942c8669e56d6

- **Salt:** tryhackme

- **Algoritmo:** HMAC-SHA1 (tentaiva e erro e wiki do hashcat)

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashid -j -m lvl2-hash4.txt                                                                        
--File 'lvl2-hash4.txt'--
Analyzing 'e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme'
[+] SHA-1 [Hashcat Mode: 100][JtR Format: raw-sha1]
[+] Double SHA-1 [Hashcat Mode: 4500]
[+] RIPEMD-160 [Hashcat Mode: 6000][JtR Format: ripemd-160]
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn [Hashcat Mode: 190][JtR Format: raw-sha1-linkedin]
[+] Skein-256(160) 
[+] Skein-512(160) 
[+] SMF ≥ v1.1 [Hashcat Mode: 121]
--End of file 'lvl2-hash4.txt'-- 
```

- **Mensagem:** 481616481616

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/crack-the-hash]
└─$ hashcat -a 0 -m 160 lvl2-hash4.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i5-1340P, 6816/13633 MB (2048 MB allocatable), 16MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 66

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 516 MB (11620 MB free)

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 946729410
* Runtime...: 0 secs

Cracking performance lower than expected?                 

* Append -O to the commandline.
  This lowers the maximum supported password/salt length (usually down to 32).

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme:481616481616
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 160 (HMAC-SHA1 (key = $salt))
Hash.Target......: e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme
Time.Started.....: Thu Apr 16 23:52:59 2026 (24 secs)
Time.Estimated...: Thu Apr 16 23:53:23 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best66.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........: 33875.6 kH/s (8.68ms) @ Accel:574 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 812820736/946729410 (85.86%)
Rejected.........: 0/812820736 (0.00%)
Restore.Point....: 12306560/14344385 (85.79%)
Restore.Sub.#01..: Salt:0 Amplifier:0-64 Iteration:0-64
Candidate.Engine.: Device Generator
Candidates.#01...: 48480lovesucks -> 2269
Hardware.Mon.#01.: Temp: 88c Util: 78%

Started: Thu Apr 16 23:52:38 2026
Stopped: Thu Apr 16 23:53:25 2026

```
