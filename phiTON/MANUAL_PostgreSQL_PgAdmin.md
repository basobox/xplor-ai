# Dokumentasi Instalasi PostgreSQL 17 dan pgAdmin 4 Menggunakan Docker Compose

## 1. Tujuan

Membangun lingkungan database lokal menggunakan:

- **PostgreSQL 17** sebagai Database Management System (DBMS).
- **pgAdmin 4** sebagai antarmuka grafis untuk mengelola PostgreSQL.
- **Docker Compose** untuk menjalankan PostgreSQL dan pgAdmin dalam container.
- **Conda environment `h8_env`** sebagai lingkungan kerja proyek.

Tujuan akhirnya adalah memastikan PostgreSQL berjalan, database dapat dibuat, tabel dapat dikelola, dan pgAdmin berhasil terhubung ke PostgreSQL.

## 2. Struktur Lingkungan Kerja

Lokasi proyek:

```bash
~/Downloads/H8
```

Conda environment:

```bash
h8_env
```

Struktur konfigurasi:

```text
H8/
├── .env
└── docker-compose.yml
```

- `.env`: menyimpan variabel konfigurasi database dan akun pgAdmin.
- `docker-compose.yml`: mendefinisikan layanan PostgreSQL dan pgAdmin.

## 3. Konfigurasi Environment (`.env`)

Contoh konfigurasi:

```env
POSTGRES_DB=h8_database
POSTGRES_USER=h8_user
POSTGRES_PASSWORD=GANTI_DENGAN_PASSWORD_KUAT

PGADMIN_DEFAULT_EMAIL=admin@gmail.com
PGADMIN_DEFAULT_PASSWORD=GANTI_DENGAN_PASSWORD_KUAT
```

| Variabel | Fungsi |
|---|---|
| `POSTGRES_DB` | Nama database |
| `POSTGRES_USER` | Username PostgreSQL |
| `POSTGRES_PASSWORD` | Password PostgreSQL |
| `PGADMIN_DEFAULT_EMAIL` | Email untuk login ke pgAdmin |
| `PGADMIN_DEFAULT_PASSWORD` | Password untuk login ke pgAdmin |

**Penting:** PostgreSQL dan pgAdmin memiliki autentikasi masing-masing. Password PostgreSQL dipakai saat pgAdmin menghubungkan diri ke database. Password pgAdmin dipakai untuk login ke aplikasi web.

Gunakan password kuat dan unik. Jangan membagikan isi `.env` atau memasukkannya ke Git.

## 4. Mengaktifkan Environment dan Menjalankan Docker Compose

Aktifkan environment dan masuk ke direktori proyek:

```bash
conda activate h8_env
cd ~/Downloads/H8
```

Jalankan layanan:

```bash
docker-compose up -d
```

Periksa status container:

```bash
docker-compose ps
```

Hasil yang diharapkan:

```text
NAME          IMAGE                   STATUS
h8_pgadmin    dpage/pgadmin4:latest   Up
h8_postgres   postgres:17             Up (healthy)
```

Keterangan:

| Container | Fungsi |
|---|---|
| `h8_postgres` | Server database PostgreSQL |
| `h8_pgadmin` | Antarmuka web pgAdmin |

Port yang digunakan:

| Aplikasi | Port | Alamat lokal |
|---|---:|---|
| PostgreSQL | `5432` | `127.0.0.1:5432` |
| pgAdmin | `5050` | `http://localhost:5050` |

`healthy` menunjukkan bahwa health check PostgreSQL berhasil. Status `Up` pada pgAdmin menunjukkan containernya sedang berjalan; akses web dan koneksi database tetap perlu diuji.

## 5. Pemeriksaan PostgreSQL

### 5.1. Memeriksa environment variable

Periksa username dan database di dalam container:

```bash
docker-compose exec postgres printenv POSTGRES_USER
docker-compose exec postgres printenv POSTGRES_DB
```

Periksa apakah password tersedia tanpa menampilkannya:

```bash
docker-compose exec postgres bash -c 'if [ -n "$POSTGRES_PASSWORD" ]; then echo "POSTGRES_PASSWORD tersedia"; else echo "POSTGRES_PASSWORD kosong"; fi'
```

Hasil yang diharapkan:

```text
h8_user
h8_database
POSTGRES_PASSWORD tersedia
```

**Analisis:** variabel tersedia di environment container. Namun, hal ini belum membuktikan bahwa password tersebut sama dengan password autentikasi yang tersimpan pada role PostgreSQL.

### 5.2. Menguji login melalui koneksi lokal

