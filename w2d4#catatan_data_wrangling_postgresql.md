# 📝 Catatan Belajar PostgreSQL: Data Wrangling & Manipulasi Data (pgAdmin 4)

Catatan komprehensif ini dirancang untuk mendokumentasikan perintah-perintah SQL di PostgreSQL yang berfokus pada **Data Wrangling**—proses membersihkan, mengubah struktur, dan memetakan data mentah menjadi format yang siap untuk dianalisis (Transformasi Data / ELT).

---

## 1. Data Manipulation Language (DML)
### Insert & Select Data
Digunakan untuk memasukkan data uji coba ke dalam tabel `bikeshare_trips` dan menampilkan seluruh isinya.
```sql
-- Memasukkan beberapa baris data ke dalam tabel
INSERT INTO public.bikeshare_trips(
    trip_id, subscriber_type, bike_id, bike_type, start_time, 
    start_station_id, start_station_name, end_station_id, end_station_name, duration_minutes
)
VALUES 
    (9999999, 'public_bike', 456, 'electric', '2026-11-01', 1, 'station A', 10, 'station B', 10),
    (99999998, 'public_bike', 456, 'electric', '2026-11-01', 2, 'station A', 11, 'station B', 25),
    (99999997, 'private_bike', 456, 'electric', '2026-11-01', 1, 'station A', 10, 'station B', 5),
    (99999996, 'public_bike', 456, 'electric', '2026-11-01', 3, 'station A', 20, 'station B', 20),
    (99999995, 'private_bike', 456, 'electric', '2026-11-01', 3, 'station A', 15, 'station B', 70);

-- Menampilkan seluruh data tabel
SELECT * FROM bikeshare_trips;
```

---

## 2. Modifikasi Struktur Tabel & Tipe Data (DDL & Casting)
⚠️ **Penting:** Perubahan tipe data permanen menggunakan `ALTER TABLE` sebaiknya tidak dilakukan sembarangan di lingkungan kerja (production) karena dapat mengunci tabel (*table lock*) dan mengganggu anggota tim lain.

### Mengubah Tipe Data Secara Permanen (`ALTER TABLE`)
```sql
-- 1. Mengubah tipe data menjadi TEXT (Berhasil)
ALTER TABLE bikeshare_trips
ALTER COLUMN duration_minutes TYPE text;  

-- 2. Mengubah dari TEXT ke INT secara langsung (ERROR!)
-- PostgreSQL tidak tahu cara mengubah string ke angka secara otomatis jika data sudah ada
ALTER TABLE bikeshare_trips
ALTER COLUMN duration_minutes TYPE int;  

-- 3. Solusi: Menggunakan klausa USING untuk konversi eksplisit (Berhasil)
ALTER TABLE bikeshare_trips 
ALTER COLUMN duration_minutes TYPE integer USING duration_minutes::int;

-- Cek hasil perubahan tipe data
SELECT duration_minutes FROM bikeshare_trips;
```

### Mengubah Tipe Data Sementara (`CASTING`)
Digunakan saat query berjalan untuk manipulasi data tanpa mengubah struktur asli kolom di database.
```sql
-- Mengubah tipe data sementara ke INTEGER menggunakan fungsi CAST
SELECT CAST(duration_minutes AS integer) FROM bikeshare_trips;
```

---

## 3. Fungsi-Fungsi Manipulasi String (Teks)
Fungsi esensial untuk membersihkan, memotong, dan menyatukan data teks yang berantakan.
```sql
-- LEFT & RIGHT: Mengambil karakter dari urutan paling kiri atau kanan
SELECT 
    LEFT(start_station_name, 3) AS tiga_huruf_kiri, 
    RIGHT(start_station_name, 3) AS tiga_huruf_kanan,
    start_station_name
FROM bikeshare_trips;

-- SUBSTR: Mengambil potongan teks di posisi tertentu (mulai karakter ke-3, sepanjang 5 karakter)
SELECT 
    SUBSTR(start_station_name, 3, 5) AS potongan_teks, 
    start_station_name
FROM bikeshare_trips;

-- TRIM: Menghapus karakter tertentu di awal (LEADING), akhir (TRAILING), atau keduanya (BOTH)
SELECT 
    TRIM(LEADING 'sta' FROM start_station_name) AS teks_bersih, 
    start_station_name
FROM bikeshare_trips;

-- CONCAT: Menggabungkan beberapa kolom teks atau string menjadi satu
SELECT 
    start_station_name,
    end_station_name,
    CONCAT('dari : ', start_station_name, ' ke : ', end_station_name) AS rute_perjalanan
FROM bikeshare_trips;

-- UPPER & LOWER: Mengubah format teks menjadi huruf besar semua atau huruf kecil semua
SELECT
    UPPER(start_station_name) AS huruf_besar,
    LOWER(start_station_name) AS huruf_kecil
FROM bikeshare_trips;
```

