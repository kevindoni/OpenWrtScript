# OpenWrtScript

OpenWrtScripts
Ini adalah satu set skrip (terkadang juga disebut "Openscripts") yang melaporkan, mengonfigurasi, dan mengukur (dan meningkatkan) latensi di router rumah (dan di mana pun!) Skrip ini berfungsi sama baiknya untuk LEDE dan OpenWrt dan mencakup:

. getstats.sh - skrip untuk mengumpulkan informasi pemecahan masalah yang membantu mendiagnosis masalah dalam distribusi OpenWrt.

. opkgscript.sh - skrip untuk menyimpan daftar paket yang saat ini diinstal (katakanlah, sebelum sysupgrade), dan kemudian mengembalikan set lengkap paket setelah peningkatan.

. config-openwrt.sh - skrip untuk mengkonfigurasi router OpenWrt secara konsisten setelah mem-flash firmware pabrik.

. betterspeedtest.sh & netperfrunner.sh & networkhammer.sh - script yang mengukur kinerja beban router atau penawaran Anda ke jaringan untuk pengujian.

. idlelatency.sh - skrip untuk mengukur latensi "jalur menganggur" tanpa pembuatan lalu lintas tambahan dari skrip.

. tunnelbroker.sh - skrip untuk menyiapkan terowongan IPv6 6-in-4 ke TunnelBroker.net.

Script ini dapat disimpan dalam /usr/lib/OpenWrtScriptsdirektori. Cara termudah untuk melakukannya adalah dengan menggunakan ssh ke router dan masukkan perintah ini:

opkg update
opkg install netperf
opkg install git
cd /usr/lib
git clone git://github.com/richb-hanover/OpenWrtScripts.git



# getstats.sh
getstats.sh Script membantu masalah diagnosa dengan OpenWRT. Jika Anda melaporkan masalah, selalu membantu untuk menyertakan output dari skrip ini.

getstats.shmengeksekusi satu set perintah bawaan dan menulis output yang dikumpulkan ke /tmp/openwrtstats.txt. Script juga mengeksekusi perintah yang diteruskan sebagai argumen pada baris perintah. Ini juga menampilkan daftar paket opkg yang diinstal pengguna - yaitu paket yang tidak diinstal secara default. Pada contoh di bawah, output akan berisi hasil dari kumpulan perintah standar ditambah dua argumen tambahan:

Pemakaian: sh getstats.sh "ls /usr/lib" "ls -al /etc/config"

Untuk menginstal dan menjalankan skrip ini: Skrip ini mandiri, dan dapat ditempatkan di direktori mana pun. Baca bagian atas file getstats.sh untuk prosedur sederhana dalam menggunakan skrip.

Contoh file keluaran: Lihat contoh file keluaran - openwrtstats.txt

# opkgscript.sh
opkgscript.shScript membantu untuk mengembalikan set saat paket setelah sysupgrade atau bahkan instalasi yang bersih baik Lede atau OpenWRT. Secara default, writeperintah menyimpan daftar paket yang diinstal /etc/config/opkg.installed( di mana itu akan disimpan di seluruh sysupgrades), dan installperintah membaca file, untuk memulihkan set paket itu. Kloning dari skrip asli Malte Forkel .

Pemakaian:

sh opkgscript.sh write gunakan sebelum sysupgrade untuk menyimpan set paket saat ini

sh opkgscript.sh install gunakan setelah sysupgrade berhasil, untuk memulihkan paket-paket itu

sh opkgscript.sh help tampilkan informasi bantuan lengkap untuk skrip

# config-openwrt.sh
config-openwrt.shScript update pengaturan pabrik OpenWRT ke konfigurasi dikenal baik-baik. Jika Anda sering memperbarui firmware, Anda dapat menggunakan skrip ini untuk mengonfigurasi ulang router ke kondisi yang konsisten. Anda harus membuat salinan skrip ini, menyesuaikannya dengan kebutuhan Anda, kemudian menggunakan prosedur "Untuk menjalankan skrip ini" (di bawah).

