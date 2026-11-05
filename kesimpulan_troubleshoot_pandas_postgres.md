# Ringkasan Kendala, Solusi, dan Analisis Data (Pandas & PostgreSQL)

Dokumen ini merangkum seluruh kendala (*error*), langkah *troubleshooting*, optimasi performa, pembersihan data kosong, hingga contoh analisis data menggunakan Pandas yang telah didiskusikan.

---

## 1. Kendala Pertama: `ModuleNotFoundError`

### Masalah
Saat mencoba melakukan `import pandas as pd` di Jupyter Notebook, muncul error berikut:
```text
ModuleNotFoundError: No module named 'pandas'
```

### Penyebab
Library Pandas belum terinstal di dalam *environment* Python (`h8_env`) yang sedang digunakan oleh Jupyter Notebook.

### Solusi & Troubleshoot
* **Di dalam Jupyter Notebook:** Jalankan perintah instalasi menggunakan tanda seru `!` agar dieksekusi langsung di terminal *environment* aktif.
  ```python
  !pip install pandas
  ```
* **Langkah Penting:** Setelah instalasi selesai, lakukan **Restart Kernel** pada Jupyter Notebook agar sistem dapat mendeteksi library Pandas yang baru diinstal.

---

## 2. Kendala Kedua: Kegagalan Sintaksis `pip install` di Terminal

### Masalah
Saat mencoba menginstal library tambahan di terminal, muncul error dari pip:
```text
(h8_env) live@minios:~/Workspaces$ pip install pandas install sqlalchemy psycopg2-binary
ERROR: Could not find a version that satisfies the requirement install (from versions: none)
ERROR: No matching distribution found for install
```

### Penyebab
Terjadi kesalahan pengetikan (*typo*) berupa pengulangan kata `install` sebanyak dua kali (`pip install pandas install ...`), sehingga `pip` mengira pengguna ingin menginstal sebuah paket/library bernama `"install"`.

### Solusi & Troubleshoot
Perbaiki perintah di terminal Bash (tanpa tanda seru `!` dan tanpa double kata `install`):
```bash
pip install sqlalchemy psycopg2-binary
```

---

## 3. Kendala Ketiga: `SyntaxError` / `DatabaseError` saat menggunakan `to_sql()`

### Masalah
Ketika mengeksekusi perintah `newDf.to_sql("new_dataframe", connection)`, program memunculkan error sintaksis SQL dan peringatan (*Warning*):
```text
UserWarning: pandas only supports SQLAlchemy connectable... Other DBAPI2 objects are not tested.

SyntaxError: syntax error at or near ";"
LINE 8:             AND name=?;
                              ^
DatabaseError: ...
```

### Penyebab
1. **Ketidakcocokan Driver:** Secara default, metode `.to_sql()` dari Pandas mengasumsikan koneksi berbasis **SQLite** jika dilewati objek koneksi DBAPI biasa (`psycopg2`).
2. **Perbedaan Placeholder:** Koneksi PostgreSQL bawaan `psycopg2` menggunakan placeholder `%s`, sedangkan SQLite menggunakan `?`. Karena Pandas mengira itu SQLite, ia mengirimkan sintaksis berlambang `?` yang tidak dipahami oleh PostgreSQL.

### Solusi & Troubleshoot
Pandas mewajibkan penggunaan **SQLAlchemy Engine** untuk menjembatani komunikasi dialek SQL dengan PostgreSQL.

1. Gunakan `create_engine` dari `sqlalchemy`.
2. Pastikan string koneksi menggunakan format URI:  
   `postgresql+psycopg2://username:password@host:port/database`
3. Masukkan password riil (misal: `'1234'`) ke dalam URI koneksi, bukan teks penampung seperti `'PASSWORD_ANDA'`.

---

## 4. Optimasi Performa untuk Data Berukuran Besar

Secara *default*, Pandas memasukkan data ke database baris demi baris (satu per satu) sehingga lambat untuk data berjumlah ribuan hingga jutaan. Dua parameter berikut wajib ditambahkan untuk optimasi:

* **`chunksize=10000`**: Memotong DataFrame menjadi beberapa bagian (per 10.000 baris) agar dikirim secara bertahap dan mencegah kendala memori komputer (*out of memory*).
* **`method='multi'`**: Mengubah perintah SQL menjadi *bulk insert*, menggabungkan banyak baris data ke dalam satu perintah SQL tunggal sebelum dikirim ke PostgreSQL (memotong waktu eksekusi dari menit menjadi detik).

---

## 5. Pembersihan Data Kosong (*Missing Values*) sebelum Ekspor

Sebelum data ditransfer ke PostgreSQL, disarankan untuk menangani nilai kosong (`NaN` atau `None`) agar konsistensi tipe data di database terjaga:

* **`newDf.isnull().sum()`**: Melakukan *sanity check* untuk menghitung jumlah baris kosong di setiap kolom.
* **`dropna(subset=['id'])`**: Menghapus satu baris penuh jika kolom penanda utama (seperti `id`) bernilai kosong.
* **`fillna(nilai_baru)`**: Mengisi nilai kosong dengan nilai substitusi, misalnya mengisi kolom numerik `duration` dengan nilai rata-rata (`mean`) atau kolom kategori `rider_type` dengan teks `'Unknown'`.

---

## 6. Penerapan Analisis Data dengan Pandas

Setelah data berhasil disimpan dengan aman, Pandas menyediakan berbagai fungsi *powerhouse* untuk mengambil kembali dan menganalisis data langsung dari database:

* **`pd.read_sql()`**: Membaca hasil query tabel database dan mengubahnya kembali menjadi objek DataFrame Pandas.
* **`df.shape`**: Properti instan untuk mendeteksi total baris dan kolom yang sukses masuk ke database tanpa perlu fungsi `COUNT` manual.
* **`df.groupby()`**: Mengelompokkan data berdasarkan kategori tertentu (misalnya `rider_type`).
* **`.mean()` / `.agg()`**: Menghitung rata-rata (*mean*) durasi perjalanan atau melakukan agregasi gabungan (Total data `count`, Rata-rata `mean`, dan Jumlah total `sum`) secara sekaligus.

---

## 7. Struktur Kode Akhir yang Sempurna (Include Data Cleaning)

Berikut adalah kode komparatif utuh yang lengkap, aman, bersih, dan efisien:

```python
from sqlalchemy import create_engine
import psycopg2
import pandas as pd

# ==========================================
# 1. KONEKSI & READ DATA (PSOCYPG2)
# ==========================================
connection = psycopg2.connect(
    database='h8_database',
    user='h8_user',
    password='1234',
    host='localhost',
    port=5432
)

# (Opsional) Mengubah data langsung dari table mentah menjadi DataFrame Pandas awal
# df = pd.read_sql("SELECT * FROM dc_bikeshare_q1_2012", connection)

# ==========================================
# 2. FILTER & DATA CLEANING
# ==========================================
# Memilih kolom tertentu
newDf = df[['id', 'duration', 'rider_type']]

print("=== CHECK DATA KOSONG SEBELUM PEMBERSIHAN ===")
print(newDf.isnull().sum())
print("\n")

# A. Hapus baris jika kolom 'id' kosong
newDf = newDf.dropna(subset=['id'])

# B. Isi kolom 'duration' yang kosong dengan nilai rata-rata (mean)
rerata_durasi_awal = newDf['duration'].mean()
newDf['duration'] = newDf['duration'].fillna(rerata_durasi_awal)

# C. Isi kolom 'rider_type' yang kosong dengan teks 'Unknown'
newDf['rider_type'] = newDf['rider_type'].fillna('Unknown')

print("=== CHECK DATA KOSONG SETELAH PEMBERSIHAN ===")
print(newDf.isnull().sum())
print("\n")

# ==========================================
# 3. EKSPOR DENGAN OPTIMASI (SQLALCHEMY)
# ==========================================
# Buat engine baru dengan password riil '1234'
engine = create_engine('postgresql+psycopg2://h8_user:1234@localhost:5432/h8_database')

# Menyimpan ke SQL dengan fitur Bulk Insert & Chunking untuk data besar
newDf.to_sql(
    name="new_dataframe", 
    con=engine, 
    if_exists='replace', 
    index=False,
    chunksize=10000,
    method='multi'
)
print("Data bersih sukses disimpan ke tabel 'new_dataframe' dengan performa optimal!\n")

# ==========================================
# 4. MEMBACA ULANG & ANALISIS DATA DENGAN PANDAS
# ==========================================
# Ambil kembali data dari tabel yang baru saja kita simpan
df_db = pd.read_sql("SELECT * FROM new_dataframe", con=engine)

print("=== A. MENGECEK TOTAL BARIS ===")
total_baris, total_kolom = df_db.shape
print(f"Total baris di DB: {total_baris:,} baris | Total kolom: {total_kolom} kolom\n")

print("=== B. RATA-RATA DURASI BERDASARKAN TIPE RIDER ===")
# Mencari rerata durasi berdasarkan tipe rider
rerata_durasi = df_db.groupby('rider_type')['duration'].mean()
print(rerata_durasi)
print("\n")

print("=== C. AGREGASI LENGKAP (TOTAL PERJALANAN, RERATA, TOTAL DURASI) ===")
# Rangkuman agregat statistik deskriptif sekaligus
rangkuman_rider = df_db.groupby('rider_type')['duration'].agg(['count', 'mean', 'sum'])
rangkuman_rider.columns = ['Total Perjalanan', 'Rata-rata Durasi', 'Total Durasi (Detik)']
print(rangkuman_rider)
```
