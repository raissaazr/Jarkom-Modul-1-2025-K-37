# Jarkom-Modul-1-2025-K-37
| Nama                         | NRP        |
| ---------------------------- | ---------- |
| Azaria Raissa Maulidinnisa   | 5027241043 |
| Daniswara Fausta Novanto     | 5027241050 |

## Soal 1
Buka host server 10.15.43.32 GNS3 yang disediakan. Kemudian buat 8 node baru dengan ketentuan 1 node untuk router (Eru), 1 node untuk internet, 2 node untuk switch, dan 4 node sisanya untuk klien (Melkor, Manwe, Varda, Ulmo). 

<img width="718" height="556" alt="Screenshot 2025-09-29 160716" src="https://github.com/user-attachments/assets/11205116-e2d2-4dc2-9432-821879c38982" />

Untuk mengecek apakah node sudah bisa diakses dapat dilakukan dengan klik ``start`` pada node router. Kemudian buka terminal linux dan ketik 
```telnet 10.15.43.32 5311```, dengan ``5311`` menunjukkan port yang digunakan, dapat diketahui di GNS, jika terhubung akan muncul output seperti berikut:

<img width="632" height="127" alt="Screenshot 2025-10-02 155143-bfr udhcp" src="https://github.com/user-attachments/assets/d1cc82ce-5259-4f01-8861-243723093c1f" />

Lakukan juga di setiap node klien. 

<img width="783" height="139" alt="Screenshot 2025-10-02 155346" src="https://github.com/user-attachments/assets/59593235-333a-4ada-b445-d90870208cba" />

<img width="656" height="195" alt="Screenshot 2025-10-02 155411" src="https://github.com/user-attachments/assets/c4549349-ccd6-4db8-90fc-fc10b65c787a" />

## Soal 2
Untuk menghubungkan router ke internet, edit konfigurasi milik router (Eru) menjadi seperti ini:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.82.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.82.2.1
	netmask 255.255.255.0
```
``auto eth0`` dan ``iface eth0 inet dhcp`` Ini adalah jalan keluar Eru ke internet, misal mendapatkan alamat IP, netmask, dan gateway secara otomatis dari router di luar GNS3. Kemudian lakukan ``ping google.com  -c 3`` dari router Eru. Jika berhasil akan muncul output sebagai berikut:

<img width="786" height="173" alt="Screenshot 2025-10-02 155914-pingEru" src="https://github.com/user-attachments/assets/c1b488fe-dbd8-440c-ad96-ae9388e06308" />

Hasil statistik memberikan informasi mengenai aktivitas ``ping`` yang dilakukan. Jika berhasil maka statistik akan memberikan informasi seperti di ganbar.  

## Soal 3
Untuk memastikan klien terhubung satu sama lain, kita dapat melakukan ``ping`` antar sesama klien, baik yang didalam satu subnet maupun beda subnet. Sebelum melakukan ping kita perlu mengetahui ip address dari setiap klien. 
### Melkor
```
auto eth0
iface eth0 inet static
	address 10.82.1.2
	netmask 255.255.255.0
	gateway 10.82.1.1
```
### Manwe
```
auto eth0
iface eth0 inet static
	address 10.82.1.3
	netmask 255.255.255.0
	gateway 10.82.1.1
```
### Varda
```
auto eth0
iface eth0 inet static
	address 10.82.2.2
	netmask 255.255.255.0
	gateway 10.82.2.1
```
### Ulmo
```
auto eth0
iface eth0 inet static
	address 10.82.2.3
	netmask 255.255.255.0
	gateway 10.82.2.1

