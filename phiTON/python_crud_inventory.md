# Kesimpulan Belajar Python: CRUD Inventory Management

## 1. Tujuan latihan

Latihan ini membangun aplikasi sederhana **Inventory Management** untuk
supermarket menggunakan Python prosedural.

Fitur yang ditargetkan:

1.  **Create** --- menambahkan barang.
2.  **Read** --- menampilkan seluruh barang.
3.  **Update** --- mengubah data barang berdasarkan ID.
4.  **Delete** --- menghapus barang berdasarkan ID.

Latihan ini menggabungkan beberapa konsep dasar Python dalam satu
aplikasi kecil. Dengan begitu, teori langsung dipakai untuk
menyelesaikan masalah nyata.

------------------------------------------------------------------------

## 2. Struktur data yang digunakan

Data disimpan dalam sebuah list utama:

``` python
data = []
```

Setiap barang disimpan sebagai nested list dengan urutan:

``` python
[no, nama, kategori, harga]
```

Contoh:

``` python
data = [
    [1, "Beras", "Sembako", 75000],
    [2, "Minyak", "Sembako", 18000],
    [3, "Sabun", "Kebutuhan Rumah", 5000]
]
```

Struktur tersebut dapat dibaca seperti ini:

  Indeks        Isi
  ------------- -------------------
  `barang[0]`   ID / nomor barang
  `barang[1]`   Nama barang
  `barang[2]`   Kategori
  `barang[3]`   Harga

### Memahami list bersarang

-   `data` adalah list utama yang menampung semua barang.
-   `data[0]` mengambil record barang pertama.
-   `data[0][1]` mengambil nama barang pertama.
-   `data[1][3]` mengambil harga barang kedua.

**Penting:** ID barang bukan indeks list. ID adalah data yang disimpan
pada posisi `barang[0]`. ID digunakan untuk mencari barang, sedangkan
indeks list digunakan untuk mengakses posisi elemen.

------------------------------------------------------------------------

## 3. Konsep Python yang dipelajari

### A. Variabel

Variabel menyimpan nilai yang digunakan program.

``` python
nama = "Beras"
harga = 75000
```

### B. `input()`

`input()` menerima masukan dari pengguna dan hasilnya selalu berupa
string.

``` python
nama = input("Nama barang: ")
harga = int(input("Harga barang: "))
```

Karena harga akan diproses sebagai angka, hasil `input()` diubah
menggunakan `int()`.

### C. List dan `append()`

`append()` menambahkan satu elemen ke akhir list.

``` python
data.append([no, nama, kategori, harga])
```

Satu elemen yang ditambahkan adalah sebuah list berisi empat nilai.

### D. Kondisi `if` dan `else`

Digunakan untuk mengambil keputusan.

``` python
if not data:
    print("Data belum ada")
else:
    print("Data tersedia")
```

`if not data` berarti list `data` kosong.

### E. Perulangan `for`

Digunakan untuk membaca setiap record barang.

``` python
for barang in data:
    print(barang)
```

Pada setiap putaran, variabel `barang` menunjuk ke satu record di dalam
`data`.

### F. Unpacking

Unpacking membagi isi list ke beberapa variabel.

``` python
no, nama, kategori, harga = barang
```

Kode tersebut bekerja karena `barang` memiliki empat elemen.

### G. Fungsi

Setiap fitur dibuat sebagai fungsi agar tugas program terpisah dan lebih
mudah diuji.

``` python
def add_inventory(data):
    ...

def view_inventory(data):
    ...

def update_inventory(data):
    ...

def delete_inventory(data):
    ...
```

Parameter `data` membuat fungsi dapat bekerja pada list inventory yang
sama.

### H. `while True` dan `break`

`while True` menjalankan menu berulang kali.

``` python
while True:
    pilihan = input("Pilih menu: ")

    if pilihan == "0":
        break
```

`break` menghentikan perulangan. Pada latihan ini menu menggunakan
pilihan `0` untuk keluar. Jika mengikuti persyaratan awal secara persis,
kondisi keluar perlu diganti agar program berhenti ketika pengguna
mengetik `exit` (huruf kecil, case-sensitive).

------------------------------------------------------------------------

## 4. Fitur Create --- menambahkan barang

### Logika

1.  Membuat ID baru.
2.  Meminta nama barang.
3.  Meminta kategori.
4.  Meminta harga.
5.  Menambahkan satu record ke list utama.

### Kode

``` python
def add_inventory(data):
    no = len(data) + 1

    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = int(input("Harga: "))

    data.append([no, nama, kategori, harga])

    print("Data berhasil ditambahkan")
```

