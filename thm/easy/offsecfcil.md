> [!note]
>
> - **Challenge:** OFFSEC - Hash
> - **Dificuldade:** Easy
> - **Plaraforma:** TryHackMe
> - **Link:** https://tryhackme.com/room/offsecfcil 
> - **Autor:** hi im kvothe

# Writeup

Essa room consiste em vários que hashes que devem ser quebrados. Para isso, foram usadas as ferramentas `hashid` para identificação dos algoritmos e `hashcat` e `john` para a quebra. Além disso, a documentação do hashcat foi usada na identificação dos algorítmos que não foram possíveis de seremidentificados apenas com o hashid.

### Hash 1: 482c811da5d5b4bc6d497ffa98491e38

- **Algoritmo:** MD5

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashid -m -j hash1.txt
--File 'hash1.txt'--
Analyzing '482c811da5d5b4bc6d497ffa98491e38'
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
--End of file 'hash1.txt'-- 
```

- **Mensagem:** password123

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 hash1.txt         
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
password123      (?)     
1g 0:00:00:00 DONE (2026-04-17 14:33) 1.136g/s 1745p/s 1745c/s 1745C/s 753951..mexico1
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 
```

### Hash 2: 861c4f67e887dec85292d36ab05cd7a1a7275228 

- **Algoritmo:** SHA-1

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashid -m -j hash2.txt                                                                    
--File 'hash2.txt'--
Analyzing '861c4f67e887dec85292d36ab05cd7a1a7275228'
[+] SHA-1 [Hashcat Mode: 100][JtR Format: raw-sha1]
[+] Double SHA-1 [Hashcat Mode: 4500]
[+] RIPEMD-160 [Hashcat Mode: 6000][JtR Format: ripemd-160]
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn [Hashcat Mode: 190][JtR Format: raw-sha1-linkedin]
[+] Skein-256(160) 
[+] Skein-512(160) 
--End of file 'hash2.txt'--
```

- **Mensagem:** easy

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 hash2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA1 [SHA1 256/256 AVX2 8x])
Warning: no OpenMP support for this hash type, consider --fork=16
Press 'q' or Ctrl-C to abort, almost any other key for status
easy             (?)     
1g 0:00:00:00 DONE (2026-04-17 14:35) 2.127g/s 366825p/s 366825c/s 366825C/s easymoney..easley
Use the "--show --format=Raw-SHA1" options to display all of the cracked passwords reliably
Session completed.
```

### Hash 3: 4149c5cc4c378444d116d65ad5ba4099

- **Algoritmo:** MD4

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashid -m -j hash3.txt                                                                     
--File 'hash3.txt'--
Analyzing '4149c5cc4c378444d116d65ad5ba4099'
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
--End of file 'hash3.txt'--
```

- **Mensagem:** 0ffs3c

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashcat -a 3 -m 900 hash3.txt -1 ?l?d ?1?1?1?1?1?1
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i5-1340P, 6816/13633 MB (2048 MB allocatable), 16MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Brute-Force
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 516 MB (11852 MB free)

4149c5cc4c378444d116d65ad5ba4099:0ffs3c                   
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 900 (MD4)
Hash.Target......: 4149c5cc4c378444d116d65ad5ba4099
Time.Started.....: Fri Apr 17 14:42:23 2026 (2 secs)
Time.Estimated...: Fri Apr 17 14:42:25 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Mask.......: ?1?1?1?1?1?1 [6]
Guess.Charset....: -1 ?l?d, -2 N/A, -3 N/A, -4 N/A, -5 N/A, -6 N/A, -7 N/A, -8 N/A 
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   429.9 MH/s (7.96ms) @ Accel:349 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 772825600/2176782336 (35.50%)
Rejected.........: 0/772825600 (0.00%)
Restore.Point....: 591904/1679616 (35.24%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1024 Iteration:0-1024
Candidate.Engine.: Device Generator
Candidates.#01...: sa6n3c -> hvff7b
Hardware.Mon.#01.: Temp: 71c Util: 87%

Started: Fri Apr 17 14:42:11 2026
Stopped: Fri Apr 17 14:42:26 2026
```