---

## 4. Fungsi Manipulasi Tanggal (Date & Time)
```sql
-- EXTRACT: Mengambil bagian spesifik (Tahun, Bulan, Hari, Jam) dari kolom bertipe date/timestamp
SELECT 
    start_time,
    EXTRACT(YEAR FROM start_time) AS tahun_saja
FROM bikeshare_trips;

-- Menggunakan EXTRACT di dalam WHERE clause untuk memfilter data pada tanggal tertentu
SELECT * 
FROM bikeshare_trips
WHERE EXTRACT(YEAR FROM start_time) = 2026 
  AND EXTRACT(MONTH FROM start_time) = 11 
  AND EXTRACT(DAY FROM start_time) = 5;
```

---

## 5. Fungsi Agregasi dengan Casting
Fungsi statistik seperti `AVG`, `SUM`, `MAX`, `MIN` tidak bisa memproses data bertipe string/text secara langsung. Kita harus melakukan *casting* terlebih dahulu.
```sql
-- ERROR jika tipe data kolom duration_minutes masih TEXT:
-- SELECT avg(duration_minutes) FROM bikeshare_trips;

-- SOLUSI: Konversikan (CAST) ke integer terlebih dahulu di dalam fungsi AVG
SELECT AVG(CAST(duration_minutes AS int)) AS rata_rata_durasi
FROM bikeshare_trips;
```

---

## 6. Kondisional Menggunakan `CASE WHEN`
Digunakan untuk membuat logika kondisi (`IF-THEN-ELSE`) pada tampilan hasil query tanpa memfilter baris data (berbeda fungsinya dengan `WHERE`).
```sql
-- Pengelompokan durasi perjalanan (Kombinasi dengan CAST karena kolom bertipe TEXT)
SELECT 
    duration_minutes,
    -- Case 1: Pengelompokan teks panjang
    CASE
        WHEN CAST(duration_minutes AS int) < 10 THEN 'Short'
        WHEN CAST(duration_minutes AS int) BETWEEN 10 AND 50 THEN 'Medium'
        ELSE 'Long'
    END AS kategori_durasi,
    
    -- Case 2: Pengelompokan kode singkatan
    CASE
        WHEN CAST(duration_minutes AS int) < 10 THEN 'S'
        WHEN CAST(duration_minutes AS int) BETWEEN 10 AND 50 THEN 'M'
        ELSE 'L'
    END AS kode_durasi
FROM bikeshare_trips;
```

---

## 7. Subquery (Query di dalam Query)
Digunakan ketika ingin memfilter data berdasarkan hasil perhitungan dinamis dari query lain (misalnya mencari data yang di atas rata-rata).
```sql
-- Menampilkan semua perjalanan yang durasinya LEBIH BESAR dari rata-rata durasi keseluruhan
SELECT *
FROM bikeshare_trips
WHERE CAST(duration_minutes AS int) > (
    SELECT AVG(CAST(duration_minutes AS int))
    FROM bikeshare_trips
);
```

---

## 8. Mengubah Tipe Data Kolom Secara Aman (Advanced Wrangling)
Jika kolom berjenis `text` mengandung spasi, karakter non-angka, atau string kosong, melakukan *casting* langsung via `::int` akan menyebabkan *error*. Kita perlu membersihkannya menggunakan regex terlebih dahulu sebelum mengubah tipe datanya secara permanen.

```sql
-- Skenario: Mengubah tipe kolom duration_minutes dari TEXT ke INT secara aman
-- dengan menghapus karakter non-angka dan mengubah string kosong menjadi NULL
ALTER TABLE bikeshare_trips 
ALTER COLUMN duration_minutes 
	TYPE integer 
	USING NULLIF(REGEXP_REPLACE(duration_minutes, '\D', '', 'g'), '')::integer;

/* 
Penjelasan Wrangling:
- REGEXP_REPLACE(..., '\D', '', 'g') => Menghapus semua karakter yang BUKAN angka (\D).
- NULLIF(..., '') => Jika hasilnya string kosong '', ubah menjadi NULL agar tidak error saat di-cast ke int.
- ::integer => Mengubah teks bersih menjadi tipe data integer.
*/
```

