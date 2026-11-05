# Panduan Menjalankan Docker Compose – Hacktiv8 Data Science

## 1. Tujuan

Menyiapkan lingkungan database untuk mengikuti materi Hacktiv8 Data Science menggunakan:

- Docker
- Docker Compose
- PostgreSQL 17
- pgAdmin 4

Docker digunakan untuk menjalankan PostgreSQL dan pgAdmin dalam container sehingga tidak perlu menginstal kedua aplikasi database tersebut secara langsung ke sistem operasi.

## 2. Langkah-Langkah Menjalankan Docker Compose

### Langkah 1 – Buka Terminal dan Masuk ke Folder Project

Project berada di:

```bash
~/Downloads/H8
```

Jalankan:

```bash
cd ~/Downloads/H8
```

Periksa isi folder:

```bash
ls -la
```

Pastikan terdapat file konfigurasi:

```text
H8/
├── compose.yaml
└── .env
```

### Langkah 2 – Periksa Konfigurasi Environment

Buka file `.env`:

```bash
nano .env
```

File ini menyimpan konfigurasi database dan pgAdmin.

Contoh struktur:

```env
POSTGRES_DB=h8_database
POSTGRES_USER=h8_user
POSTGRES_PASSWORD=password_database

PGADMIN_DEFAULT_EMAIL=admin@gmail.com
PGADMIN_DEFAULT_PASSWORD=password_pgadmin
```

Sesuaikan nama variabel dengan yang digunakan dalam `compose.yaml`.

Simpan menggunakan:
- `Ctrl + O` → Enter
- `Ctrl + X`

**Catatan:** Gunakan email dengan domain yang valid untuk akun pgAdmin.

### Langkah 3 – Periksa Konfigurasi Docker Compose

Buka:

```bash
nano compose.yaml
```

Konfigurasi menjalankan dua layanan:

1. PostgreSQL 17
2. pgAdmin 4

Gambaran struktur konfigurasi:

```yaml
services:
  postgres:
    image: postgres:17
    container_name: h8_postgres
    ports:
      - "127.0.0.1:5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U h8_user -d h8_database"]
      interval: 10s
      timeout: 5s
      retries: 5

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: h8_pgadmin
    ports:
      - "127.0.0.1:5050:80"
    volumes:
      - pgadmin_data:/var/lib/pgadmin
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  postgres_data:
  pgadmin_data:
```

Sesuaikan konfigurasi `environment` dan referensi variabel dengan isi `.env` yang digunakan.

### Langkah 4 – Validasi Konfigurasi

Sebelum menjalankan container, periksa konfigurasi:

```bash
docker-compose config
```

Perintah ini berguna untuk:
- Memeriksa sintaks YAML.
- Memeriksa konfigurasi layanan.
- Memeriksa interpolasi variabel `.env`.

Jika konfigurasi valid, lanjutkan.

### Langkah 5 – Jalankan Docker Compose

Jalankan:

```bash
docker-compose up -d
```

Keterangan:

| Perintah | Fungsi |
|---|---|
| `docker-compose` | Menjalankan Docker Compose |
| `up` | Membuat dan menjalankan container |
| `-d` | Menjalankan di background (detached mode) |

Jika ingin memaksa pembuatan ulang container:

```bash
docker-compose up -d --force-recreate
```

### Langkah 6 – Periksa Status Container

```bash
docker-compose ps
```

Periksa apakah kedua container berjalan:

- `h8_postgres`
- `h8_pgadmin`

Status yang diharapkan adalah `Up`, sedangkan PostgreSQL juga harus sehat (`healthy`).

### Langkah 7 – Buka pgAdmin

Buka browser dan akses:

```text
http://localhost:5050
```

Login menggunakan:

- Email: sesuai `PGADMIN_DEFAULT_EMAIL`
- Password: sesuai `PGADMIN_DEFAULT_PASSWORD`

### Langkah 8 – Hubungkan pgAdmin dengan PostgreSQL

Setelah berhasil masuk ke pgAdmin:

1. Klik kanan Servers.
2. Pilih Register → Server.
3. Pada tab General, masukkan nama server, misalnya `H8 PostgreSQL`.
4. Buka tab Connection.
5. Isi konfigurasi berikut:

