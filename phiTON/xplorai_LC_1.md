# Rangkuman Live Code 1 -- Set 2

## Employee Payroll System

**Nama:** Nawafi Suren\
**Batch:** HCK-47\
**Program:** Hacktiv8 Data Science Fulltime Program

------------------------------------------------------------------------

## 1. Tujuan Live Code

Live Code 1 mengevaluasi pemahaman materi:

-   Python Conditional (`if`, `elif`, `else`)
-   Python Loops (`for`)
-   Python Functions
-   `kwargs` atau variable-length keyword arguments
-   List dan operasi penjumlahan
-   Computational Thinking
-   Pembuatan flowchart

## 2. Soal Resmi: Employee Payroll System

Perusahaan ingin menghitung penghasilan bulanan karyawan berdasarkan
jabatan dan jumlah jam kerja.

### Ketentuan soal nomor 1

Buat function bernama `hitung_penghasilan(jabatan, jam_kerja)`.

Jabatan dan gaji tetap:

  Jabatan     Gaji tetap per bulan
  --------- ----------------------
  Manager             Rp10.000.000
  Staff                Rp6.000.000
  Kasir                Rp4.000.000

Ketentuan lembur:

-   Jam kerja normal adalah 160 jam per bulan.
-   Setiap jam kerja di atas 160 jam dibayar Rp20.000.
-   Jam lembur dihitung hanya dari kelebihan jam kerja, yaitu
    `jam_kerja - 160`.

Contoh yang diminta:

  Pemanggilan                                     Hasil
  -------------------------------------- --------------
  `hitung_penghasilan("Manager", 180)`     Rp10.400.000
  `hitung_penghasilan("Staff", 200)`        Rp6.800.000
  `hitung_penghasilan("Kasir", 160)`        Rp4.000.000

### Ketentuan soal nomor 2

Buat function `total_pengeluaran` untuk menghitung total pengeluaran
perusahaan dalam satu bulan.

Persyaratan:

1.  Menerima banyak data karyawan menggunakan `**kwargs`.
2.  Membuat list kosong untuk menampung gaji setiap karyawan.
3.  Menggunakan looping.
4.  Memanggil function `hitung_penghasilan` dari soal nomor 1.
5.  Menambahkan hasil gaji ke list menggunakan `.append()`.
6.  Menjumlahkan semua gaji menggunakan `sum()`.
7.  Mengembalikan total pengeluaran.

Contoh:

``` python
total_pengeluaran(Manager=180, Staff=200, Kasir=160)
# Rp21.200.000

total_pengeluaran(Manager=210, Staff=170, Kasir=190)
# Rp21.800.000
```

### Ketentuan soal nomor 3

Buat flowchart menggunakan aplikasi Miro, sesuai alur algoritma program,
lalu simpan atau ekspor sebagai gambar.

## 3. Evaluasi Kode Awal Nawafi

Kode awal sudah memuat:

-   Input jabatan dan jam kerja.
-   Function `hitung_penghasilan`.
-   Conditional untuk menentukan gaji pokok berdasarkan jabatan.
-   Tarif tambahan Rp20.000 per jam.
-   Pemeriksaan apakah jam kerja lebih dari 160 jam.

Namun, terdapat beberapa kekurangan:

  -----------------------------------------------------------------------
  Requirement                         Kondisi kode awal
  ----------------------------------- -----------------------------------
  Function `hitung_penghasilan`       Sudah ada

  Tiga pilihan jabatan                Sudah ada

  Gaji pokok sesuai jabatan           Sudah sesuai

  Perhitungan lembur                  Salah: seluruh jam kerja dikalikan
                                      Rp20.000

  Menggunakan `return`                Belum, masih menggunakan `print()`

  Function `total_pengeluaran`        Belum dibuat

  `**kwargs`                          Belum digunakan

  Looping                             Belum digunakan

  List kosong dan `.append()`         Belum digunakan

  `sum()`                             Belum digunakan

  Docstring                           Belum ada

  Flowchart Miro                      Belum disertakan

  Deskripsi program                   Masih membahas cleaning data
                                      teks/sentimen, tidak sesuai kasus
                                      payroll
  -----------------------------------------------------------------------

### Kesalahan perhitungan lembur