```bash
docker-compose exec postgres psql -U h8_user -d h8_database -c "SELECT current_user;"
```

Hasil yang diharapkan:

```text
 current_user
--------------
 h8_user
(1 row)
```

Perintah ini biasanya menggunakan Unix socket lokal di dalam container. Karena itu, keberhasilannya belum tentu membuktikan autentikasi password melalui TCP/IP.

### 5.3. Memeriksa daftar database

```bash
docker-compose exec postgres psql -U h8_user -d h8_database -c "\l"
```

Database bawaan dan database proyek yang dapat terlihat antara lain:

- `h8_database`
- `postgres`
- `template0`
- `template1`

Database proyek seharusnya dimiliki oleh `h8_user` dan menggunakan encoding UTF8.

### 5.4. Memeriksa tabel

```bash
docker-compose exec postgres psql -U h8_user -d h8_database -c "\dt"
```

Jika hasilnya:

```text
Did not find any relations.
```

ini **bukan error**. Artinya database tersedia, tetapi belum memiliki tabel.

Database adalah wadah untuk objek-objek database, sedangkan tabel menyimpan data dalam baris dan kolom.

## 6. Pengujian SQL

### 6.1. Masuk ke database

```bash
docker-compose exec postgres psql -U h8_user -d h8_database
```

### 6.2. Membuat tabel (`CREATE TABLE`)

```sql
CREATE TABLE mahasiswa (
    id SERIAL PRIMARY KEY,
    nama VARCHAR(100),
    jurusan VARCHAR(100)
);
```

Hasil yang diharapkan:

```text
CREATE TABLE
```

Penjelasan:

| Kolom | Tipe data | Fungsi |
|---|---|---|
| `id` | `SERIAL` | Nomor otomatis |
| `nama` | `VARCHAR(100)` | Nama mahasiswa |
| `jurusan` | `VARCHAR(100)` | Jurusan mahasiswa |

`PRIMARY KEY` menjamin nilai identitas setiap baris unik dan tidak `NULL`.

### 6.3. Memeriksa tabel

```sql
\dt
```

Tabel `mahasiswa` seharusnya muncul pada schema `public` dengan owner `h8_user`.

### 6.4. Memasukkan data (`INSERT`)

```sql
INSERT INTO mahasiswa (nama, jurusan)
VALUES ('Budi', 'Data Science');
```

Hasil:

```text
INSERT 0 1
```

Artinya satu baris data berhasil dimasukkan.

### 6.5. Menampilkan data (`SELECT`)

```sql
SELECT * FROM mahasiswa;
```

Hasil yang diharapkan:

```text
 id | nama |   jurusan
----+------+--------------
  1 | Budi | Data Science
(1 row)
```

### 6.6. Keluar dari psql

```sql
\q
```

**Kesimpulan pengujian:** `CREATE TABLE`, `INSERT`, dan `SELECT` berhasil dijalankan. Operasi `UPDATE` dan `DELETE` belum diuji dalam sesi ini.

## 7. Membuka dan Mengonfigurasi pgAdmin

Buka alamat berikut di browser:

```text
http://localhost:5050
```

Login ke pgAdmin menggunakan:

- Email: nilai `PGADMIN_DEFAULT_EMAIL`.
- Password: nilai `PGADMIN_DEFAULT_PASSWORD`.

Saat mendaftarkan server PostgreSQL di pgAdmin, gunakan konfigurasi berikut:

| Field | Nilai |
|---|---|
| General → Name | `H8 PostgreSQL` |
| Connection → Host name/address | `postgres` |
| Connection → Port | `5432` |
| Connection → Maintenance database | `h8_database` |
| Connection → Username | `h8_user` |
| Connection → Password | Nilai password PostgreSQL |

**Mengapa hostname `postgres`, bukan `localhost`?**

pgAdmin dan PostgreSQL berjalan pada container berbeda dalam jaringan Docker Compose yang sama. Nama service PostgreSQL, yaitu `postgres`, dapat digunakan sebagai hostname antarkontainer. `localhost` dari dalam container pgAdmin menunjuk ke container pgAdmin itu sendiri, bukan container PostgreSQL.

## 8. Troubleshooting pgAdmin

### 8.1. Halaman pgAdmin mengalami loading

Periksa status layanan:

```bash
docker-compose ps
```

Jika container `h8_pgadmin` berstatus `Up`, container sedang berjalan, tetapi hal itu sendiri belum menjamin aplikasi web dapat digunakan.

Periksa log pgAdmin:

```bash
docker-compose logs pgadmin
```