```
Misal kita lakukan ``ping`` satu subnet dari Melkor ke Manwe dan dari Varda ke Ulmo.

<img width="647" height="178" alt="Screenshot 2025-10-02 160010" src="https://github.com/user-attachments/assets/2bb385eb-d732-49f7-ab95-f6148940845c" />

<img width="602" height="156" alt="Screenshot 2025-10-02 160104" src="https://github.com/user-attachments/assets/6e1a073a-27e9-4ce2-829e-ec8806738814" />

Jika statistik menunjukkan tidak ada ``packet-loss`` berarti setiap klien sudha terhubung. Selanjutnya lakukan ``ping`` antar subnet, misal Melkor ke Varda dan Ulmo ke Melkor.

<img width="599" height="174" alt="Screenshot 2025-10-02 160039" src="https://github.com/user-attachments/assets/596dd6f4-c132-40d7-b110-02044d997c2f" />

<img width="642" height="171" alt="Screenshot 2025-10-02 160152" src="https://github.com/user-attachments/assets/38accb51-035d-489c-a188-e312614c27e1" />

## Soal 4
Untuk menghubungkan klien ke internet, kita perlu mengedit konfigurasi pada Eru menjadi seperti ini:
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.82.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.82.2.1
	netmask 255.255.255.0

up apt update && apt install iptables -y
up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.82.0.0/16
```
``iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.82.0.0/16`` Perintah ini memungkinkan semua paket dari jaringan internal untuk "menyamar" (masquerade) menggunakan alamat IP publik Eru saat keluar melalui interface eth0. Kemudian ketik command ``cat /etc/resolv.conf`` dan lihat outputnya, simpan IP yang muncul karena IP tersebut merupakan IP DNS. 

<img width="377" height="39" alt="Screenshot 2025-10-02 160227" src="https://github.com/user-attachments/assets/819dda84-de5d-44e2-afbe-11b555851782" />

Selanjutnya ketik command ``echo nameserver 192.168.122.1 > /etc/resolv.conf`` di semua node klien dan coba lakukan ``ping`` ke google seperti sebelumnya. 

<img width="779" height="188" alt="Screenshot 2025-10-02 160307" src="https://github.com/user-attachments/assets/6176c3db-db00-481f-91f3-1cd251b9c911" />

<img width="789" height="195" alt="Screenshot 2025-10-02 160401" src="https://github.com/user-attachments/assets/02b44f52-f6db-41ab-a227-2f235c1308f6" />

## Soal 5 
Untuk memastikan agar konfigurasi tidak hilang saat melakukan restart, simpan setiap intall-an di bagian ``/root/.bahsrc`` atau tambahkan pada edit configuration.

<img width="610" height="83" alt="Screenshot 2025-10-02 160515" src="https://github.com/user-attachments/assets/a516d6d3-d663-43c1-a838-dc10da31bf55" />

<img width="423" height="65" alt="Screenshot 2025-10-02 160531" src="https://github.com/user-attachments/assets/1cd6d64a-e3a4-4aef-864d-ef1104daffad" />

## Soal 6 
Buat file ``traffic.sh`` di node Melkor, dengan menggunakan ``nano traffic.sh``.
```
#!/bin/bash
echo "Memulai pembuatan traffic jaringan selama 10 detik..."

cleanup() {
    echo ""
    echo "Menghentikan semua proses pembuat traffic..."
    kill $(jobs -p) > /dev/null 2>&1
    echo "Selesai."
}

trap cleanup EXIT

ping -i 0.1 8.8.8.8 > /dev/null 2>&1 &

wget -qO /dev/null http://speedtest.tele2.net/10MB.zip &
wget -qO /dev/null http://proof.ovh.net/files/10Mb.dat &

nmap -T4 -F scanme.nmap.org > /dev/null 2>&1 &

sleep 10
```
Setelah menyimpan file ``traffic.sh`` tersebut, ganti hak akses file tersebut menggunkan ``chmod +x traffic.sh`` agar file bisa dijalankan. Setelahnya start capture wireshark di antara Eru dan switch 1 dan jalankan file di Melkor ``./traffic.sh``. Lalu di wireshark terapkan filter capture ``ip.addr == 10.82.1.3`` karena tujuannya adalah melihat koneksi antara Manwe dan Eru. 

<img width="495" height="142" alt="Screenshot 2025-10-02 161006" src="https://github.com/user-attachments/assets/c4a107c4-9eba-4eeb-bbd2-f8280aadad04" />