Skrip ini dirancang untuk mengonfigurasi pengaturan setelah flash firmware "pabrik" awal. Ada bagian di bawah ini untuk mengonfigurasi banyak aspek router Anda. Semua bagian dikomentari. Ada bagian untuk:

Siapkan antarmuka WAN untuk terhubung ke penyedia Anda
. Perbarui paket perangkat lunak
. Perbarui kata sandi root
. Setel zona waktu
. Aktifkan SNMP untuk pemantauan dan pengukuran lalu lintas
. Aktifkan mDNS/ZeroConf pada antarmuka WAN
. Atur parameter SQM (Smart Queue Management)
[ Catatan: item yang tersisa belum dikonversi untuk berfungsi di OpenWrt ]

. Aktifkan ekspor NetFlow untuk analisis lalu lintas
. Ubah alamat IP dan subnet default untuk antarmuka
. Ubah nama DNS default
. Setel saluran radio
. Setel nama SSID nirkabel
. Setel kredensial keamanan nirkabel]_
Untuk menjalankan skrip ini

Flash router dengan firmware pabrik. Kemudian telnet/ssh masuk dan jalankan pernyataan ini. Anda harus melakukan ini melalui koneksi kabel karena beberapa perubahan ini dapat mengatur ulang jaringan nirkabel.

ssh root@192.168.1.1
cd /tmp
cat > config.sh 
[paste in the contents of this file, then hit ^D]
sh config.sh
Presto! (You should reboot the router when this completes.)
Catatan: Jika Anda menggunakan router OpenWrt sekunder, Anda dapat membuat salinan lain dari skrip ini, dan menggunakannya untuk mengatur parameter konfigurasi yang berbeda (mungkin subnet yang berbeda, saluran radio, SSID, mengaktifkan mDNS, dll).

# betterspeedtest.sh
betterspeedtest.sh Script mengemulasi tes berbasis web yang dilakukan oleh speedtest.net, tetapi apakah itu yang lebih baik. Saat skrip melakukan pengunduhan dan pengunggahan ke server di Internet, skrip ini secara bersamaan mengukur latensi ping untuk melihat apakah transfer file memengaruhi respons jaringan Anda.

Inilah mengapa itu penting: Jika transfer data meningkatkan latensi/lag banyak, maka aktivitas jaringan lain, seperti obrolan suara atau video, permainan, dan aktivitas jaringan umum juga akan bekerja dengan buruk. Gamer akan melihat ini sebagai ketertinggalan ketika orang lain menggunakan jaringan. Skype dan FaceTime akan melihat putus atau macet. Latensi buruk, dan router yang baik tidak akan membiarkannya terjadi.

Skrip betterspeedtest.sh mengukur latensi selama transfer file. Untuk memanggilnya:

sh betterspeedtest.sh [ -4 | -6 ] [ -H netperf-server ] [ -t duration ] [ -p host-to-ping ] [ -i ] [ -n simultaneous-streams ]
Opsi, jika ada, adalah:

. -H | --host: DNS atau Alamat server netperf (default - netperf.bufferbloat.net)
Server alternatif adalah netperf-east (pantai timur AS), netperf-west (California), dan netperf-eu (Denmark)
. -4 | -6: Aktifkan pengujian ipv4 atau ipv6 (default - ipv4)
. -t | --time: Durasi untuk berapa lama pengujian setiap arah harus dijalankan - (default - 60 detik)
. -p | --ping: Host ke ping untuk mengukur latensi (default - gstatic.com)
. -i | --idle: Jangan mengirim lalu lintas, hanya ukur latensi menganggur
. -n | --number: Jumlah sesi simultan (default - 5 sesi)
Outputnya menunjukkan kecepatan unduh dan unggah terpisah (satu arah), bersama dengan ringkasan latensi, termasuk persentil min, maks, rata-rata, median, dan 10 dan 90 sehingga Anda bisa memahami distribusinya. Alat ini juga menampilkan persentase kehilangan paket. Contoh di bawah ini menunjukkan dua pengukuran, buruk dan baik.

