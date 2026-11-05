# Modul Catatan Belajar: Integrasi, Pembersihan, dan Manipulasi Pandas dengan PostgreSQL

Dokumen ini adalah panduan lengkap yang merangkum seluruh kendala (*error*), langkah *troubleshooting*, teknik pembersihan data, penggabungan tabel, hingga optimasi performa skala besar menggunakan Pandas dan PostgreSQL.

---

## 1. Kendala: `ModuleNotFoundError` (Pandas Tidak Ditemukan)

### Masalah
Saat mencoba melakukan `import pandas as pd` di Jupyter Notebook, muncul error:
```text
ModuleNotFoundError: No module named 'pandas'
```

### Penyebab
Library Pandas belum terinstal di dalam *environment* Python (`h8_env`) yang sedang digunakan aktif oleh Jupyter Notebook.

### Solusi & Troubleshoot
Jalankan perintah instalasi langsung di dalam cell Jupyter Notebook menggunakan tanda seru `!`:
```python
!pip install pandas
```
**Langkah Wajib:** Setelah proses instalasi selesai, lakukan **Restart Kernel** pada menu Jupyter Notebook di bagian atas agar sistem mendeteksi library baru tersebut.

---

## 2. Kendala: Kegagalan Perintah `pip install` di Terminal

### Masalah
Saat mengetik perintah instalasi di terminal Bash, muncul error:
```text
ERROR: Could not find a version that satisfies the requirement install
ERROR: No matching distribution found for install
```

### Penyebab
Terjadi salah pengetikan (*typo*) berupa pengulangan kata `install` sebanyak dua kali (`pip install pandas install ...`), sehingga sistem mengira Anda ingin menginstal library bernama `"install"`.

### Solusi & Troubleshoot
Ketik perintah dengan benar di terminal tanpa mengulang kata kerja dan tanpa tanda seru `!`:
```bash
pip install sqlalchemy psycopg2-binary
```

---

## 3. Kendala: `SyntaxError` / `DatabaseError` pada `.to_sql()`

### Masalah
Ketika mengeksekusi perintah `newDf.to_sql("new_dataframe", connection)`, program memunculkan error sintaksis:
```text
UserWarning: pandas only supports SQLAlchemy connectable...
SyntaxError: syntax error at or near ";"
LINE 8:             AND name=?;
                              ^
```

### Penyebab
Secara default, jika dilewati objek koneksi DBAPI biasa seperti `psycopg2`, Pandas akan mengasumsikan koneksi berbasis **SQLite**. Akibatnya, Pandas mengirimkan placeholder tanda tanya (`?`), sedangkan PostgreSQL hanya memahami placeholder versi mereka (`%s`).

### Solusi & Troubleshoot
Pandas mewajibkan penggunaan **SQLAlchemy Engine** untuk menjembatani perbedaan dialek SQL tersebut.
1. Impor `create_engine` dari library `sqlalchemy`.
2. Masukkan URI koneksi dengan format yang benar dan ganti teks penampung dengan password asli Anda (misalnya: `'1234'`).

---

## 4. Optimasi Performa untuk Data Berukuran Besar

Secara standar, Pandas memasukkan data ke database baris demi baris (satu per satu). Untuk data berjumlah puluhan ribu hingga jutaan baris, proses ini akan memakan waktu sangat lama. Dua parameter berikut wajib digunakan untuk melakukan optimasi:

* **`chunksize=10000`**: Memotong DataFrame menjadi beberapa bagian kecil agar dikirim secara bertahap, menghindari komputer kehabisan memori (*out of memory*).
* **`method='multi'`**: Mengaktifkan fitur *bulk insert*, yaitu menggabungkan banyak baris data ke dalam satu perintah SQL tunggal sebelum dikirim ke PostgreSQL (memotong waktu eksekusi dari menit ke detik).

---

## 5. Pembersihan Data (*Data Cleaning*) Sebelum Ekspor

Sebelum data dikirim ke database, data yang kotor harus dibersihkan terlebih dahulu agar tidak merusak kualitas analisis.

### A. Menangani Data Kosong (*Missing Values*)
* **`isnull().sum()`**: Melakukan pengecekan total data kosong di setiap kolom.
* **`dropna(subset=['kolom'])`**: Menghapus baris jika kolom penting tertentu bernilai kosong.
* **`fillna(nilai)`**: Mengisi nilai kosong dengan angka statis, teks khusus (seperti `'Unknown'`), atau nilai rata-rata (`mean()`).

### B. Menghapus Data Duplikat
* **`duplicated().sum()`**: Mengecek jumlah baris yang kembar.
* **`drop_duplicates(subset=['id'], keep='first')`**: Menghapus data kembar berdasarkan kolom kunci dan mempertahankan baris pertama yang ditemukan.