| Parameter | Nilai |
|---|---|
| Host name/address | `postgres` |
| Port | `5432` |
| Maintenance database | `h8_database` |
| Username | `h8_user` |
| Password | Sesuai `.env` |

Klik Save.

**Penting:** Gunakan `postgres` sebagai hostname, bukan `localhost`, karena pgAdmin mengakses PostgreSQL melalui jaringan internal Docker Compose.

---

## 3. Perintah Docker Compose yang Penting

| Perintah | Fungsi |
|---|---|
| `docker-compose up -d` | Menjalankan container |
| `docker-compose down` | Menghentikan dan menghapus container |
| `docker-compose ps` | Memeriksa status container |
| `docker-compose logs` | Melihat log semua layanan |
| `docker-compose logs -f` | Memantau log secara langsung |
| `docker-compose logs pgadmin` | Melihat log pgAdmin |
| `docker-compose logs postgres` | Melihat log PostgreSQL |
| `docker-compose restart` | Restart seluruh layanan |
| `docker-compose stop` | Menghentikan container |
| `docker-compose start` | Menjalankan kembali container yang berhenti |
| `docker-compose config` | Memvalidasi konfigurasi |
| `docker-compose down -v` | Menghapus container sekaligus volume |

**Perhatian:** Jangan menggunakan `docker-compose down -v` sembarangan karena volume menyimpan data PostgreSQL dan pgAdmin.

---

## 4. Troubleshooting Docker Compose

### Masalah 1 – Docker Compose Tidak Bisa Dijalankan

Gejala:

```text
docker-compose: command not found
```

Penyebab:

Docker Compose belum terinstal atau executable belum tersedia.

Solusi:

Periksa versi:

```bash
docker-compose --version
```

Jika belum tersedia, lakukan instalasi Docker Compose.

### Masalah 2 – Error `exec format error`

Gejala:

```text
exec format error
```

Penyebab:

Binary Docker Compose yang sebelumnya digunakan tidak sesuai dengan format atau arsitektur sistem.

Solusi yang dilakukan:

1. Menghapus plugin Docker Compose yang rusak.
2. Menggunakan paket Docker Compose dari repositori Debian.
3. Memeriksa kembali versi instalasi.

Periksa executable:

```bash
which docker-compose
```

Kemudian:

```bash
docker-compose --version
```

### Masalah 3 – Error Validasi Email pgAdmin

Gejala pada log:

```text
Validation output:
The part after the @-sign is a special-use or reserved name
that cannot be used with email.

False
```

Penyebab:

Alamat email pada konfigurasi pgAdmin tidak memenuhi validasi email. Domain yang digunakan kemungkinan merupakan domain khusus atau reserved.

Solusi:

Edit `.env`:

```bash
nano .env
```

Ganti email dengan alamat email yang valid dan menggunakan domain publik.

Contoh:

```env
PGADMIN_DEFAULT_EMAIL=admin@gmail.com
```

Pastikan password juga sudah diisi.

Kemudian jalankan ulang:

```bash
docker-compose up -d --force-recreate
```

Periksa log:

```bash
docker-compose logs pgadmin
```

### Masalah 4 – pgAdmin Mengalami `Problem Loading Page`

Gejala:

Container pgAdmin sudah dijalankan, tetapi ketika membuka:

```text
http://localhost:5050
```

Halaman masih menampilkan:

```text
Problem Loading Page
```

Langkah troubleshooting:

**1. Periksa status container**

```bash
docker-compose ps
```

**2. Periksa log pgAdmin**

```bash
docker-compose logs --tail=100 pgadmin
```

**3. Periksa log PostgreSQL**

```bash
docker-compose logs --tail=100 postgres
```

**4. Periksa port**

```bash
docker-compose port pgadmin 80
```

Pastikan port host `5050` diarahkan ke port container `80`.

**5. Periksa konfigurasi**

```bash
docker-compose config
```

**6. Restart layanan**

```bash
docker-compose restart
```

**7. Jika perlu, buat ulang container**

```bash
docker-compose up -d --force-recreate
```