Pengujian Idle menggunakan proses yang sama untuk mengukur latensi saluran, tetapi tanpa lalu lintas tambahan apa pun dari skrip ini. Ini berjalan untuk --time yang ditentukan.

Di sebelah kiri adalah uji coba tanpa SQM. Perhatikan bahwa latensi menjadi besar (lebih dari 5 detik), artinya kinerja jaringan akan buruk bagi siapa pun yang menggunakan jaringan.

Di sebelah kanan adalah tes menggunakan SQM: latency naik sedikit (kurang dari 23 msec di bawah beban), dan kinerja jaringan tetap baik.

Example with NO SQM - BAD                                     Example using SQM - GOOD

root@openwrt:/usr/lib/OpenWrtScripts# sh betterspeedtest.sh   root@openwrt:/usr/lib/OpenWrtScripts# sh betterspeedtest.sh
[date/time] Testing against netperf.bufferbloat.net (ipv4)    [date/time] Testing against netperf.bufferbloat.net (ipv4)
   with 5 simultaneous sessions while pinging gstatic.com        with 5 simultaneous sessions while pinging gstatic.com
   (60 seconds in each direction)                                (60 seconds in each direction)

 Download:  6.65 Mbps                                         Download:  6.62 Mbps
  Latency: (in msec, 58 pings, 0.00% packet loss)              Latency: (in msec, 61 pings, 0.00% packet loss)
      Min: 43.399                                                  Min: 43.092
    10pct: 156.092                                               10pct: 43.916
   Median: 230.921                                              Median: 46.400
      Avg: 248.849                                                 Avg: 46.575
    90pct: 354.738                                               90pct: 48.514
      Max: 385.507                                                 Max: 56.150

   Upload:  0.72 Mbps                                           Upload:  0.70 Mbps
  Latency: (in msec, 59 pings, 0.00% packet loss)              Latency: (in msec, 53 pings, 0.00% packet loss)
      Min: 43.699                                                  Min: 43.394
    10pct: 352.521                                               10pct: 44.202
   Median: 4208.574                                             Median: 50.061
      Avg: 3587.534                                                Avg: 50.486
    90pct: 5163.901                                              90pct: 56.061
      Max: 5334.262                                                Max: 69.333
idlelatency.sh
The idlelatency.shScript merangkum kali ping diukur selama interval waktu tertentu. Untuk memanggil skrip:

sh idlelatency.sh [ -4 | -6 ] [ -t duration ] [ -p host-to-ping ] 
Opsi, jika ada adalah:

-4 | -6: Aktifkan pengujian ipv4 atau ipv6 (default - ipv4)
-t | --time: Durasi untuk berapa lama pengujian setiap arah harus dijalankan - (default - 60 detik)
-p | --ping: Host ke ping untuk mengukur latensi (default - gstatic.com)
Output dari skrip terlihat seperti ini:

root@openwrt: sh idlelatency.sh
2020-05-02 12:10:53 Testing idle line while pinging gstatic.com (60 seconds)
............................................................
  Latency: (in msec, 60 pings, 0.00% packet loss)
      Min: 20.438
    10pct: 22.633
   Median: 36.907
      Avg: 35.143
    90pct: 45.994
      Max: 50.377
netperfrunner.sh
The netperfrunner.shScript menjalankan beberapa perintah netperf secara bersamaan. Ini meniru stress test netperf-wrapper [Github] tetapi tanpa hasil GUI yang bagus.

Saat Anda memulai skrip ini, skrip ini secara bersamaan mengunggah dan mengunduh beberapa aliran (file) ke server di Internet. Ini menempatkan beban berat pada tautan bottleneck jaringan Anda (mungkin koneksi Anda ke Internet), dan memungkinkan Anda mengukur total bandwidth dan latensi tautan selama transfer.