### Hal penting

`len(data)` menghitung jumlah record. Jika ada dua record, `len(data)`
menghasilkan `2`, sehingga ID berikutnya menjadi `3`.

**Catatan pengembangan:** cara `len(data) + 1` cukup untuk latihan awal,
tetapi jika barang dihapus, ID dapat terpakai kembali atau berpotensi
membingungkan. Aplikasi yang lebih matang sebaiknya memakai pembuat ID
yang selalu bertambah atau ID unik.

------------------------------------------------------------------------

## 5. Fitur Read --- menampilkan barang

### Logika

1.  Periksa apakah data kosong.
2.  Jika kosong, tampilkan pesan `Data belum ada`.
3.  Jika ada data, lakukan perulangan.
4.  Ambil setiap record dan tampilkan kolomnya.

### Kode

``` python
def view_inventory(data):
    if not data:
        print("Data belum ada")
    else:
        print("----- Daftar Barang -----")
        print("No | Nama | Kategori | Harga")

        for barang in data:
            no, nama, kategori, harga = barang
            print(f"{no} | {nama} | {kategori} | {harga}")
```

### Hal penting

Baris:

``` python
no, nama, kategori, harga = barang
```

membuat setiap nilai lebih mudah dibaca dan dicetak.

Judul tabel sebaiknya diletakkan sebelum `for`, bukan di dalam `for`,
supaya tidak dicetak berulang untuk setiap barang.

------------------------------------------------------------------------

## 6. Fitur Update --- mengubah barang

Update berarti **mengubah record yang sudah ada**, bukan menambahkan
record baru.

### Logika Update

1.  Minta ID barang yang ingin diubah.
2.  Siapkan penanda `ditemukan = False`.
3.  Periksa setiap record menggunakan `for`.
4.  Bandingkan ID pada `barang[0]` dengan ID yang dimasukkan pengguna.
5.  Jika cocok, ubah nilai pada indeks yang sesuai.
6.  Tandai barang ditemukan dan hentikan pencarian.
7.  Jika pencarian selesai tetapi barang tidak ditemukan, tampilkan
    pesan.

### Kode

``` python
def update_inventory(data):
    id_barang = int(input("Masukkan ID barang yang ingin diubah: "))

    ditemukan = False

    for barang in data:
        if barang[0] == id_barang:
            ditemukan = True

            print("Data lama:")
            print(barang)

            nama = input("Masukkan nama baru: ")
            kategori = input("Masukkan kategori baru: ")
            harga = int(input("Masukkan harga baru: "))

            barang[1] = nama
            barang[2] = kategori
            barang[3] = harga

            print("Data berhasil diperbarui")
            break

    if not ditemukan:
        print("Nomor barang tidak ditemukan")
```

### Mengapa menggunakan assignment, bukan `append()`?

Misalnya record awal:

``` python
[1, "Beras", "Sembako", 75000]
```

Untuk mengubah nama, gunakan:

``` python
barang[1] = "Beras Premium"
```

Hasilnya:

``` python
[1, "Beras Premium", "Sembako", 75000]
```

Jangan menggunakan:

``` python
barang.append("Beras Premium")
```

Karena `append()` menambahkan elemen baru di akhir record, bukan
mengganti nilai lama. Akibatnya struktur record menjadi tidak sesuai.

### Peran `ditemukan`

``` python
ditemukan = False
```

Diletakkan **sebelum perulangan** agar nilainya tidak direset pada
setiap putaran.

Ketika ID cocok:

``` python
ditemukan = True
```

Setelah perulangan:

``` python
if not ditemukan:
    print("Nomor barang tidak ditemukan")
```

Pemeriksaan ini dilakukan setelah `for` selesai. Jika diletakkan sebagai
`else` pada `if` di dalam loop, pesan tidak ditemukan bisa muncul untuk
setiap record yang tidak cocok.

### Peran `break`

`break` menghentikan pencarian setelah record yang cocok ditemukan dan
diubah.

------------------------------------------------------------------------

## 7. Fitur Delete --- menghapus barang

Delete berarti menghapus satu record dari list utama berdasarkan ID.

### Logika Delete

1.  Periksa apakah inventory kosong.
2.  Minta ID barang yang ingin dihapus.
3.  Siapkan penanda `ditemukan = False`.
4.  Cari record dengan membandingkan `barang[0]` dengan ID.
5.  Jika cocok, hapus record tersebut dari list utama.
6.  Tampilkan pesan berhasil dan hentikan perulangan.
7.  Jika tidak ditemukan, tampilkan pesan yang sesuai.