Kode awal menggunakan:

``` python
tambahan_upah = jam_kerja * upah_per_jam

if jam_kerja > 160:
    total_upah = upah_per_bulan + tambahan_upah
```

Untuk Manager dengan 180 jam, kode awal menghitung:

``` text
Rp10.000.000 + (180 × Rp20.000)
= Rp13.600.000
```

Padahal hanya 20 jam yang merupakan lembur:

``` text
Jam lembur = 180 - 160 = 20 jam
Upah lembur = 20 × Rp20.000 = Rp400.000
Total = Rp10.000.000 + Rp400.000
      = Rp10.400.000
```

Jadi, perhitungan yang benar hanya mengenakan upah lembur pada jam kerja
di atas 160 jam.

## 4. Jawaban Program yang Diperbaiki

### Soal 1 -- Function `hitung_penghasilan`

``` python
def hitung_penghasilan(jabatan, jam_kerja):
    """
    Menghitung penghasilan karyawan berdasarkan jabatan
    dan jumlah jam kerja per bulan.

    Parameter:
    jabatan (str): Manager, Staff, atau Kasir.
    jam_kerja (int): Jumlah jam kerja dalam satu bulan.

    Return:
    int: Total penghasilan karyawan.
    """

    if jabatan.lower() == "manager":
        upah_per_bulan = 10_000_000
    elif jabatan.lower() == "staff":
        upah_per_bulan = 6_000_000
    elif jabatan.lower() == "kasir":
        upah_per_bulan = 4_000_000
    else:
        raise ValueError("Jabatan tidak dikenal")

    upah_lembur = 20_000

    if jam_kerja > 160:
        jam_lembur = jam_kerja - 160
        tambahan_upah = jam_lembur * upah_lembur
    else:
        tambahan_upah = 0

    total_upah = upah_per_bulan + tambahan_upah

    return total_upah
```

Pengujian:

``` python
print(hitung_penghasilan("Manager", 180))
print(hitung_penghasilan("Staff", 200))
print(hitung_penghasilan("Kasir", 160))
```

Output:

``` text
10400000
6800000
4000000
```

### Soal 2 -- Function `total_pengeluaran`

``` python
def total_pengeluaran(**data_karyawan):
    """
    Menghitung total pengeluaran perusahaan untuk membayar
    seluruh gaji karyawan dalam satu bulan.

    Parameter:
    **data_karyawan: Jabatan dan jam kerja setiap karyawan.

    Return:
    int: Total pengeluaran gaji seluruh karyawan.
    """

    daftar_gaji = []

    for jabatan, jam_kerja in data_karyawan.items():
        gaji = hitung_penghasilan(jabatan, jam_kerja)
        daftar_gaji.append(gaji)

    total_gaji = sum(daftar_gaji)

    return total_gaji
```

Pengujian:

``` python
hasil_1 = total_pengeluaran(
    Manager=180,
    Staff=200,
    Kasir=160
)

hasil_2 = total_pengeluaran(
    Manager=210,
    Staff=170,
    Kasir=190
)

print("Total Pengeluaran : Rp {:,}".format(hasil_1).replace(",", "."))
print("Total Pengeluaran : Rp {:,}".format(hasil_2).replace(",", "."))
```

Output:

``` text
Total Pengeluaran : Rp 21.200.000
Total Pengeluaran : Rp 21.800.000
```

## 5. Penjelasan Konsep Python

### 5.1 `input()`

`input()` digunakan untuk menerima masukan dari pengguna. Hasil
`input()` selalu berupa string.

``` python
jabatan = input("Jabatan: ")
```

Jika masukan berupa angka dan akan digunakan untuk perhitungan,
konversikan ke integer:

``` python
jam_kerja = int(input("Jumlah jam kerja: "))
```

Soal ini tidak mewajibkan `input()`. Function dapat dipanggil langsung
dengan argument seperti `hitung_penghasilan("Manager", 180)`.

### 5.2 Conditional: `if`, `elif`, dan `else`

Conditional digunakan untuk menjalankan instruksi berdasarkan kondisi.

