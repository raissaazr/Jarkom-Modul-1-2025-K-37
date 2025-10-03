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

## Soal 8

## Soal 9 

## Soal 10
Lakukan ``ping`` ke router Eru  dari Melkor ``ping 10.82.1.1 -c 100``
<img width="570" height="515" alt="Screenshot 2025-10-02 161143" src="https://github.com/user-attachments/assets/fc4a3ff5-0547-44e0-b2b4-9bf3cd90f7f8" />
Lihat hasil statistik ``ping``.
<img width="661" height="94" alt="Screenshot 2025-10-02 161330" src="https://github.com/user-attachments/assets/06ab0e59-4427-4a27-8ff0-98520c6601af" />
Hasil statistik menunjukkan bahwa node Melkor berhasil mengirim dan menerima semua paket ICMP yang ditujukan ke node Eru dengan kinerja yang sangat baik. ``0% packet loss`` Semua paket berhasil mencapai Eru dan kembali ke Melkor. Ini membuktikan koneksi jaringan antara kedua node ini sangat stabil. Waktu respons rata-rata (avg) hanya ``0.314 milidetik``, yang merupakan respons hampir instan. hal ini menunjukkan komunikasi antara Melkor dan Eru berjalan stabil dan cepat.

## Soal 11

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
<img width="1028" height="525" alt="Image" src="https://github.com/user-attachments/assets/7a70f184-5777-48ef-8a20-03e7e4da8177" />

## Soal 15

## Soal 16

## Soal 17
<img width="1061" height="415" alt="Image" src="https://github.com/user-attachments/assets/8c791174-8f59-4b68-9bfd-4b933775fc95" />

## Soal 18
<img width="1069" height="615" alt="Image" src="https://github.com/user-attachments/assets/6043649a-8c72-4bb5-9855-1b9b73478356" />

## Soal 19
<img width="1094" height="424" alt="Image" src="https://github.com/user-attachments/assets/16825be5-c6b4-4d49-b475-52777aa309a1" />

## Soal 20
<img width="1089" height="419" alt="Image" src="https://github.com/user-attachments/assets/f8b557df-fc49-4355-bd0d-e1ade508b8db" />