Periksa juga log PostgreSQL:

```bash
docker-compose logs postgres
```

Buka kembali:

```text
http://localhost:5050
```

### 8.2. Error `password authentication failed`

Pesan yang ditemukan:

```text
connection failed:

connection to server at "172.18.0.2",
port 5432 failed:

FATAL: password authentication failed
for user "h8_user"
```

#### Analisis pesan error

- `172.18.0.2`: alamat IP container PostgreSQL yang ditemukan pgAdmin.
- `5432`: port PostgreSQL.
- `h8_user`: username yang dicoba.
- `password authentication failed`: server menolak password yang diberikan.

Pesan ini menunjukkan bahwa pgAdmin berhasil mencapai server PostgreSQL melalui jaringan, tetapi autentikasi password gagal. Jadi, fokus pemeriksaan adalah kredensial, bukan instalasi ulang Docker.

#### Kemungkinan penyebab: `.env` berubah setelah inisialisasi database

Pada image resmi PostgreSQL, `POSTGRES_USER`, `POSTGRES_DB`, dan `POSTGRES_PASSWORD` digunakan saat inisialisasi data directory yang masih kosong.

Jika database sudah diinisialisasi dan datanya disimpan dalam volume, mengubah `POSTGRES_PASSWORD` di `.env` tidak otomatis mengubah password role PostgreSQL yang sudah tersimpan.

Akibatnya, nilai password di environment container dapat berbeda dari password aktual role `h8_user`.

## 9. Menguji Password PostgreSQL Melalui TCP/IP

Gunakan perintah berikut untuk menguji password dari environment container dengan koneksi TCP/IP:

```bash
docker-compose exec postgres bash -c 'PGPASSWORD="$POSTGRES_PASSWORD" psql -h 127.0.0.1 -U "$POSTGRES_USER" -d "$POSTGRES_DB" -c "SELECT current_user, current_database();"'
```

Penjelasan:

- `PGPASSWORD`: mengambil password dari environment variable container.
- `-h 127.0.0.1`: memaksa koneksi melalui TCP/IP.
- `-U`: menentukan username.
- `-d`: menentukan database.

Jika berhasil, hasilnya akan menampilkan:

```text
 current_user | current_database
--------------+-----------------
 h8_user      | h8_database
(1 row)
```

Jika gagal dengan kesalahan autentikasi, password pada environment tidak cocok dengan password yang diterima untuk role tersebut.

## 10. Mengubah Password Role PostgreSQL

Jika password aktual perlu disamakan dengan nilai yang diinginkan, ubah password role tanpa menghapus database atau volume.

Masuk ke PostgreSQL:

```bash
docker-compose exec postgres psql -U h8_user -d h8_database
```

Di prompt `psql`, jalankan:

```sql
\password h8_user
```

Masukkan password baru dua kali ketika diminta. Perintah `\password` meminta input secara interaktif agar password tidak perlu ditulis langsung dalam perintah SQL.

Keluar:

```sql
\q
```

Kemudian:

1. Pastikan nilai `POSTGRES_PASSWORD` di `.env` sama dengan password baru jika memang ingin mempertahankan konfigurasi tersebut.
2. Ulangi pengujian TCP/IP pada bagian 9.
3. Masukkan password PostgreSQL yang sama pada konfigurasi koneksi server pgAdmin.

Mengubah password role tidak mengharuskan database atau tabel dihapus.

> Catatan: keberhasilan dan langkah perbaikan harus diverifikasi dengan hasil perintah. Jangan menganggap password sudah cocok hanya karena variabel tersedia.

## 11. Verifikasi Akhir

Lakukan pemeriksaan berikut untuk memastikan seluruh komponen berfungsi.

### 11.1. Status container

```bash
docker-compose ps
```

Target:

- `h8_postgres`: `Up (healthy)`.
- `h8_pgadmin`: `Up`.

### 11.2. Autentikasi PostgreSQL melalui TCP/IP

Jalankan perintah pengujian pada bagian 9 dan pastikan hasilnya menunjukkan:

```text
h8_user | h8_database
```

### 11.3. Koneksi pgAdmin

Buka:

```text
http://localhost:5050
```

Login ke pgAdmin, lalu hubungkan server menggunakan hostname `postgres`, port `5432`, database `h8_database`, user `h8_user`, dan password PostgreSQL yang benar.

### 11.4. Uji Query Tool

Jalankan:

```sql
SELECT
    current_database(),
    current_user,
    version();
```

Kemudian:

```sql
SELECT * FROM mahasiswa;
```