Jangan menghapus volume database ketika melakukan troubleshooting biasa.

Catatan status terakhir: masalah `Problem Loading Page` masih belum terkonfirmasi selesai dalam percakapan. Diperlukan pemeriksaan log pgAdmin dan status container untuk menemukan penyebab pastinya.

---

## 5. Proses Instalasi Docker Compose di Debian 13

### Langkah 1 – Periksa Sistem Operasi

```bash
cat /etc/os-release
```

Sistem yang digunakan adalah Debian 13.

### Langkah 2 – Periksa Instalasi Docker

```bash
docker --version
```

Periksa apakah Docker berjalan:

```bash
sudo systemctl status docker
```

Jika belum aktif:

```bash
sudo systemctl start docker
```

Agar otomatis berjalan saat boot:

```bash
sudo systemctl enable docker
```

### Langkah 3 – Periksa Docker Compose

```bash
docker-compose --version
```

Pada proses instalasi sebelumnya ditemukan masalah pada plugin Compose yang menghasilkan:

```text
exec format error
```

Plugin yang rusak kemudian dihapus.

### Langkah 4 – Instal Docker Compose dari Repositori Debian

Gunakan:

```bash
sudo apt update
```

Kemudian instal:

```bash
sudo apt install docker-compose
```

Instalasi ini menggunakan paket Docker Compose yang tersedia di repositori Debian.

Versi yang berhasil terpasang dalam percakapan:

```text
Docker Compose version 2.26.1
```

### Langkah 5 – Verifikasi Instalasi

```bash
docker-compose --version
```

Periksa lokasi executable:

```bash
which docker-compose
```

Pastikan perintah dapat dijalankan tanpa error.

### Langkah 6 – Jalankan Kembali Project H8

Masuk ke folder:

```bash
cd ~/Downloads/H8
```

Validasi konfigurasi:

```bash
docker-compose config
```

Jalankan:

```bash
docker-compose up -d
```

Periksa:

```bash
docker-compose ps
```

---

## 6. Ringkasan Alur Penggunaan

Urutan penggunaan project setiap kali ingin menjalankan PostgreSQL dan pgAdmin:

```bash
# 1. Masuk folder project
cd ~/Downloads/H8

# 2. Periksa konfigurasi
docker-compose config

# 3. Jalankan container
docker-compose up -d

# 4. Periksa status
docker-compose ps

# 5. Periksa log jika ada masalah
docker-compose logs -f
```

Akses pgAdmin melalui browser:

```text
http://localhost:5050
```

Untuk menghentikan project:

```bash
docker-compose down
```

Untuk menjalankannya kembali:

```bash
docker-compose up -d
```

---

## 7. Kesimpulan

Dari proses instalasi dan konfigurasi, terdapat beberapa hal penting yang perlu dipahami:

1. Docker digunakan untuk menjalankan aplikasi dalam container.
2. Docker Compose digunakan untuk mengelola beberapa container sekaligus.
3. PostgreSQL berfungsi sebagai database server.
4. pgAdmin merupakan antarmuka grafis untuk mengelola PostgreSQL.
5. File `compose.yaml` mengatur layanan, port, volume, dan hubungan antarkontainer.
6. File `.env` menyimpan konfigurasi seperti username, password, dan email.
7. Perintah `docker-compose up -d` digunakan untuk menjalankan project.
8. Perintah `docker-compose ps` dan `docker-compose logs` sangat penting untuk troubleshooting.
9. Volume Docker berfungsi mempertahankan data meskipun container dihentikan atau dibuat ulang.
10. Pada Debian 13, instalasi Docker Compose berhasil dilakukan melalui paket repositori Debian dengan versi 2.26.1.

**Status akhir:**
- Instalasi Docker Compose: berhasil.
- PostgreSQL dan pgAdmin: sudah dikonfigurasi.
- Validasi email pgAdmin: ditemukan masalah dan diarahkan untuk diperbaiki.
- Akses pgAdmin: masih mengalami `Problem Loading Page` pada akhir percakapan.
- Solusi akhir untuk masalah halaman pgAdmin: masih memerlukan pemeriksaan log lebih lanjut.
