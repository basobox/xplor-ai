# Rangkuman Diskusi: Pemrograman Berorientasi Objek (OOP) Python

Dokumen ini berisi rangkuman lengkap dari pembahasan mengenai dasar-dasar **Object-Oriented Programming (OOP)** di Python, analisis kesalahan (*error traceback*), serta solusi kodenya.

---

## 1. Konsep Dasar OOP yang Dipelajari

1. **Class (Kelas):** Cetak biru (*blueprint*) untuk membuat objek (contoh: `class Orang`).
2. **Attribute (Atribut):** Variabel yang melekat pada objek (contoh: `self.nama` dan `self.umur`).
3. **Method (Metode):** Fungsi yang berada di dalam kelas dan dapat dijalankan oleh objek (contoh: `def perkenalan(self)`).
4. **Instance/Object (Objek):** Hasil wujud nyata yang dibuat berdasarkan kelas (contoh: `orang1`, `orang2`, `orang3`).

---

## 2. Masalah dan Analisis Error

### Pesan Error yang Dialami:
```text
Traceback (most recent call last):
  File "/home/live/Workspaces/gc.py", line 21, in <module>
    orang3 = str(input(f"Masukkan Umur Orang ke-3 : {orang3.umur}"))
                                                     ^^^^^^^^^^^
AttributeError: 'str' object has no attribute 'umur'
```

### Penyebab Error:
Error tersebut terjadi karena penulisan kode input berikut:

```python
orang3 = input(f"Masukkan Nama Orang ke-3 : {orang3.nama}")
orang3 = str(input(f"Masukkan Umur Orang ke-3 : {orang3.umur}"))
```

* **Menimpa Objek (*Variable Overwriting*):** 
  Variabel `orang3` yang semula berisi objek `Orang("", 0)` ditimpa oleh kembalian fungsi `input()`.
* **Perubahan Tipe Data:** 
  Setelah baris `orang3 = input(...)` dieksekusi, tipe data `orang3` berubah dari **`Orang`** (objek) menjadi **`str`** (string biasa, contoh: `"Cici"`).
* **Atribut Tidak Ditemukan:** 
  Ketika baris kedua dieksekusi, Python mencoba membaca `orang3.umur`. Karena `orang3` sudah menjadi string biasa, Python mengeluarkan pesan error `AttributeError: 'str' object has no attribute 'umur'`.

---

## 3. Solusi dan Perbaikan Kode

Untuk mengubah nilai di dalam objek tanpa merusak/menimpa objek itu sendiri, kita harus mengakses atributnya secara langsung:

1. **`orang3.nama = input(...)`** untuk memperbarui nama.
2. **`orang3.umur = int(input(...))`** untuk memperbarui umur sekaligus mengonversi input string menjadi integer (angka).

---

## 4. Kode Program Lengkap (Final & Berhasil)

Berikut adalah kode Python yang sudah diperbaiki dan berjalan sesuai harapan:

```python
# 1. Mendefinisikan kelas Orang
class Orang:
    # Konstruktor untuk inisialisasi atribut (nama dan umur)
    def __init__(self, nama, umur):
        self.nama = nama
        self.umur = umur

    # Method perkenalan()
    def perkenalan(self):
        print(f"Halo Python, nama saya {self.nama} dan sekarang saya berusia {self.umur} tahun.")


# 2. Membuat objek dari kelas Orang
orang1 = Orang("Ani", 32)
orang2 = Orang("Budi", 42)

# Membuat objek orang3 dengan nilai default awal
orang3 = Orang("", 0)

# 3. Mengisi/memperbarui atribut objek orang3 menggunakan input pengguna
orang3.nama = input("Masukkan Nama Orang ke-3 : ")
orang3.umur = int(input("Masukkan Umur Orang ke-3 : "))

print("-" * 40)

# 4. Mengakses atribut objek
print(f"Nama Orang 1: {orang1.nama}")    # Output: Ani

# 5. Memanggil method dari masing-masing objek
orang2.perkenalan()     # Output: Halo Python, nama saya Budi dan sekarang saya berusia 42 tahun.
orang3.perkenalan()     # Output: Sesuai dengan data yang dimasukkan melalui input
```

---

## 5. Poin Penting yang Perlu Diingat

* **Akses Atribut:** Gunakan notasi titik (`objek.atribut`) untuk membaca atau mengubah nilai atribut.
* **Tipe Data Input:** Fungsi `input()` di Python **selalu** mengembalikan tipe data String (`str`). Jika data yang dibutuhkan adalah angka, bungkus dengan `int()` atau `float()`.
* **Penyimpanan Variabel:** Jangan menimpa nama variabel objek (`orang3 = input(...)`) jika Anda masih ingin mempertahankan fungsi-fungsi atau atribut dari kelas objek tersebut.