### Kode

``` python
def delete_inventory(data):
    if not data:
        print("Data belum ada")
        return

    id_barang = int(input("Masukkan ID barang yang ingin dihapus: "))

    ditemukan = False

    for barang in data:
        if barang[0] == id_barang:
            ditemukan = True

            data.remove(barang)

            print("Data berhasil dihapus")
            break

    if not ditemukan:
        print("Nomor barang tidak ditemukan")
```

### Memahami `data.remove(barang)`

Perhatikan perbedaan berikut:

``` python
barang[1] = "Nama Baru"
```

Mengubah satu nilai di dalam record.

``` python
data.remove(barang)
```

Menghapus seluruh record `barang` dari list utama.

`remove()` menerima nilai/objek yang ingin dihapus, bukan nomor indeks.

Alternatifnya adalah menggunakan `pop()` jika posisi indeks sudah
diketahui. Untuk latihan ini, `remove()` lebih mudah dipahami karena
kita sudah menemukan record yang cocok melalui perulangan.

**Catatan:** jangan menghapus record dengan `data.remove()` sebelum ID
cocok. Hapus hanya ketika kondisi `if barang[0] == id_barang` benar.

------------------------------------------------------------------------

## 8. Program lengkap CRUD Inventory

Kode berikut menggabungkan Create, Read, Update, Delete, dan menu utama.
Ini merupakan versi latihan dasar; validasi input angka dan pengelolaan
ID setelah penghapusan dapat ditingkatkan pada tahap refactoring.

``` python
data = []


# CREATE
def add_inventory(data):
    no = len(data) + 1

    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = int(input("Harga: "))

    data.append([no, nama, kategori, harga])

    print("Data berhasil ditambahkan")


# READ
def view_inventory(data):
    if not data:
        print("Data belum ada")
    else:
        print("----- Daftar Barang -----")
        print("No | Nama | Kategori | Harga")

        for barang in data:
            no, nama, kategori, harga = barang
            print(f"{no} | {nama} | {kategori} | {harga}")


# UPDATE
def update_inventory(data):
    if not data:
        print("Data belum ada")
        return

    id_barang = int(input("Masukkan ID barang yang ingin diubah: "))

    ditemukan = False

    for barang in data:
        if barang[0] == id_barang:
            ditemukan = True

            print("Data lama:", barang)

            nama = input("Masukkan nama baru: ")
            kategori = input("Masukkan kategori baru: ")
            harga = int(input("Masukkan harga baru: "))

            barang[1] = nama
            barang[2] = kategori
            barang[3] = harga

            print("Data berhasil diperbarui")
            break

    if not ditemukan:
        print("Nomor barang tidak ditemukan")


# DELETE
def delete_inventory(data):
    if not data:
        print("Data belum ada")
        return

    id_barang = int(input("Masukkan ID barang yang ingin dihapus: "))

    ditemukan = False

    for barang in data:
        if barang[0] == id_barang:
            ditemukan = True

            data.remove(barang)

            print("Data berhasil dihapus")
            break

    if not ditemukan:
        print("Nomor barang tidak ditemukan")


# MENU UTAMA
def main():
    while True:
        print("\n===== INVENTORY MANAGEMENT =====")
        print("1. Input Data")
        print("2. Menampilkan Data")
        print("3. Ubah Data")
        print("4. Hapus Data")
        print("0. Keluar")

        pilihan = input("Pilih menu: ")

        if pilihan == "1":
            add_inventory(data)

        elif pilihan == "2":
            view_inventory(data)

        elif pilihan == "3":
            update_inventory(data)

        elif pilihan == "4":
            delete_inventory(data)

        elif pilihan == "0":
            print("Program selesai")
            break

        else:
            print("PILIHAN TIDAK VALID")


main()
```

------------------------------------------------------------------------

## 9. Perjalanan satu record dalam CRUD

Misalnya pengguna menambahkan barang:

``` python
[1, "Beras", "Sembako", 75000]
```

Kemudian:

  Operasi   Perubahan
  --------- ---------------------------------------------
  Create    Record baru dimasukkan ke `data`
  Read      Record dibaca dan ditampilkan
  Update    Nilai pada indeks `1`, `2`, atau `3` diubah
  Delete    Seluruh record dihapus dari `data`

Inti CRUD dapat diringkas menjadi empat operasi list:

``` python
# CREATE
data.append(record)

# READ
for barang in data:
    print(barang)

# UPDATE
barang[1] = nilai_baru

# DELETE
data.remove(barang)
```

