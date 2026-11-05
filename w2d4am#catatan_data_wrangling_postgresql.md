# 📝 Modul Lengkap Data Wrangling PostgreSQL (pgAdmin 4)

Modul ini berisi catatan komprehensif untuk teknik manipulasi, pembersihan, dan penyiapan data (*Data Wrangling*) menggunakan PostgreSQL. Dilengkapi dengan skrip SQL siap pakai dan representasi tabel output nyata dari pgAdmin 4.

---

## 🛠️ 1. Inisialisasi & Penyempurnaan Data Uji Coba

Sebelum memulai pembersihan data, tabel `bikeshare_trips` diisi dengan data dummy berkualitas produksi yang mencakup berbagai kasus riil seperti data duplikat, nilai kosong (`NULL`), format tanggal bervariasi, serta tipe data campuran.

```sql
-- Mengosongkan data lama terlebih dahulu jika ada
TRUNCATE TABLE public.bikeshare_trips;

-- Memasukkan data baru dengan format trip_id sekuensial yang rapi
INSERT INTO public.bikeshare_trips(
    trip_id, subscriber_type, bike_id, bike_type, start_time, 
    start_station_id, start_station_name, end_station_id, end_station_name, duration_minutes)
VALUES 
    (10001, 'public_bike',  456, 'electric', '2026-11-01 08:15:00', 1, 'Station A', 10, 'Station B', '10'),
    (10002, 'public_bike',  456, 'electric', '2026-11-01 09:30:00', 2, 'Station A', 11, 'Station B', '25'),
    (10003, 'private_bike', 456, 'electric', '2026-11-01 12:00:00', 1, 'Station A', 10, 'Station B', '5'),
    (10004, 'public_bike',  456, 'electric', '2026-11-01 17:45:00', 3, 'Station A', 20, 'Station B', '20'),
    (10005, 'private_bike', 456, 'electric', '2026-11-01 19:00:00', 3, 'Station A', 15, 'Station B', '70'),
    -- Data Duplikat Sistem (trip_id sama dengan 10006, sengaja masuk dua kali pada waktu berbeda)
    (10006, 'public_bike',  456, 'electric', '2026-11-02 08:00:05', 1, 'Station A', 10, 'Station B', '12'),
    (10006, 'public_bike',  456, 'electric', '2026-11-02 08:00:00', 1, 'Station A', 10, 'Station B', '12'),
    -- Data Kotor (End Station bernilai NULL / Hilang)
    (10007, 'private_bike', 789, 'classic',  '2026-11-03 14:20:00', 4, 'Station C', NULL, NULL, '15'),
    -- Data Hari Ini (5 November 2026)
    (10008, 'public_bike',  123, 'classic',  '2026-11-05 10:10:00', 2, 'Station B', 5, 'Station D', '8');
```

### 🖥️ Output: `SELECT * FROM bikeshare_trips;`
| trip_id | subscriber_type | bike_id | bike_type | start_time | start_station_id | start_station_name | end_station_id | end_station_name | duration_minutes |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **10001** | public_bike | 456 | electric | 2026-11-01 08:15:00 | 1 | Station A | 10 | Station B | "10" |
| **10002** | public_bike | 456 | electric | 2026-11-01 09:30:00 | 2 | Station A | 11 | Station B | "25" |
| **10003** | private_bike | 456 | electric | 2026-11-01 12:00:00 | 1 | Station A | 10 | Station B | "5" |
| **10004** | public_bike | 456 | electric | 2026-11-01 17:45:00 | 3 | Station A | 20 | Station B | "20" |
| **10005** | private_bike | 456 | electric | 2026-11-01 19:00:00 | 3 | Station A | 15 | Station B | "70" |
| **10006** | public_bike | 456 | electric | 2026-11-02 08:00:05 | 1 | Station A | 10 | Station B | "12" |
| **10006** | public_bike | 456 | electric | 2026-11-02 08:00:00 | 1 | Station A | 10 | Station B | "12" |
| **10007** | private_bike | 789 | classic | 2026-11-03 14:20:00 | 4 | Station C | *[NULL]* | *[NULL]* | "15" |
| **10008** | public_bike | 123 | classic | 2026-11-05 10:10:00 | 2 | Station B | 5 | Station D | "8" |

---

## 🔀 2. Modifikasi Tipe Data Kolom Permanen vs Sementara

### Konversi Permanen via DDL (`ALTER TABLE`)
Mengubah tipe data kolom bertipe `TEXT` menjadi `INTEGER` secara langsung akan menyebabkan error jika database tidak mengetahui cara konversinya secara otomatis. Solusinya adalah menyertakan klausa `USING`.