### C. Menyaring Data Pencilan (*Outliers*) dengan Metode IQR
Data ekstrem yang tidak masuk akal (misal durasi bernilai negatif atau angka yang terlalu besar karena eror sistem) dibuang menggunakan rumus *Interquartile Range* (IQR):
```python
Q1 = df['duration'].quantile(0.25)
Q3 = df['duration'].quantile(0.75)
IQR = Q3 - Q1
batas_atas = Q3 + 1.5 * IQR

# Menyaring hanya data yang berada di rentang normal
df_clean = df[(df['duration'] > 0) & (df['duration'] <= batas_atas)]
```
*Efek Outliers:* Membuang data pencilan akan mengembalikan nilai rata-rata (*mean*) ke angka yang logis dan mencerminkan perilaku mayoritas data yang sebenarnya.

---

## 6. Manipulasi dan Penggabungan Tabel (*Merge / Join*)

### A. Operasi DataFrame Populer
* **`.astype()`**: Mengubah tipe data kolom (misal dari teks ke angka bulat atau kategori).
* **`.sort_values(by='kolom', ascending=False)`**: Mengurutkan data dari nilai terbesar ke terkecil.
* **`.query()`**: Menyaring data berdasarkan banyak kondisi dengan sintaksis yang bersih mirip SQL.
* **`.value_counts()`**: Menghitung frekuensi kemunculan nilai unik pada kolom kategori.

### B. Penggabungan Tabel dengan `pd.merge()`
Digunakan untuk menyatukan tabel transaksi (data perjalanan) dengan tabel master (data stasiun) berdasarkan kolom kunci tertentu, setara dengan perintah `JOIN` di SQL.
* **`how='left'`**: Mempertahankan semua baris di tabel kiri meskipun tidak ada kecocokan di tabel kanan.
* **`how='inner'`**: Hanya mengambil baris data yang kuncinya cocok dan ada di kedua tabel.

---

## 7. Skrip Implementasi Akhir (End-to-End)

Berikut adalah kode Python utuh yang menggabungkan seluruh fungsionalitas: Koneksi, *Query*, Pembersihan Data, Penggabungan Tabel (*Left Join*), hingga Ekspor Teroptimasi ke PostgreSQL.

```python
import pandas as pd
import numpy as np
from sqlalchemy import create_engine

# 1. Inisialisasi Engine Koneksi ke PostgreSQL
engine = create_engine('postgresql+psycopg2://h8_user:1234@localhost:5432/h8_database')

# 2. Membaca Tabel Awal dari Database
df_perjalanan = pd.read_sql("SELECT id, duration, start_station_id, rider_type FROM dc_bikeshare_q1_2012", con=engine)
df_stasiun = pd.read_sql("SELECT station_id, station_name FROM bikeshare_stations", con=engine)

# 3. Pembersihan Data (Cleaning)
# Hapus ID kosong & data duplikat
df_perjalanan = df_perjalanan.dropna(subset=['id'])
df_perjalanan = df_perjalanan.drop_duplicates(subset=['id'], keep='first')

# Isi data tipe rider yang kosong
df_perjalanan['rider_type'] = df_perjalanan['rider_type'].fillna('Unknown')

# Filter Outliers Durasi menggunakan metode IQR
Q1 = df_perjalanan['duration'].quantile(0.25)
Q3 = df_perjalanan['duration'].quantile(0.75)
IQR = Q3 - Q1
batas_top = Q3 + 1.5 * IQR
df_perjalanan_clean = df_perjalanan[(df_perjalanan['duration'] > 0) & (df_perjalanan['duration'] <= batas_top)]

# 4. Penggabungan Tabel (Merge / Left Join)
df_enriched = pd.merge(
    df_perjalanan_clean, 
    df_stasiun, 
    left_on='start_station_id', 
    right_on='station_id', 
    how='left'
)
# Buang kolom kunci duplikat agar rapi
df_enriched = df_enriched.drop(columns=['station_id'])

# 5. Analisis Agregasi Sederhana
print("=== RANGKUMAN STATISTIK RIDER ===")
rangkuman = df_enriched.groupby('rider_type')['duration'].agg(['count', 'mean', 'sum'])
rangkuman.columns = ['Total Perjalanan', 'Rata-rata Durasi (Detik)', 'Total Durasi']
print(rangkuman)
print("\n")

# 6. Ekspor Kembali Hasil Akhir ke PostgreSQL dengan Optimasi Data Besar
df_enriched.to_sql(
    name="bikeshare_enriched_report", 
    con=engine, 
    if_exists='replace', 
    index=False,
    chunksize=10000,
    method='multi'
)

print("Proses selesai! Data super bersih dan kaya berhasil disimpan ke tabel 'bikeshare_enriched_report'.")
```