### Hash 4: cdeb746ec095149627348b61d4140fc58b745875

- **Salt:** satech

- **Algoritmo:** HMAC-SHA1

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashid -m -j hash4.txt                                                            
--File 'hash4.txt'--
Analyzing 'cdeb746ec095149627348b61d4140fc58b745875:satech'
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
--End of file 'hash4.txt'--
```

- **Mensagem:** ovelha

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashcat -a 0 -m 160 cdeb746ec095149627348b61d4140fc58b745875:satech /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
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

Host memory allocated for this attack: 516 MB (11419 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 946729410

cdeb746ec095149627348b61d4140fc58b745875:satech:ovelha    
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 160 (HMAC-SHA1 (key = $salt))
Hash.Target......: cdeb746ec095149627348b61d4140fc58b745875:satech
Time.Started.....: Fri Apr 17 15:07:38 2026 (0 secs)
Time.Estimated...: Fri Apr 17 15:07:38 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best66.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........: 38732.8 kH/s (6.83ms) @ Accel:532 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 6724480/946729410 (0.71%)
Rejected.........: 0/6724480 (0.00%)
Restore.Point....: 93632/14344385 (0.65%)
Restore.Sub.#01..: Salt:0 Amplifier:0-64 Iteration:0-64
Candidate.Engine.: Device Generator
Candidates.#01...: bethann1 -> co06
Hardware.Mon.#01.: Temp: 51c Util: 36%

Started: Fri Apr 17 15:07:38 2026
Stopped: Fri Apr 17 15:07:40 2026
```

### Hash 5: 362fda2183b7ac73400a83f6ab2c359451e48adf6c3d46a2963ee2abdf852912

- **Algoritmo:** SHA-256

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashid -m -j hash5.txt                                                                                                                      
--File 'hash5.txt'--
Analyzing '362fda2183b7ac73400a83f6ab2c359451e48adf6c3d46a2963ee2abdf852912'
[+] Snefru-256 [JtR Format: snefru-256]
[+] SHA-256 [Hashcat Mode: 1400][JtR Format: raw-sha256]
[+] RIPEMD-256 
[+] Haval-256 [JtR Format: haval-256-3]
[+] GOST R 34.11-94 [Hashcat Mode: 6900][JtR Format: gost]
[+] GOST CryptoPro S-Box 
[+] SHA3-256 [Hashcat Mode: 5000][JtR Format: raw-keccak-256]
[+] Skein-256 [JtR Format: skein-256]
[+] Skein-512(256) 
--End of file 'hash5.txt'--
```

- **Mensagem:** sawctf

```bash
┌──(kali㉿kali)-[~/Documents/ctfs/thm/offsecfcil]
└─$ hashcat -a 3 -m 1400 hash5.txt -1 ?l?d ?1?1?1?1?1?1                                                                                         
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i5-1340P, 6816/13633 MB (2048 MB allocatable), 16MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Brute-Force
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 516 MB (10710 MB free)

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

362fda2183b7ac73400a83f6ab2c359451e48adf6c3d46a2963ee2abdf852912:sawctf
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1400 (SHA2-256)
Hash.Target......: 362fda2183b7ac73400a83f6ab2c359451e48adf6c3d46a2963...852912
Time.Started.....: Fri Apr 17 15:10:40 2026 (13 secs)
Time.Estimated...: Fri Apr 17 15:10:53 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Mask.......: ?1?1?1?1?1?1 [6]
Guess.Charset....: -1 ?l?d, -2 N/A, -3 N/A, -4 N/A, -5 N/A, -6 N/A, -7 N/A, -8 N/A 
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   129.7 MH/s (9.92ms) @ Accel:119 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 1711985408/2176782336 (78.65%)
Rejected.........: 0/1711985408 (0.00%)
Restore.Point....: 1319472/1679616 (78.56%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1024 Iteration:0-1024
Candidate.Engine.: Device Generator
Candidates.#01...: sanahf -> hvfj2e
Hardware.Mon.#01.: Temp: 88c Util: 86%

Started: Fri Apr 17 15:10:28 2026
Stopped: Fri Apr 17 15:10:55 2026

```