Hasil capture filter menampilkan paket yang berasal dan menuju ke IP address Manwe.
<img width="1212" height="688" alt="Screenshot 2025-10-03 083126" src="https://github.com/user-attachments/assets/362a4f70-5151-4ad4-a452-5cda151913f0" />

## Soal 7 
Untuk membuat FTP Server di Eru kita perlu menginstall ``vsftpd``.

<img width="1059" height="368" alt="Screenshot 2025-10-02 162830" src="https://github.com/user-attachments/assets/a12ba52c-2ae0-4c5c-9929-72cb72ab8a69" />

Selanjutnya, buat direktori ``ftpshare`` dan konfigurasi dasar:

<img width="609" height="167" alt="Screenshot 2025-10-02 163130" src="https://github.com/user-attachments/assets/5a058e32-3316-4060-827f-734d38f96e73" />

``echo "local_enable=YES" >> /etc/vsftpd.conf `` Mengizinkan user Linux lokal (ainur, melkor) untuk login melalui FTP. ``echo "write_enable=YES" >> /etc/vsftpd.conf `` Mengizinkan operasi write di server secara umum. ``echo "chroot_local_user=YES" >> /etc/vsftpd.conf`` Setiap user lokal akan "terkunci" di direktori home mereka ``/home/ftpshare`` dan tidak dapat mengakses filesystem lainnya. ``echo "local_root=/home/ftpshare" >> /etc/vsftpd.conf`` Mengatur ``/home/ftpshare`` sebagai root direktori yang akan dilihat oleh semua user lokal setelah login. ``echo "anonymous_enable=NO" >> /etc/vsftpd.conf`` MEnonaktifkan Anonim dan memastikan bahwa hanya user terdaftar yang dapat login.

Buat user baru dengan ``useradd`` dan atur password masing-masing menggunakan ``passwd``.

<img width="383" height="96" alt="Screenshot 2025-10-02 163205" src="https://github.com/user-attachments/assets/d5d04a65-fa7d-4d6b-920c-61a01d5b08b8" />
<img width="422" height="95" alt="Screenshot 2025-10-02 163243" src="https://github.com/user-attachments/assets/c423c997-c380-46a8-8b55-6502063851a6" />

Atur hak akses masing-maisng user.

<img width="427" height="41" alt="Screenshot 2025-10-02 163347" src="https://github.com/user-attachments/assets/4e5efa2d-26c1-4b99-98ad-89fb71e198d5" />

``usermod -d /home/ftpshare ainur`` Perintah ini menetapkan ``/home/ftpshare`` sebagai home directory baru untuk user ainur. ``chmod 760 /home/ftpshare`` untuk membedakan hak akses: Ainur mendapatkan izin Write (w). Melkor mendapatkan izin No Access (0). 

Selanjutnya jalankan FTP Server:

<img width="322" height="40" alt="Screenshot 2025-10-02 163423" src="https://github.com/user-attachments/assets/07b6719e-ab0c-458d-bc8c-277263b8ba50" />

## Soal 8

## Soal 9 

## Soal 10
Lakukan ``ping`` ke router Eru  dari Melkor ``ping 10.82.1.1 -c 100``.

<img width="570" height="515" alt="Screenshot 2025-10-02 161143" src="https://github.com/user-attachments/assets/fc4a3ff5-0547-44e0-b2b4-9bf3cd90f7f8" />

Lihat hasil statistik ``ping``.

<img width="661" height="94" alt="Screenshot 2025-10-02 161330" src="https://github.com/user-attachments/assets/06ab0e59-4427-4a27-8ff0-98520c6601af" />

Hasil statistik menunjukkan bahwa node Melkor berhasil mengirim dan menerima semua paket ICMP yang ditujukan ke node Eru dengan kinerja yang sangat baik. ``0% packet loss`` Semua paket berhasil mencapai Eru dan kembali ke Melkor. Ini membuktikan koneksi jaringan antara kedua node ini sangat stabil. Waktu respons rata-rata (avg) hanya ``0.314 milidetik``, yang merupakan respons hampir instan. hal ini menunjukkan komunikasi antara Melkor dan Eru berjalan stabil dan cepat.

