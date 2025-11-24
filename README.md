WebGIST — Dokumentasi Sistem

Versi: 1.0
Platform: Laravel 12.x
Tanggal: 2025

1. Pendahuluan

WebGIST adalah aplikasi manajemen layanan internet yang digunakan untuk:

Manajemen pelanggan

Manajemen ODP / jaringan

Sistem antrian instalasi

Manajemen tiket gangguan

Monitoring teknisi

Monitoring NOC (server & ONU via GenieACS)

Dashboard multi-role

Aplikasi mendukung 4 role utama:

Role	Akses
Admin	Semua fitur (Antrian, Pelanggan, ODP, Invoice, User, Ticket)
Koordinator	Antrian, Pemilihan ODP manual, Monitoring teknisi, Laporan
Teknisi	Job list, Input hasil, Ticket gangguan
NOC	Aktivasi pelanggan, Monitoring server, Monitoring ONU, Kelola ODP
2. Instalasi Sistem
2.1. Persyaratan Server

PHP 8.2+

MySQL/MariaDB

Composer

Ekstensi PHP:

pdo_mysql

mbstring

openssl

gd / imagick

Web Server Apache/Nginx

2.2. Langkah Instalasi
1. Clone atau upload project
git clone https://github.com/... webgist
cd webgist


Jika upload ke hosting, pastikan folder berada di:

/home/USERNAME/PROJECT/

2. Install vendor
composer install

3. Buat file .env
cp .env.example .env


Isi konfigurasi database:

DB_HOST=localhost
DB_DATABASE=nama_db
DB_USERNAME=user_db
DB_PASSWORD=pass_db

4. Generate application key
php artisan key:generate

5. Migrasi database (opsional)

⚠️ Jika memakai database existing, langkah ini dilewati.

php artisan migrate

6. Set permission storage
chmod -R 775 storage bootstrap/cache

7. Konfigurasi domain public

Arahkan domain ke:

/public

3. Alur Sistem
3.1. Fitur Antrian (Admin & Koordinator)
ADMIN

Input pelanggan baru → sistem otomatis mencari ODP terdekat berdasarkan titik koordinat.

Jika jarak > 200m → Admin tidak dapat melanjutkan (demi validitas jaringan).

Admin hanya bisa membuat antrian jika:

Jarak ≤ 200 meter

Port tersedia

KOORDINATOR

Bisa input antrian meskipun jarak > 200m

Akan muncul opsi:

Pilih ODP manual

Koordinator lebih fleksibel karena mereka memahami jaringan lapangan lebih detail.

TEKNISI

Job otomatis muncul berdasarkan antrian yang siap dikerjakan.

Teknisi input:

Foto Before

Foto After

Catatan

Port ODP

Setelah klik Selesai, job hilang dari list teknisi.

NOC

Melihat daftar “Tunggu Aktivasi”

Bisa:

Terima & aktivasi pelanggan

Tolak pelanggan

Setelah aktivasi → status pelanggan berubah menjadi “Aktif”.

4. Fitur Ticket Gangguan
Admin/NOC/Koordinator

Bisa membuat tiket gangguan

Bisa melihat seluruh tiket

Bisa verifikasi & close tiket

Teknisi

Melihat tiket terbuka

Klik Kerjakan → otomatis status: In Progress

Setelah selesai →

Input tindakan

Upload foto Before/After

Submit

Tiket hilang dari list teknisi

5. Fitur ODP Location

CRUD ODP

Format koordinat mendukung:

DMS → 7°08'57"S 109°56'14"E

Decimal → -7.149250, 109.937361

Konversi otomatis

Tracking port lengkap:

Port 1–N

Status tiap port: kosong / terisi

Tampilkan detail pelanggan pada port

6. Fitur Monitoring Server (NOC)

Menampilkan list server yang dipantau

Fitur utama:

Ping test

Edit server

Tambah server

Hapus server

Auto coloring:

Hijau → Online

Merah → Offline

7. Fitur Monitoring ONU (GenieACS)
Endpoint yang digunakan
GET http://GENIEACS_HOST:7557/devices


Data diambil dari Virtual Parameter:

Field	Virtual Parameter
SN / ID ONU	DeviceID.SerialNumber
Hostname	Device.DeviceInfo.SoftwareVersion
Status	Device.Gpon.LinkStatus
Uptime	Device.DeviceInfo.UpTime
Signal RX	Device.GPON.RXPower
Signal TX	Device.GPON.TXPower
OLT	Device.ManagementServer.ConnectionRequestURL
Fitur Monitoring

Tabel daftar semua ONU terhubung

Search SN / nama pelanggan

Auto-refresh (optional)

Hanya dapat diakses role NOC

8. Struktur Database Utama
Table: pelanggan

id

nama

alamat

no_hp

paket

sn_onu

sn_pon

odpman

port_odp

jarak_odp

status_pelanggan

tanggal_aktivasi

Table: odp_locations

id

kode_odp

nama_odp

latitude

longitude

kapasitas_port_total

port_terpakai

status

Table: antrian

id

nama

alamat

tikor

jarak_odp

status

teknisi_id

odp_id

Table: tickets

id

nama_pelanggan

jenis_gangguan

deskripsi

status

tindakan

teknisi_id

waktu_mulai

waktu_selesai

9. Hak Akses
Fitur	Admin	Koordinator	Teknisi	NOC
Dashboard	✔	✔	✔	✔
Data Antrian	✔	✔	❌	❌
Input Antrian	✔ (<200m)	✔ (tanpa limit)	❌	❌
Pilih ODP Manual	❌	✔	❌	❌
Job List Teknisi	❌	❌	✔	❌
Aktivasi Pelanggan	❌	❌	❌	✔
Manajemen ODP	✔	❌	❌	✔
Ticket Gangguan	✔	✔	✔	✔
Monitoring Server	❌	❌	❌	✔
Monitoring ONU	❌	❌	❌	✔
User Management	✔	❌	❌	❌
10. Penutup

Dokumentasi ini berisi panduan lengkap instalasi, alur sistem, hak akses, serta detail fitur WebGIST. Jika membutuhkan update, tambahan fitur, atau pembuatan mobile apps (Android/iOS), proyek dapat diperluas menggunakan API yang sudah tersedia.