``` python
if jabatan == "manager":
    upah_per_bulan = 10_000_000
elif jabatan == "staff":
    upah_per_bulan = 6_000_000
elif jabatan == "kasir":
    upah_per_bulan = 4_000_000
else:
    raise ValueError("Jabatan tidak dikenal")
```

-   `if`: memeriksa kondisi pertama.
-   `elif`: memeriksa kondisi alternatif jika kondisi sebelumnya tidak
    terpenuhi.
-   `else`: dijalankan jika semua kondisi sebelumnya tidak terpenuhi.

### 5.3 Method `.lower()`

`.lower()` mengubah seluruh huruf dalam string menjadi huruf kecil.

``` python
jabatan = "MANAGER"
print(jabatan.lower())
```

Output:

``` text
manager
```

Mengapa digunakan?

Agar input `"Manager"`, `"MANAGER"`, dan `"manager"` dapat dikenali
sebagai jabatan yang sama.

Catatan: `.lower()` menghasilkan string baru; nilai variabel asal tidak
berubah kecuali hasilnya disimpan kembali.

### 5.4 `raise ValueError()`

`raise` digunakan untuk memunculkan exception secara sengaja.
`ValueError` adalah jenis exception bawaan Python yang dapat digunakan
ketika nilai yang diberikan tidak valid.

``` python
raise ValueError("Jabatan tidak dikenal")
```

Jika jabatan tidak sesuai pilihan, program memunculkan error dengan
pesan tersebut. Exception yang tidak ditangani akan menghentikan alur
normal program.

### 5.5 Function dan `return`

Function adalah blok kode yang dibuat untuk menjalankan tugas tertentu
dan dapat dipanggil kembali.

``` python
def hitung_penghasilan(jabatan, jam_kerja):
    ...
    return total_upah
```

`return` mengirimkan hasil dari function kepada bagian program yang
memanggilnya.

Perbedaan:

-   `print()` menampilkan informasi ke layar.
-   `return` mengembalikan nilai agar dapat digunakan lagi dalam
    perhitungan.

Dalam soal ini, `return` penting karena function `total_pengeluaran`
harus menggunakan hasil dari `hitung_penghasilan`.

### 5.6 `**kwargs`

`**kwargs` memungkinkan function menerima sejumlah argument berbentuk
keyword argument.

``` python
def contoh(**data):
    print(data)

contoh(Manager=180, Staff=200, Kasir=160)
```

Output:

``` text
{'Manager': 180, 'Staff': 200, 'Kasir': 160}
```

Di dalam function, `data` berupa dictionary. Setiap jabatan menjadi key
dan jam kerja menjadi value.

### 5.7 Looping `for`

Looping digunakan untuk menjalankan instruksi berulang kali pada setiap
data.

``` python
for jabatan, jam_kerja in data_karyawan.items():
    print(jabatan, jam_kerja)
```

`.items()` menghasilkan pasangan key dan value dari dictionary.

### 5.8 List dan `.append()`

List adalah struktur data untuk menyimpan beberapa nilai.

``` python
daftar_gaji = []
```

List kosong dibuat untuk menampung hasil perhitungan gaji.

`.append()` menambahkan satu nilai ke bagian akhir list.

``` python
daftar_gaji.append(gaji)
```

### 5.9 Fungsi `sum()`

`sum()` menjumlahkan seluruh angka dalam iterable, misalnya list.

``` python
daftar_gaji = [10400000, 6800000, 4000000]

total_gaji = sum(daftar_gaji)
print(total_gaji)
```

Output:

``` text
21200000
```

### 5.10 String Formatting: `.format()`

`.format()` memasukkan nilai ke dalam placeholder `{}` pada string.

``` python
hasil = 21200000
print("Total: Rp {}".format(hasil))
```

Output:

``` text
Total: Rp 21200000
```

Format angka `"{:,}"` menambahkan pemisah ribuan berupa koma:

``` python
print("{:,}".format(hasil))
```

Output:

``` text
21,200,000
```

### 5.11 String Method `.replace()`

`.replace(teks_lama, teks_baru)` mengganti kemunculan teks lama dengan
teks baru.

``` python
angka = "21,200,000"
print(angka.replace(",", "."))
```

Output:

``` text
21.200.000
```

Digabungkan untuk menampilkan format Rupiah:

``` python
hasil = 21200000

print("Total Pengeluaran : Rp {:,}".format(hasil).replace(",", "."))
```

Output:

``` text
Total Pengeluaran : Rp 21.200.000
```

Alternatif menggunakan f-string:

``` python
print(f"Total Pengeluaran : Rp {hasil:,}".replace(",", "."))
```

### 5.12 Number Formatting: `f`, `.0f`, `.2f`

Format `f` digunakan untuk menampilkan angka dalam bentuk fixed-point
(desimal).

-   `:.0f`: nol digit di belakang koma.
-   `:.1f`: satu digit di belakang koma.
-   `:.2f`: dua digit di belakang koma.
-   `:.3f`: tiga digit di belakang koma.

Contoh:

``` python
angka = 123.456789

print("{:.0f}".format(angka))
print("{:.1f}".format(angka))
print("{:.2f}".format(angka))
print("{:.3f}".format(angka))
```

Output:

``` text
123
123.5
123.46
123.457
```

Format `{:,.2f}` menggabungkan pemisah ribuan dan dua digit desimal:

``` python
angka = 10000000
print("{:,.2f}".format(angka))
```

Output:

``` text
10,000,000.00
```

Untuk format Rupiah tanpa angka desimal:

``` python
print("{:,.0f}".format(angka).replace(",", "."))
```

Output:

``` text
10.000.000
```

**Mengapa `{:.}` tidak tepat?**

Titik setelah titik dua dalam format seperti `{:.2f}` digunakan untuk
mengatur presisi desimal, bukan pemisah ribuan. Karena itu `{:.}` tidak
lengkap: jumlah digit desimal dan tipe format belum ditentukan.

Pemisah ribuan menggunakan `,` dalam format Python, lalu dapat diganti
menjadi titik dengan `.replace(",", ".")`.

## 6. Ringkasan Perbedaan Sintaks

  -----------------------------------------------------------------------
  Sintaks                 Jenis                   Kegunaan
  ----------------------- ----------------------- -----------------------
  `input()`               Function                Menerima masukan
                                                  pengguna

  `print()`               Function                Menampilkan informasi

  `if`, `elif`, `else`    Keyword                 Membuat percabangan

  `def`                   Keyword                 Mendefinisikan function

  `return`                Keyword                 Mengembalikan nilai
                                                  dari function

  `raise`                 Keyword                 Memunculkan exception

  `ValueError`            Exception class         Menandai nilai yang
                                                  tidak valid

  `.lower()`              String method           Mengubah teks menjadi
                                                  huruf kecil

  `.format()`             String method           Memasukkan nilai ke
                                                  string

  `.replace()`            String method           Mengganti teks

  `**kwargs`              Sintaks argument        Menerima keyword
                                                  argument berjumlah
                                                  variabel

  `.items()`              Dictionary method       Mengambil pasangan key
                                                  dan value

  `.append()`             List method             Menambahkan elemen ke
                                                  list

  `sum()`                 Built-in function       Menjumlahkan angka

  `:.2f`                  Format specifier        Menampilkan dua digit
                                                  desimal

  `:,.0f`                 Format specifier        Pemisah ribuan dan
                                                  tanpa digit desimal
  -----------------------------------------------------------------------

## 7. Flowchart Algoritma

### 7.1 Flowchart `hitung_penghasilan`

``` mermaid
flowchart TD
    A([Start]) --> B[/Input jabatan dan jam kerja/]
    B --> C{Jabatan?}
    C -->|Manager| D[Gaji pokok 10 juta]
    C -->|Staff| E[Gaji pokok 6 juta]
    C -->|Kasir| F[Gaji pokok 4 juta]
    C -->|Tidak valid| X[Pesan error]
    D --> G{Jam kerja > 160?}
    E --> G
    F --> G
    G -->|Ya| H[Jam lembur = jam kerja - 160]
    H --> I[Upah lembur = jam lembur x 20.000]
    G -->|Tidak| J[Upah lembur = 0]
    I --> K[Total = gaji pokok + upah lembur]
    J --> K
    K --> L[/Return total gaji/]
    L --> M([End])
    X --> M
```

### 7.2 Flowchart `total_pengeluaran`