## Soal 11
Install server telnet di melkor dan tambahkan user. 

<img width="944" height="352" alt="Screenshot 2025-10-03 095006" src="https://github.com/user-attachments/assets/4a0d7538-5204-43d8-a514-cccf7d8d3074" />
<img width="379" height="135" alt="Screenshot 2025-10-03 095026" src="https://github.com/user-attachments/assets/c9b9cb0d-b4af-4231-8f1b-e06d3b4709ce" />

Jalankan server :

<img width="332" height="43" alt="image" src="https://github.com/user-attachments/assets/b09db275-bd0b-401e-ba4f-c825aff2bb24" />

Install perintah telnet di Eru dan jalankan telnet ke IP Melkor.

<img width="650" height="341" alt="image" src="https://github.com/user-attachments/assets/89debf72-241d-4f87-bb49-a6af86fc0807" />

<img width="563" height="65" alt="image" src="https://github.com/user-attachments/assets/7bc553b5-fb15-4d0a-a711-6b07ae6d4357" />

## Soal 12
Untuk menggunakan ``nc`` kita perlu menginstall dulu dengan ``apt update && apt-get install netcat-openbsd -y``.

<img width="1096" height="476" alt="Screenshot 2025-10-02 161516" src="https://github.com/user-attachments/assets/d6f7c8d8-807e-4347-a026-00aec1b163af" />

Install juga ``vsftpd`` di melkor.

<img width="1091" height="309" alt="Screenshot 2025-10-02 162227" src="https://github.com/user-attachments/assets/a8f6cd92-b7a2-460c-bf1c-9e4827680407" />

Jalankan ``ftp server`` dengan :

<img width="359" height="47" alt="Screenshot 2025-10-02 162253" src="https://github.com/user-attachments/assets/073013ea-30dd-4b18-aa62-a33a89132fa3" />

Selanjutnya, jalankan ``nc`` pada port 21 melkor pada Eru.

<img width="498" height="39" alt="Screenshot 2025-10-02 162311" src="https://github.com/user-attachments/assets/6a899a70-4905-4a63-9a69-91a03cf43eae" />

Opsi ``-z`` melakukan pemindaian tanpa mengirim data, dan ``-v`` menampilkan output secara verbose. Hasil dari netcat menunjukkan bahwa upaya koneksi TCP berhasil. Ini membuktikan bahwa ada layanan yang sedang berjalan di Melkor dan mendengarkan (listening) pada Port 21.<br/>
Untuk mengecek port 80, install ``nginx`` pada melkor.

<img width="931" height="466" alt="Screenshot 2025-10-02 162347" src="https://github.com/user-attachments/assets/44717dd4-415b-4379-9339-2fffbcfb0729" />

Jalankan dengan:

<img width="351" height="39" alt="Screenshot 2025-10-02 162358" src="https://github.com/user-attachments/assets/032733a1-aa44-47f4-9403-cfecfac32548" />
Selanjutnya, jalankan ``nc`` pada port 80 melkor pada Eru.

<img width="530" height="42" alt="Screenshot 2025-10-02 162409" src="https://github.com/user-attachments/assets/88a9e863-bb4d-40fe-bb21-16a16066f192" />
Ini membuktikan bahwa ada layanan yang sedang berjalan di Melkor dan mendengarkan (listening) pada Port 80.<br/>

Jalankan ``nc`` pada port 666 :

<img width="637" height="43" alt="Screenshot 2025-10-02 162420" src="https://github.com/user-attachments/assets/d528f4fc-a408-4038-8198-45833a6153b8" />

Berdasarkan hasil ini, Port 666 di Melkor adalah tertutup (closed). Hal ini menunjukkan bahwa Melkor tidak menjalankan layanan tersembunyi pada Port 666.

## Soal 13