---

## 9. Penanganan Data Kosong & Hilang (`NULL` Handling)
Data mentah dari sistem sering kali memiliki nilai `NULL`. Di tahap *wrangling*, kita harus mendefinisikan nilai pengganti (*fallback value*) agar tidak merusak perhitungan statistik.

```sql
-- COALESCE: Mengganti nilai NULL dengan nilai default / teks alternatif
SELECT 
    trip_id,
    COALESCE(end_station_name, 'Unknown Station') AS end_station_clean
FROM bikeshare_trips;

-- Logika kustom menggunakan CASE WHEN untuk data angka kosong
SELECT 
    trip_id,
    CASE 
        WHEN start_station_id IS NULL THEN 0 
        ELSE start_station_id 
    END AS start_station_clean
FROM bikeshare_trips;
```

---

## 10. Mengatasi Data Duplikat (*Deduplication*)
Data duplikat dapat merusak validitas analisis data. Kita bisa memfilter data unik menggunakan *Window Function* `ROW_NUMBER()`.

```sql
-- Memfilter data unik saja menggunakan Subquery (Hanya mengambil baris dengan row_num = 1)
SELECT * 
FROM (
    SELECT *,
        ROW_NUMBER() OVER(
            PARTITION BY trip_id 
            ORDER BY start_time DESC
        ) AS row_num
    FROM bikeshare_trips
) sub
WHERE row_num = 1;
```

---

## 11. Pengkondisian Data Massal & Fitur Flagging
Dalam *data wrangling*, `CASE WHEN` sering digunakan untuk **binning** (mengelompokkan data numerik) atau **flagging** (membuat penanda biner 1 dan 0).

```sql
SELECT 
    trip_id,
    bike_type,
    CAST(duration_minutes AS int) AS duration,
    -- Fitur Binning
    CASE
        WHEN CAST(duration_minutes AS int) < 15 THEN 'Short Trip'
        WHEN CAST(duration_minutes AS int) BETWEEN 15 AND 45 THEN 'Medium Trip'
        ELSE 'Long Trip'
    END AS trip_category,
    -- Fitur Flagging (1 jika konsumsi baterai berpotensi tinggi, 0 jika tidak)
    CASE 
        WHEN bike_type = 'electric' AND CAST(duration_minutes AS int) > 30 THEN 1 
        ELSE 0 
    END AS is_high_battery_consume
FROM bikeshare_trips;
```

---

## 12. Rekayasa Fitur Waktu (*Time Feature Engineering*)
Mengubah format tanggal/waktu mentah menjadi fitur dimensi baru yang siap dipakai untuk analisis tren bisnis berdasarkan waktu.

```sql
SELECT 
    trip_id,
    start_time,
    EXTRACT(HOUR FROM start_time::timestamp) AS start_hour, -- Jam mulainya perjalanan
    TO_CHAR(start_time::date, 'Day') AS day_of_week,       -- Nama hari lengkap (Senin, Selasa, dll.)
    CASE 
        WHEN EXTRACT(ISODOW FROM start_time::date) IN (6, 7) THEN 'Weekend'
        ELSE 'Weekday'
    END AS day_type                                        -- Pembeda Hari Kerja vs Akhir Pekan
FROM bikeshare_trips;
```

---

## 13. Pembersihan & Standardisasi Teks (String Cleaning)
Menghilangkan kesalahan input manusia seperti spasi berlebih (*trailing/leading spaces*) atau ketidakkonsistenan huruf besar-kecil.

```sql
SELECT 
    start_station_name,
    TRIM(BOTH ' ' FROM LOWER(start_station_name)) AS clean_station_name, -- Hapus spasi di ujung & kecilkan semua huruf
    REPLACE(start_station_name, 'station', 'Stn.') AS short_station_name -- Mengganti kata standar menjadi singkatan baku
FROM bikeshare_trips;
```

---

## 14. Ringkasan Agregasi untuk Analisis (Data Aggregation)
Tahap akhir *data wrangling* sebelum melempar dataset ke alat visualisasi data (seperti Tableau, PowerBI, atau Metabase) dengan mengelompokkan data berdasarkan dimensi utama.

```sql
SELECT 
    subscriber_type,
    bike_type,
    COUNT(trip_id) AS total_trips,
    ROUND(AVG(CAST(duration_minutes AS int)), 2) AS avg_duration_minutes
FROM bikeshare_trips
GROUP BY subscriber_type, bike_type
ORDER BY total_trips DESC;
```