Keempat operasi ini adalah inti manipulasi data pada latihan inventory.

------------------------------------------------------------------------

## 10. Kesalahan penting yang perlu dihindari

  -----------------------------------------------------------------------
  Kesalahan               Mengapa salah           Perbaikan konsep
  ----------------------- ----------------------- -----------------------
  Menganggap ID sama      ID adalah nilai data,   Cari ID dengan
  dengan indeks list      indeks adalah posisi    `barang[0]`

  Menggunakan `append()`  `append()` menambah     Gunakan assignment
  untuk Update            elemen                  seperti
                                                  `barang[1] = nama`

  Menaruh                 Penanda terus direset   Letakkan sebelum loop
  `ditemukan = False` di                          
  dalam `for`                                     

  Membuat `for` kedua     Record yang cocok sudah Langsung ubah record
  setelah ID ditemukan    tersedia di variabel    tersebut
                          `barang`                

  Menampilkan pesan tidak Pesan bisa muncul pada  Periksa
  ditemukan di dalam      setiap record yang      `if not ditemukan`
  `else` loop             tidak cocok             setelah loop

  Lupa `break` setelah    Loop terus berjalan     Hentikan pencarian
  menemukan record        tanpa kebutuhan         setelah berhasil

  Menghapus berdasarkan   ID tidak selalu sama    Temukan record dulu,
  ID sebagai indeks       dengan posisi list      lalu `remove()`
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 11. Batasan versi latihan dan tahap perbaikan

Kode lengkap di atas sengaja dibuat sederhana agar konsep CRUD terlihat
jelas. Ada beberapa hal yang belum ditangani:

1.  **Validasi harga dan ID.** Jika pengguna memasukkan teks ketika
    program meminta angka, `int()` akan menghasilkan error.
2.  **ID setelah penghapusan.** `len(data) + 1` dapat menghasilkan ID
    yang pernah dipakai lagi setelah ada penghapusan.
3.  **Konfirmasi Delete.** Program langsung menghapus setelah ID
    ditemukan; aplikasi nyata sebaiknya meminta konfirmasi.
4.  **Update sebagian kolom.** Versi ini meminta nama, kategori, dan
    harga baru sekaligus. Aplikasi lanjutan dapat mengizinkan pengguna
    mengubah satu kolom saja.
5.  **Penyimpanan permanen.** Data masih berada di memori. Ketika
    program ditutup, data hilang karena belum disimpan ke file atau
    database.

Batasan tersebut bukan alasan untuk langsung membuat program rumit.
Selesaikan dahulu logika dasar, kemudian lakukan refactoring secara
bertahap.

------------------------------------------------------------------------

## 12. Urutan belajar berikutnya

Urutan latihan yang disarankan:

1.  **Procedural CRUD:** pastikan Create, Read, Update, dan Delete
    dipahami dan dapat ditulis sendiri.
2.  **Refactoring:** rapikan fungsi, validasi input, penanganan ID, dan
    pemisahan tanggung jawab.
3.  **CRUD dengan Dictionary:** pahami penyimpanan key-value dan akses
    berdasarkan key.
4.  **CRUD kombinasi:** gunakan list, tuple, dan dictionary sesuai
    kebutuhan.
5.  **OOP CRUD:** bangun ulang aplikasi inventory yang sama menggunakan
    class dan object.
6.  **Penyimpanan data:** pelajari file handling atau database setelah
    alur CRUD dipahami.

## 13. Kesimpulan akhir

CRUD adalah latihan yang efektif untuk menghubungkan banyak konsep dasar
Python dalam satu aplikasi yang aplikatif.

-   **List** menjadi tempat menyimpan kumpulan record.
-   **Nested list** menyimpan atribut setiap barang.
-   **Indexing** mengambil atau mengubah bagian record.
-   **Loop** mencari dan membaca record.
-   **Condition** menentukan apakah ID ditemukan.
-   **Function** memisahkan tanggung jawab setiap fitur.
-   **`append()`** menambahkan record.
-   **Assignment** mengubah nilai record.
-   **`remove()`** menghapus record.
-   **`while`** menjaga menu tetap berjalan.

Hal terpenting bukan menghafal kode CRUD, melainkan memahami bagaimana
data bergerak: dibuat, dibaca, diubah, dan dihapus. Setelah alur ini
benar-benar dipahami secara prosedural, membangun ulang aplikasi yang
sama menggunakan OOP akan jauh lebih masuk akal karena masalah bisnis
dan alur datanya sudah dikenal.