Pastikan database, user, versi PostgreSQL, dan data mahasiswa tampil sesuai harapan.

## 12. Ringkasan Troubleshooting

| Permasalahan | Analisis | Tindakan |
|---|---|---|
| pgAdmin loading | Perlu memastikan container dan aplikasi web | Periksa `docker-compose ps` dan log |
| `Did not find any relations` | Database belum memiliki tabel | Buat tabel dengan `CREATE TABLE` |
| Environment password tersedia | Belum membuktikan password role cocok | Uji autentikasi TCP/IP |
| `password authentication failed` | Password yang dikirim ditolak | Periksa dan sinkronkan password role |
| pgAdmin tidak terhubung | Host, port, atau kredensial mungkin salah | Gunakan hostname service `postgres`, port `5432`, dan kredensial benar |
| PostgreSQL `healthy` | Health check berhasil | Lanjutkan uji autentikasi dan query |

## 13. Perintah Penting untuk Referensi

### Aktivasi environment dan masuk direktori

```bash
conda activate h8_env
cd ~/Downloads/H8
```

### Menjalankan container

```bash
docker-compose up -d
```

### Memeriksa status container

```bash
docker-compose ps
```

### Melihat log PostgreSQL

```bash
docker-compose logs postgres
```

### Melihat log pgAdmin

```bash
docker-compose logs pgadmin
```

### Masuk ke PostgreSQL

```bash
docker-compose exec postgres psql -U h8_user -d h8_database
```

### Perintah psql yang sering digunakan

```sql
\l       -- melihat daftar database
\dt      -- melihat daftar tabel
\q       -- keluar dari psql
```

### Menghentikan layanan

```bash
docker-compose down
```

Perintah ini menghentikan dan menghapus container serta jaringan proyek. Named volume biasanya tetap ada.

**Jangan menjalankan `docker-compose down -v` sembarangan.** Opsi `-v` dapat menghapus volume database dan menyebabkan kehilangan data.

## 14. Kesimpulan Akhir

Instalasi PostgreSQL 17 dan pgAdmin 4 menggunakan Docker Compose telah berhasil melalui tahapan konfigurasi, pemeriksaan container, pengujian database, pembuatan tabel, operasi SQL, serta troubleshooting koneksi.

Masalah utama yang ditemukan adalah:

`FATAL: password authentication failed for user "h8_user"`

Analisis menunjukkan bahwa server PostgreSQL dapat dijangkau oleh pgAdmin, tetapi autentikasi password gagal. Salah satu penyebab yang perlu diperhatikan adalah perbedaan antara password yang tersimpan pada role PostgreSQL dengan nilai terbaru di file `.env`, karena perubahan environment variable tidak otomatis mengubah password role pada database yang sudah diinisialisasi.

Setelah konfigurasi password diselesaikan, koneksi pgAdmin berhasil digunakan.

### Status akhir berdasarkan pengujian

| Komponen | Hasil |
|---|---|
| Conda environment `h8_env` | Berhasil |
| Docker Compose | Berjalan |
| PostgreSQL 17 | Healthy |
| Database `h8_database` | Berhasil dibuat dan diakses |
| User `h8_user` | Berhasil digunakan |
| Tabel `mahasiswa` | Berhasil dibuat |
| INSERT | Berhasil |
| SELECT | Berhasil |
| pgAdmin 4 | Berhasil dibuka |
| Koneksi pgAdmin ke PostgreSQL | Berhasil |

**Lingkungan database siap digunakan untuk melanjutkan pembelajaran SQL dalam program Full Stack Data Science (FTDS).**

### Poin pembelajaran utama

1. Container berstatus `Up` belum otomatis membuktikan seluruh fungsi aplikasi berjalan dengan benar.
2. Database kosong bukan berarti database mengalami error.
3. Environment variable tidak selalu mengubah konfigurasi database yang sudah diinisialisasi.
4. Koneksi antarkontainer Docker Compose menggunakan nama service sebagai hostname.
5. Koneksi melalui Unix socket dan TCP/IP dapat memiliki metode autentikasi yang berbeda.
6. Troubleshooting sebaiknya dilakukan bertahap berdasarkan pesan error, bukan langsung menginstal ulang aplikasi.

### Catatan keamanan

- Ganti password sementara atau password yang pernah dibagikan dengan password kuat dan unik.
- Jangan membagikan isi file `.env`.
- Tambahkan `.env` ke `.gitignore`.
- Simpan cadangan database secara berkala.
- Hindari menghapus volume database tanpa backup dan pemahaman dampaknya.