## Soal 14
1. How many packets are recorded in the pcapng file?
Format: int
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/318d138e-c6b8-4f90-a01d-2bd9627a6200" />
Pada soal ditanya jumlah paket yang terekam, dan jumlah paket tersebut dapat dilihat di bagian bawah dari file pcapng yang sudah dibuka. \n
2. What are the user that successfully logged in?
Format: user:pass
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/6f79189c-f6cb-426b-b96d-276fadde3212" />
Dapat dilihat pada salah satu contoh file yang berisi line based text, terdapat kalimat “Invalid credentials”, jadi disini dapat disimpulkan jika percobaan untuk log in gagal, jadi selanjutnya kita lakukan filter untuk mengecualikan kata gagal yaitu “Invalid” dengan filter:
```
http.content_type contains “text/html” && !(http contains “Invalid”)
```
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/753cb12d-dcd4-4961-9cd5-e41d02072139" />
Filter tersebut digunakan untuk mengambil bagian dengan tipe konten text/html dan mengecualikan kata Invalid sehingga didapatkan 1 packet di atas dan lakukan
Follow -> TCP stream dan akan menemukan:
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/628ae7e0-5f8b-419c-bc78-b30b623d3c29" />
Dapat dilihat kita mendapatkan username dan password nya untuk menjawab soal.
<img width="616" height="310" alt="Image" src="https://github.com/user-attachments/assets/43655f8b-0730-4061-a59a-b7185720e68a" />
3. In which stream were the credentials found?
Format: int
Dari Follow yang kita lakukan untuk mendapatkan username dan password sebelumnya, kita dapat menemukan TCP stream dari paket tersebut di bagian kanan bawah, yaitu 41824
<img width="620" height="407" alt="Image" src="https://github.com/user-attachments/assets/fd57e341-cc6b-4c7e-9c2b-a24f7f2882f7" />
4. What tools are used for brute force?
Format: Hydra v1.8.0-dev
Sama dengan soal sebelumnya, untuk mendapatkan tools yang digunakan dapat dilihat dari hasil aksi Follow yang dilakukan pada bagian User Agent.
<img width="1028" height="525" alt="Image" src="https://github.com/user-attachments/assets/7a70f184-5777-48ef-8a20-03e7e4da8177" />

## Soal 15
-

## Soal 16
-

## Soal 17
1. What is the name of the first suspicious file?
Format: file.exe
Pertama kita cari terlebih dahulu paket yang mengandung .exe dengan filter:
http contains “.exe”
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/4d8bb1f2-b4a5-4965-88c0-0ba2265cfb7e" />
Dengan filter tersebut kita akan menemukan paket yang mengandung file .exe, selanjutnya kita coba untuk masukkan nama file .exe yang terlihat di bagian info.
<img width="578" height="232" alt="Image" src="https://github.com/user-attachments/assets/5d19c575-71bb-48fc-a0a0-e3c8842f8a7c" />
Karena nama file yang dimasukkan salah, kita coba pakai cara lain dengan pergi ke bagian File -> Export Objects -> HTTP dan kita akan menemukan:
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/ca2094d0-90e2-48f9-9cf6-c1acfd261dd1" />
Dimana akan mendapatkan 3 packet, namun saat sebelumnya kita mencoba untuk langsung memasukkan knr.exe dan terjadi kesalahan, selanjutnya kita coba masukkan file dengan format .doc di atas.
<img width="578" height="303" alt="Image" src="https://github.com/user-attachments/assets/5c8a53a0-39be-41ca-b235-beacaeeeb47f" />
Karena percobaan berhasil, maka file di atas merupakan file mencurigakan yang pertama.
2. What is the name of the second suspicious file?
Format: file.exe
Selanjutnya kita diminta mencari tahu nama file mencurigakan ke dua, jadi kita coba masukkan knr.exe yang sudah kita temukan sebelumnya.
<img width="594" height="395" alt="Image" src="https://github.com/user-attachments/assets/25c70186-828c-41d4-bd9e-4266ecf81b02" />
3. What is the hash of the second suspicious file (knr.exe)?
Format: sha256
Karena diminta hash file dengan format sha256, pertama kita lanjutkan dari halaman setelah Export Objects HTTP dimana kita select file knr.exe terlebih dahulu lalu kita save (disini saya menggunakan WSL Ubuntu) ke path home lalu user dan simpan file knr.exe, lalu buka Ubuntu dan jalankan sha256sum knr.exe untuk menghitung checksum (hash) sha256 dari file knr.exe
<img width="885" height="56" alt="Image" src="https://github.com/user-attachments/assets/ffccc29b-52b0-40f7-81bf-3c2da34f159d" />
Setelah mendapatkan hasilnya, kita bisa masukkan untuk menjawab soal.
<img width="1061" height="415" alt="Image" src="https://github.com/user-attachments/assets/8c791174-8f59-4b68-9bfd-4b933775fc95" />