``` mermaid
flowchart TD
    A([Start]) --> B[/Terima data kwargs/]
    B --> C[Inisialisasi list kosong]
    C --> D{Masih ada data karyawan?}
    D -->|Ya| E[Ambil jabatan dan jam kerja]
    E --> F[Panggil hitung_penghasilan]
    F --> G[Tambahkan gaji ke list]
    G --> D
    D -->|Tidak| H[Hitung sum dari list]
    H --> I[/Return total pengeluaran/]
    I --> J([End])
```

Kedua flowchart tersebut merupakan rancangan algoritma yang dapat
digambar ulang di Miro menggunakan simbol flowchart standar dan kemudian
diekspor sebagai gambar.

## 8. Struktur Notebook yang Disarankan

Ikuti outline dari instruksi tugas:

1.  **Perkenalan**
    -   Judul Live Code 1 -- Set 2.
    -   Nama: Nawafi Suren.
    -   Batch: HCK-47.
    -   Penjelasan singkat program Employee Payroll System.
2.  **Answer**
    -   Markdown judul Soal 1.
    -   Cell kode function `hitung_penghasilan`.
    -   Cell pengujian soal 1.
    -   Markdown judul Soal 2.
    -   Cell kode function `total_pengeluaran`.
    -   Cell pengujian soal 2.
    -   Markdown penjelasan bila diperlukan.
3.  **Flowchart**
    -   Sertakan gambar flowchart hasil ekspor dari Miro. Letakkan pada
        notebook atau sesuai ketentuan pengumpulan.

Setiap function yang dibuat wajib memiliki docstring yang menjelaskan
kegunaan, parameter, nilai yang dikembalikan, dan alur atau cara
kerjanya.

## 9. Checklist Sebelum Mengumpulkan

-   [ ] Deskripsi program sudah sesuai dengan Employee Payroll System.
-   [ ] Function `hitung_penghasilan` sudah dibuat.
-   [ ] Gaji pokok Manager, Staff, dan Kasir sudah benar.
-   [ ] Lembur hanya dihitung untuk jam kerja di atas 160 jam.
-   [ ] Function menggunakan `return`.
-   [ ] Jabatan tidak valid ditangani.
-   [ ] Function `total_pengeluaran` sudah dibuat.
-   [ ] Function kedua menggunakan `**kwargs`.
-   [ ] Looping `for` sudah digunakan.
-   [ ] List kosong dibuat untuk menampung gaji.
-   [ ] `.append()` digunakan untuk menambahkan gaji.
-   [ ] `sum()` digunakan untuk menghitung total pengeluaran.
-   [ ] Function kedua memanggil function pertama.
-   [ ] Kedua function memiliki docstring.
-   [ ] Contoh hasil pengujian sesuai soal.
-   [ ] Flowchart dibuat di Miro dan diekspor sebagai gambar.
-   [ ] Notebook memiliki bagian Perkenalan dan Answer.
-   [ ] Setiap soal memiliki judul Markdown dan cell kode terpisah.

## 10. Kesimpulan

Kode awal Nawafi sudah memiliki dasar conditional dan function, tetapi
belum memenuhi seluruh requirement Live Code 1. Kekeliruan utama adalah
perhitungan lembur yang mengalikan seluruh jam kerja, bukan hanya jam di
atas 160 jam.

Perbaikan utama adalah:

1.  Menghitung jam lembur dengan `jam_kerja - 160`.
2.  Menggunakan `return` agar hasil function dapat dipakai oleh function
    lain.
3.  Membuat `total_pengeluaran` dengan `**kwargs`, looping, list,
    `.append()`, dan `sum()`.
4.  Menambahkan docstring pada setiap function.
5.  Membuat flowchart di Miro.
6.  Menyesuaikan isi notebook dengan struktur dan deskripsi tugas yang
    benar.

Dengan perbaikan tersebut, program akan lebih sesuai dengan requirement
soal Employee Payroll System dan materi Python yang sedang dievaluasi.

------------------------------------------------------------------------

**Catatan integritas akademik:** Gunakan rangkuman ini sebagai bahan
belajar dan acuan. Pastikan memahami setiap bagian, menguji kode
sendiri, serta mengikuti ketentuan orisinalitas dan aturan pengumpulan
dari Hacktiv8.