Untuk memanggil skrip:

sh netperfrunner.sh [ -4 | -6 ] [ -H netperf-server ] [ -t duration ] [ -p host-to-ping ] [-n simultaneous-streams ]
Opsi, jika ada, adalah:

-H | --host: DNS atau Alamat server netperf (default - netperf.bufferbloat.net)
Server alternatif adalah netperf-east (pantai timur AS), netperf-west (California), dan netperf-eu (Denmark)
-4 | -6: Aktifkan pengujian ipv4 atau ipv6 (default - ipv4)
-t | --time: Durasi untuk berapa lama pengujian setiap arah harus dijalankan - (default - 60 detik)
-p | --ping: Host ke ping untuk mengukur latensi (default - gstatic.com)
-n | --number: Jumlah sesi simultan (default - 4 sesi)
Output dari skrip terlihat seperti ini:

root@openwrt:/usr/lib/OpenWrtScripts# sh netperfrunner.sh
[date/time] Testing netperf.bufferbloat.net (ipv4) with 4 streams down and up 
    while pinging gstatic.com. Takes about 60 seconds.
Download:  5.02 Mbps
  Upload:  0.41 Mbps
 Latency: (in msec, 61 pings, 15.00% packet loss)
     Min: 44.494
   10pct: 44.494
  Median: 66.438
     Avg: 68.559
   90pct: 79.049
     Max: 140.421
Catatan: Kecepatan unduh dan unggah yang dilaporkan mungkin jauh lebih rendah daripada kecepatan pengenal saluran Anda. Ini bukan bug, juga bukan masalah dengan koneksi internet Anda. Itu karena pesan konfirmasi yang dikirim kembali ke pengirim menghabiskan sebagian besar kapasitas tautan (sebanyak 25%).

# networkhammer.sh
The networkhammer.shScript terus memanggil script netperfrunner untuk memberikan beban berat. Ini berjalan selamanya - Ctl-C akan mengganggunya.

# tunnelbroker.sh
The tunnelbroker.shmengkonfigurasi Script OpenWRT untuk membuat sebuah terowongan IPv6 melalui Badai listrik. Ini adalah cara mudah untuk mengenal IPv6 jika ISP Anda tidak menawarkan kemampuan IPv6 asli. Ada beberapa langkah:

Buka situs Hurricane Electric TunnelBroker.net untuk menyiapkan akun gratis Anda. Ada instruksi terperinci untuk menyiapkan akun dan terowongan IPv6 di skrip itu sendiri, atau di halaman Terowongan IPv6 dari bufferbloat.net
Dari halaman utama tunnelbroker, klik "Buat Terowongan Reguler"
Masukkan alamat IP Anda di "IPv4 Endpoint" (tempel di alamat yang Anda "lihat dari")
Pilih Server Terowongan terdekat
Klik "Buat Terowongan"
Pada halaman Detail Tunnel yang dihasilkan, klik Tetapkan /48 untuk mendapatkan awalan /48

Dari halaman Detail Tunnel, salin dan tempel nilai yang cocok ke dalam tunnel.shfile. The USER_NAME adalah nama yang digunakan untuk membuat account. Temukan Update_Key pada Tab Lanjutan pada halaman Detail Tunnel.

ssh ke router dan jalankan skrip ini dengan langkah-langkah ini.

 ssh root@192.168.1.1     # use the address of your router
 cd /tmp
 cat > tunnel.sh 
 [paste in the contents of this file, then hit ^D]
 [edit the script to match your tunnelbroker values]
 sh tunnel.sh
 [Restart your router. This seems to make a difference.]
Presto! Terowongan Anda sudah habis! Komputer Anda harus mendapatkan alamat IPv6 global, dan harus dapat berkomunikasi langsung dengan perangkat IPv6 di Internet. Untuk mengujinya, coba:ping6 ivp6.google.com