## Soal 18
1. How many files are suspected of containing malware?
Format: int
Pertama kita dapat pergi ke bagian File -> Export Objects -> SMB, maka akan menampilkan 7 packet seperti di bawah:
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/6cda03cc-d121-4f13-8f0a-dfe8dce04d3d" />
Jika dilihat, akan ada beberapa file dan yang kemungkinan mengandung malware adalah file exe, jadi kita coba masukkan jumlah file: 2.
<img width="641" height="214" alt="Image" src="https://github.com/user-attachments/assets/c4f1fe25-a7f2-4821-bb20-c0bdb64be8e2" />
Karena sudah benar, saatnya lanjut ke nomor berikutnya.
2. What is the name of the first malicious file?
Format: file.exe
Pada halaman Export Objects SMB sebelumnya dapat dilihat informasi nama file yang pertama (atas), kita coba masukkan yang dimulai dari d0p.
<img width="875" height="306" alt="Image" src="https://github.com/user-attachments/assets/2b31cfd3-3cf4-4154-aff8-8f821ac4b6d1" />
Karena sudah berhasil, kita dapat melanjutkan ke soal berikutnya.
3. Apa nama file berbahaya yang kedua?
Format: file.exe
Karena sebelumnya sudah menggunakan nama file yang atas dan berhasil, sekarang kita masukkan nama file kedua yang dimulai dari oiku.
<img width="906" height="405" alt="Image" src="https://github.com/user-attachments/assets/a89a9d86-bb7a-4249-b701-cb02aa2e7aa3" />
Dan percobaan berhasil lalu lanjutkan ke soal selanjutnya.
4. What is the hash of the first malicious file?
Format: sha256
Sama seperti soal 17 yang sudah kita kerjakan, gunakan perintah sha256sum <filename> dan masukkan hasilnya untuk menjawab pertanyaan.
<img width="865" height="488" alt="Image" src="https://github.com/user-attachments/assets/9b6b1773-41fd-421a-9d30-732f72629bfa" />
5. What is the hash of the second malicious file?
Format: sha256
Kita lakukan cara yang sama untuk menjawab pertanyaan sebelumnya pada file kedua.
<img width="1069" height="615" alt="Image" src="https://github.com/user-attachments/assets/6043649a-8c72-4bb5-9855-1b9b73478356" />

## Soal 19
1. Who sent the threatening message
Format: string (name)
Pertama temukan packet dengan warna merah yang mencurigakan, lalu lakukan Follow ke TCP stream nya, dan akan mendapatkan hasil seperti di bawah:
<img width="1268" height="1080" alt="Image" src="https://github.com/user-attachments/assets/5ae79fdd-c1fb-4863-92fc-d84ab67f9430" />
Setelah masuk ke Follow TCP stream seperti di atas, selanjutnya cari bagian From yang mengindikasikan bahwa itu adalah sang pengirim dan coba masukkan tanpa email karena format nya adalah string yang berupa (name):
<img width="570" height="209" alt="Image" src="https://github.com/user-attachments/assets/d5e6bbd4-16dd-410a-9dce-324d18341792" />
2. How much ransom did the attacker demand ($)?
Format: int
Lanjut pada bagian halaman Follow TCP stream di atas, kita cari bagian dimana attacker memberi pesan meminta tebusan dan masukkan ke soal.
<img width="570" height="305" alt="Image" src="https://github.com/user-attachments/assets/289fc34d-6850-49b8-968e-124f074aac68" />
Ketika sudah menemukan jumlah yang diminta benar, kita bisa lanjut ke soal berikutnya.
3. What is the attacker’s bitcoin wallet?
Format: string
Untuk bagian ini kita dapat lanjut untuk mencari di halaman TCP stream sebelumnya berupa kata “My bitcoin wallet is:” dan masukkan nilai nya ke dalam soal.
<img width="1094" height="424" alt="Image" src="https://github.com/user-attachments/assets/16825be5-c6b4-4d49-b475-52777aa309a1" />
Setelah berhasil menemukan nilai nya, soal ini berhasil diselesaikan sepenuhnya.