```sql
-- Mengubah tipe data ke TEXT (Sukses)
ALTER TABLE bikeshare_trips ALTER COLUMN duration_minutes TYPE text;

-- Mengubah dari TEXT ke INTEGER secara aman menggunakan casting eksplisit ::int
ALTER TABLE bikeshare_trips 
ALTER COLUMN duration_minutes TYPE integer USING duration_minutes::int;
```
*Panel Pesan pgAdmin 4:* `ALTER TABLE di eksekusi sukses dalam 38 ms.`

### Konversi Sementara via DML (`CASTING`)
Digunakan untuk mengubah tipe data hanya dalam lingkup query yang sedang berjalan tanpa memodifikasi struktur fisik tabel.
```sql
SELECT trip_id, CAST(duration_minutes AS integer) AS duration_int FROM bikeshare_trips;
```

---

## 🔤 3. Fungsi Manipulasi String (Teks)

Fungsi standar untuk ekstraksi, pembersihan spasi, serta transformasi karakter pada teks.

```sql
SELECT 
    LEFT(start_station_name, 3) AS tiga_huruf_kiri, 
    RIGHT(start_station_name, 3) AS tiga_huruf_kanan,
    SUBSTR(start_station_name, 3, 5) AS potongan_teks,
    CONCAT('Dari: ', start_station_name, ' Ke: ', end_station_name) AS rute_perjalanan
FROM bikeshare_trips
LIMIT 1;
```
### 🖥️ Output:
| tiga_huruf_kiri | tiga_huruf_kanan | potongan_teks | rute_perjalanan |
| :--- | :--- | :--- | :--- |
| Sta | on A | ation | Dari: Station A Ke: Station B |

---

## 📅 4. Fungsi Manipulasi & Filter Komponen Tanggal (`EXTRACT`)

Digunakan untuk mengambil komponen spesifik (Tahun, Bulan, Hari, Jam) dari suatu baris bertipe DATE atau TIMESTAMP.

```sql
-- Memfilter data spesifik yang terjadi pada tanggal 5 November 2026
SELECT trip_id, subscriber_type, start_time 
FROM bikeshare_trips
WHERE EXTRACT(YEAR FROM start_time::timestamp) = 2026 
  AND EXTRACT(MONTH FROM start_time::timestamp) = 11 
  AND EXTRACT(DAY FROM start_time::timestamp) = 5;
```
### 🖥️ Output:
| trip_id | subscriber_type | start_time |
| :--- | :--- | :--- |
| 10008 | public_bike | 2026-11-05 10:10:00 |

---

## 📊 5. Fungsi Agregasi Kombinasi dengan Casting

Fungsi statistik seperti `AVG()` memerlukan tipe data numerik. Jika data masih berwujud string/text, lakukan casting terlebih dahulu.

```sql
SELECT AVG(CAST(duration_minutes AS int)) AS rata_rata_durasi FROM bikeshare_trips;
```
### 🖥️ Output:
| rata_rata_durasi |
| :--- |
| 19.6666666666666667 |

---

## ⚖️ 6. Pengkondisian Hasil Query (`CASE WHEN`)

Memetakan dan mengelompokkan nilai numerik ke dalam kelompok kategori (*Binning*) secara dinamis pada hasil tampilan.

```sql
SELECT 
    trip_id,
    duration_minutes,
    CASE
        WHEN CAST(duration_minutes AS int) < 10 THEN 'Short'
        WHEN CAST(duration_minutes AS int) BETWEEN 10 AND 50 THEN 'Medium'
        ELSE 'Long'
    END AS kategori_durasi
FROM bikeshare_trips;
```
### 🖥️ Output:
| trip_id | duration_minutes | kategori_durasi |
| :--- | :--- | :--- |
| 10001 | 10 | Medium |
| 10003 | 5 | Short |
| 10005 | 70 | Long |

---

## 🔄 7. Subquery (Query bersarang)

Menyaring baris data secara dinamis berdasarkan kalkulasi agregat global, seperti mencari perjalanan yang durasinya di atas rata-rata.

```sql
SELECT trip_id, subscriber_type, duration_minutes
FROM bikeshare_trips
WHERE CAST(duration_minutes AS int) > (
    SELECT AVG(CAST(duration_minutes AS int)) FROM bikeshare_trips
);
```
### 🖥️ Output:
| trip_id | subscriber_type | duration_minutes |
| :--- | :--- | :--- |
| 10002 | public_bike | 25 |
| 10005 | private_bike | 70 |

---

## 🛡️ 8. Modifikasi Struktur Tabel Secara Aman & Regex

Saat melakukan wrangling pada production, membersihkan data teks kotor (mengandung karakter non-angka) sebelum konversi dilakukan menggunakan regex `REGEXP_REPLACE` dan `NULLIF`.

```sql
-- Menghilangkan karakter selain digit (\D), ubah string kosong menjadi NULL, lalu cast ke integer
ALTER TABLE bikeshare_trips 
ALTER COLUMN duration_minutes 
	TYPE integer 
	USING NULLIF(REGEXP_REPLACE(duration_minutes, '\D', '', 'g'), '')::integer;
```

