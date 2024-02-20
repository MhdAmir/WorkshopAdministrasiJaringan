# Instal Debian dengan Virtual Box

# 1. Persiapan
Unduh ISO Debian: Kunjungi situs web resmi Debian (debian.org) dan unduh file ISO untuk versi yang ingin Anda instal.
Instal VirtualBox: Unduh dan instal dari virtualbox.org. Pastikan Anda mengunduh versi yang sesuai dengan sistem operasi host Anda.

# 2. Buat Mesin Virtual Baru
Buka VirtualBox dan klik tombol "New" atau "Baru". Berikan nama untuk mesin virtual Anda, misalnya "Debian". VirtualBox biasanya akan secara otomatis memilih tipe dan versi sistem operasi berdasarkan nama yang Anda masukkan. Jika tidak, pilih "Linux" sebagai tipe dan "Debian (64-bit)" sebagai versi.

Atur ukuran memori dan cpu. Disini saya menggunakan 2048 MB (2GB) dan 1 cpu, lebih banyak akan lebih baik, tergantung pada seberapa banyak RAM yang tersedia di sistem host.

<img src="./assets/Screenshot 2024-02-19 104719.png">

Pilih "Create a virtual hard disk now" untuk membuat disk baru. Klik "Create".

<img src="./assets/Screenshot 2024-02-19 104735.png">

# 5. Instalasi Debian

Mulai VM: Pilih VM dan klik "Start".
Buat Pengguna dan Kata Sandi: Masukkan informasi pengguna dan kata sandi.

<img src="./assets/Screenshot 2024-02-19 095456.png">

Partisi Disk: Saat diminta untuk mempartisi disk, pilih "Manual" untuk konfigurasi manual. bisa membuat partisi root (biasanya dipasang sebagai /), swap, dan storage (/storage) sesuai dengan preferensi.

<img src="./assets/Screenshot 2024-02-19 095906.png">
<img src="./assets/Screenshot 2024-02-19 100009.png">
<img src="./assets/Screenshot 2024-02-19 100046.png">
<img src="./assets/Screenshot 2024-02-19 100118.png">

Pilih Mirror Debian: Pilih mirror yang paling dekat dengan lokasi untuk mengunduh paket.

<img src="./assets/Screenshot 2024-02-19 100353.png">

Instal GRUB: Saat diminta, pilih untuk menginstal GRUB bootloader pada disk.

<img src="./assets/Screenshot 2024-02-19 110956.png">

Setelah proses instalasi selesai, mesin akan meminta Anda untuk me-restart. Setelah restart, Anda seharusnya akan disambut oleh layar login Debian baru Anda.

Selamat! Anda sekarang telah berhasil menginstal Debian di VirtualBox dengan konfigurasi manual. Ingat untuk menginstal "Guest Additions" untuk performa dan fungsionalitas yang lebih baik.

<img src="./assets/Screenshot 2024-02-19 111454.png">