## Soal 20
1. What encryption method is used?
Format: string
Lihat kolom Protocol di packet list: TCP, HTTP, BROWSER, ARP, TLSv1.2, STP, DNS, NBNS. Saat kita coba untuk follow masing-masing Protocol yang ada, hanya 2 Protocol yang menampilkan sesuatu seperti:
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/b4d7756c-40e4-4506-bcfe-77cb213db3a5" />
Jika kita lihat dari hasil follow di atas, ada 2 Protocol yang digunakan yaitu TLS dan HTTP. Kita coba masukkan untuk TLS ke dalam soal:
<img width="575" height="210" alt="Image" src="https://github.com/user-attachments/assets/26917fdc-152b-4aab-9cf4-90d6f2d5f4c3" />
Saat kita memasukkan jawaban TLS, jawaban tersebut dinyatakan benar, jadi kita bisa lanjut ke soal berikutnya.
2. What is the name of the malicious file placed by the attacker?
Format: file.exe
Untuk mencari nama file, kita bisa ikuti cara yang pernah kita lakukan, yaitu ke bagian
File -> Export Objects -> HTTP, namun saat kita cek hasilnya akan kosong, jadi kita gunakan file .txt yang diberi berupa keylog untuk membantu Wireshark mendecrypt TLS dalam file pcap. Pertama masuk ke bagian Edit -> Preferences -> Protocols -> TLS -> (Pre)-Master-Secret log filename lalu masukkan file keylog tadi dan apply. Selanjutnya kita masuk kembali ke Export Objects HTTP dan akan menampilkan beberapa packet yang sebelumnya tidak ada:
<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/0d0f594d-a441-4657-97f4-02416ca20e10" />
Dari packet di atas, yang memungkinkan sebagai file .exe adalah yang bernama invest_20.dll, jadi kita coba masukkan ke dalam soal:
<img width="778" height="307" alt="Image" src="https://github.com/user-attachments/assets/9cbcbc84-823e-43af-b153-2d575639e0a9" />
3. What is the hash of the file containing the malware?
Format: sha256
Kita lakukan sha256sum untuk file invest_20.dll seperti soal-soal sebelumnya.
<img width="1089" height="419" alt="Image" src="https://github.com/user-attachments/assets/f8b557df-fc49-4355-bd0d-e1ade508b8db" />

# Revisi 
## Soal 7
Setelah instal ``vsftpd`` di Eru dan membuat direktori baru ``/home/vsftpd``, masuk ke direktori tersebut. Setelahnya buat user, atur hak akses user, dan jalankan server ftp di user.

<img width="757" height="342" alt="Screenshot 2025-10-04 065458" src="https://github.com/user-attachments/assets/875efd28-ad7c-4d7e-a636-de0df5ad0d49" />

<img width="571" height="69" alt="Screenshot 2025-10-04 075357" src="https://github.com/user-attachments/assets/427a5e13-e58c-4020-8c61-fb7dccd18bc8" />

Di terminal manwe, coba akses ftp server dan masukkan usn dan password. 

<img width="496" height="192" alt="Screenshot 2025-10-04 075416" src="https://github.com/user-attachments/assets/95907dc5-81ab-46e6-a113-908342194827" />