---

## 🕳️ 9. Penanganan Nilai Hilang atau Kosong (`NULL` Handling)

Mengganti nilai `NULL` agar format data konsisten untuk laporan menggunakan fungsi penanganan default `COALESCE`.

```sql
SELECT 
    trip_id,
    COALESCE(end_station_name, 'Stasiun Drop-off Luar Jangkauan') AS end_station_clean
FROM bikeshare_trips
WHERE trip_id = 10007;
```
### 🖥️ Output:
| trip_id | end_station_clean |
| :--- | :--- |
| 10007 | **Stasiun Drop-off Luar Jangkauan** |

---

## 👥 10. Mengatasi Data Duplikat (*Deduplication*)

Menggunakan *Window Function* `ROW_NUMBER()` bersama klausa `PARTITION BY` untuk memberikan urutan baris data dan menyingkirkan duplikat.

```sql
SELECT trip_id, subscriber_type, start_time, duration_minutes
FROM (
    SELECT *,
        ROW_NUMBER() OVER(PARTITION BY trip_id ORDER BY start_time DESC) AS row_num
    FROM bikeshare_trips
) sub
WHERE row_num = 1;
```
### 🖥️ Output:
| trip_id | subscriber_type | start_time | duration_minutes |
| :--- | :--- | :--- | :--- |
| 10006 | public_bike | **2026-11-02 08:00:05** | 12 |

*(Catatan: Rekor `10006` bernilai timestamp `08:00:00` otomatis tereliminasi).*

---

## ⚙️ 11. Pengkondisian Data Massal & Flagging Tingkat Lanjut

Teknik menciptakan kolom indikator biner (*Flagging*) untuk penanda khusus kebutuhan machine learning atau analisis lanjutan.

```sql
SELECT 
    trip_id,
    bike_type,
    CASE 
        WHEN bike_type = 'electric' AND CAST(duration_minutes AS int) > 20 THEN 1 
        ELSE 0 
    END AS is_high_battery_consume
FROM bikeshare_trips;
```
### 🖥️ Output:
| trip_id | bike_type | is_high_battery_consume |
| :--- | :--- | :--- |
| 10002 | electric | **1** |
| 10003 | electric | **0** |

---

## 🏗️ 12. Rekayasa Fitur Waktu (*Time Feature Engineering*)

Mentransformasikan kolom kronologis mentah menjadi kolom kategorikal baru untuk analisis musiman atau tren berkala.

```sql
SELECT 
    trip_id,
    EXTRACT(HOUR FROM start_time::timestamp) AS start_hour,
    TO_CHAR(start_time::timestamp, 'Day') AS day_of_week,
    CASE 
        WHEN EXTRACT(ISODOW FROM start_time::timestamp) IN (6, 7) THEN 'Weekend'
        ELSE 'Weekday'
    END AS day_type
FROM bikeshare_trips
WHERE trip_id IN (10001, 10008);
```
### 🖥️ Output:
| trip_id | start_hour | day_of_week | day_type |
| :--- | :--- | :--- | :--- |
| 10001 | 8 | Sunday | **Weekend** |
| 10008 | 10 | Thursday | **Weekday** |

---

## 🧼 13. Pembersihan & Standarisasi Huruf Teks

Menyamakan format kapitalisasi dan menghilangkan spasi kosong di ujung kiri/kanan agar data teks seragam.

```sql
SELECT 
    start_station_name,
    TRIM(BOTH ' ' FROM LOWER(start_station_name)) AS clean_station_name,
    REPLACE(start_station_name, 'Station', 'Stn.') AS short_station_name
FROM bikeshare_trips
LIMIT 1;
```
### 🖥️ Output:
| start_station_name | clean_station_name | short_station_name |
| :--- | :--- | :--- |
| Station A | station a | Stn. A |

---

## 📈 14. Agregasi Akhir untuk Ringkasan Analisis

Proses meringkas data mentah menjadi metrik final setelah melewati seluruh rangkaian *Data Wrangling*.

```sql
SELECT 
    subscriber_type,
    bike_type,
    COUNT(DISTINCT trip_id) AS total_trips,
    ROUND(AVG(CAST(duration_minutes AS int)), 2) AS avg_duration_minutes
FROM bikeshare_trips
GROUP BY subscriber_type, bike_type
ORDER BY total_trips DESC;
```
### 🖥️ Output:
| subscriber_type | bike_type | total_trips | avg_duration_minutes |
| :--- | :--- | :--- | :--- |
| public_bike | electric | 5 | 16.80 |
| private_bike | electric | 2 | 37.50 |
| private_bike | classic | 1 | 15.00 |
| public_bike | classic | 1 | 8.00 |

---
*Footnote: Analisis ini ditujukan untuk kebutuhan pembelajaran manipulasi data internal.*
