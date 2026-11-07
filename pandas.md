# Rangkuman Istilah: Row, Column, Record, Field, dan Attribute

Perbedaan utama dari istilah-istilah ini terletak pada **konteks penggunaannya** (Spreadsheet vs Database) dan **sudut pandangnya** (Teori/Desain vs Fisik/Komputer).

## 1. Tabel Perbandingan Utama

| Posisi / Bentuk | Konteks Spreadsheet (Excel/Sheets) | Konteks Database (SQL/Oracle) | Sudut Pandang Desain (Teori) | Penjelasan Sederhana |
| :--- | :--- | :--- | :--- | :--- |
| **Horizontal** (Kiri ke Kanan) | **Row** (Baris) | **Record** | **Entity Instance** (Tuple) | Kumpulan data lengkap milik satu objek/subjek. |
| **Vertikal** (Atas ke Bawah) | **Column** (Kolom) | **Field** | **Attribute** (Karakteristik) | Kategori atau jenis informasi spesifik yang seragam. |
| **Kotak Satuan** | **Cell** (Sel) | **Value** (Nilai Data) | **Value** | Isi data tunggal di persimpangan horizontal & vertikal. |

---

## 2. Alasan Perbedaan Istilah (Kenapa Ada Banyak Nama?)

### Row vs Record
* **Row:** Merujuk pada tampilan fisik garis horizontal di layar (di Excel ditandai dengan Angka `1, 2, 3`).
* **Record:** Merujuk pada makna datanya. Satu baris horizontal tersebut adalah satu kesatuan rekam jejak data utuh (misal: data lengkap milik 1 mahasiswa).

### Field vs Attribute
Perbedaan keduanya berasal dari perbedaan sudut pandang antara teori desain dan implementasi teknis:
* **Attribute (Sudut Pandang Logika/Desain):** 
  * Merupakan karakteristik atau sifat yang melekat pada suatu objek di dunia nyata.
  * *Analogi:* Atribut dari objek **Mobil** adalah `Warna`, `Merek`, dan `Tahun Rilis`. Istilah ini digunakan saat merancang database di atas kertas.
* **Field (Sudut Pandang Fisik/Komputer):**
  * Merupakan wadah, area, atau ruang penyimpanan fisik di dalam komputer yang disediakan untuk menampung isi dari atribut tersebut.
  * *Analogi:* Kotak kosong pada formulir pendaftaran tempat Anda mengetikkan nama atau alamat Anda.

---

## 3. Analogi Dunia Nyata: Formulir Kertas

Pikirkan selembar formulir kertas pendaftaran sekolah:
* **Field / Attribute:** Kotak kosong bertuliskan **Nama**, **Alamat**, dan **No. HP**. Kotak-kotak ini menentukan jenis data apa yang boleh diisi.
* **Record / Row:** Satu lembar formulir fisik yang sudah diisi lengkap oleh seorang pendaftar dari atas sampai bawah.
