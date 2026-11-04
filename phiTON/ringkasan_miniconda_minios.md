# Ringkasan Instalasi Miniconda, Python, dan Persiapan VS Code di MiniOS

## 1. Tujuan

Menyiapkan lingkungan Python di MiniOS Linux menggunakan Miniconda,
membuat environment khusus dengan Python 3.12, lalu menghubungkannya ke
VS Code untuk belajar atau mengembangkan proyek Python.

## 2. Kondisi awal

-   Sistem yang digunakan: MiniOS Linux.

-   Nama pengguna pada terminal: `live`.

-   Direktori home: `/home/live`.

-   Saat pertama kali diperiksa, perintah `conda --version`
    menghasilkan:

    ``` text
    bash: conda: command not found
    ```

    Artinya Conda belum tersedia atau belum terdaftar di `PATH`.

> **Catatan:** Direktori `/home/live` mengindikasikan sesi pengguna
> bernama `live`. Jika MiniOS dijalankan sebagai live system,
> keberlanjutan file setelah reboot bergantung pada konfigurasi
> persistence. Pastikan proyek dan data penting disimpan di lokasi yang
> persisten.

## 3. Instalasi Miniconda

Installer dijalankan dengan perintah:

``` bash
bash Miniconda3-latest-Linux-x86_64.sh
```

Installer yang digunakan mengidentifikasi paket sebagai
`Miniconda3 py314_26.9.4-0`. Lisensi diterima dan proses instalasi
dilanjutkan.

### Kendala: memilih `/home/` sebagai lokasi instalasi

Percobaan pertama menggunakan lokasi:

``` text
/home/
```

Installer menolak lokasi tersebut dan menampilkan:

``` text
ERROR: File or directory already exists: '/home/'
```

**Penyebab:** `/home/` sudah merupakan direktori yang ada, bukan
direktori instalasi khusus yang kosong.

**Solusi:** gunakan lokasi instalasi khusus di dalam direktori pengguna.
Pada percobaan berikutnya, lokasi default berikut diterima:

``` text
/home/live/miniconda3
```

Instalasi Miniconda selesai. Versi Conda yang terpasang adalah `26.9.4`,
dan Python pada environment `base` adalah `3.14.9`.

## 4. Inisialisasi Conda pada shell

Installer menanyakan apakah Conda perlu menginisialisasi shell. Jawaban
yang diberikan adalah `yes`. Installer kemudian mengubah:

``` text
/home/live/.bashrc
```

### Kendala: `conda` masih tidak dikenali setelah instalasi

Sesaat setelah installer selesai, perintah:

``` bash
conda --version
```

masih menghasilkan pesan bahwa `conda` tidak ditemukan.

**Penyebab:** konfigurasi shell yang baru ditambahkan ke `.bashrc` belum
dimuat ke sesi terminal yang sedang berjalan.

**Solusi:** muat ulang konfigurasi shell:

``` bash
source ~/.bashrc
```

Setelah itu, pemeriksaan:

``` bash
conda --version
```

berhasil dan menampilkan:

``` text
conda 26.9.4
```

Prompt terminal juga menunjukkan environment `(base)`.

## 5. Kendala Terms of Service saat membuat environment

Percobaan pertama membuat environment bernama `belajar`:

``` bash
conda create -n belajar python=3.12 -y
```

Proses gagal dengan pesan:

``` text
CondaToSNonInteractiveError
```

Pesan tersebut menyatakan bahwa Terms of Service belum diterima untuk
channel Anaconda berikut:

-   `https://repo.anaconda.com/pkgs/main`
-   `https://repo.anaconda.com/pkgs/r`

Karena proses pembuatan environment gagal, perintah:

``` bash
conda activate belajar
```

kemudian menghasilkan pesan `EnvironmentNameNotFound`.

**Penyebab:** environment `belajar` tidak berhasil dibuat karena proses
sebelumnya berhenti akibat persetujuan Terms of Service yang belum
diberikan. Jadi, pesan `EnvironmentNameNotFound` merupakan akibat dari
kegagalan pembuatan environment, bukan masalah aktivasi yang berdiri
sendiri.

**Solusi:** terima Terms of Service untuk kedua channel:

``` bash
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main

conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
```

Kedua perintah dilaporkan berhasil.

## 6. Membuat environment Python 3.12

Setelah Terms of Service diterima, environment yang berhasil dibuat
bernama **`phiTON`** (bukan `belajar`).

Perintah:

``` bash
conda create -n phiTON python=3.12 -y
```

Hasil instalasi menunjukkan:

-   Nama environment: `phiTON`
-   Versi Python: `3.12.16`
-   Lokasi environment:

``` text
/home/live/miniconda3/envs/phiTON
```

Environment kemudian diaktifkan:

``` bash
conda activate phiTON
```

Prompt terminal berubah menjadi:

``` text
(phiTON)
```

Pemeriksaan versi:

``` bash
python --version
```

menghasilkan:

``` text
Python 3.12.16
```

**Status:** pembuatan dan aktivasi environment `phiTON` dengan Python
3.12.16 telah berhasil.

## 7. Ringkasan masalah dan solusi

  ---------------------------------------------------------------------------------
  Masalah                         Penyebab                Solusi
  ------------------------------- ----------------------- -------------------------
  `conda: command not found` pada Conda belum terpasang   Instal Miniconda
  awal proses                     atau belum masuk `PATH` 

  Installer menolak `/home/`      Direktori tersebut      Gunakan
                                  sudah ada dan bukan     `/home/live/miniconda3`
                                  direktori instalasi     
                                  khusus yang kosong      

  `conda` masih tidak dikenali    `.bashrc` belum dimuat  Jalankan
  setelah instalasi               ulang                   `source ~/.bashrc`

  `CondaToSNonInteractiveError`   Terms of Service        Jalankan dua perintah
                                  channel Anaconda belum  `conda tos accept`
                                  diterima                

  `EnvironmentNameNotFound` untuk Pembuatan environment   Buat environment setelah
  `belajar`                       `belajar` sebelumnya    ToS diterima; environment
                                  gagal                   yang berhasil dibuat
                                                          adalah `phiTON`
  ---------------------------------------------------------------------------------

## 8. Perintah penting untuk penggunaan sehari-hari

### Memuat konfigurasi shell

``` bash
source ~/.bashrc
```

### Memeriksa versi Conda

``` bash
conda --version
```

### Melihat daftar environment

``` bash
conda env list
```

atau:

``` bash
conda info --envs
```

### Mengaktifkan environment proyek

``` bash
conda activate phiTON
```

### Memeriksa versi Python

``` bash
python --version
```

### Memeriksa lokasi executable Python

Jalankan sebagai perintah terpisah:

``` bash
which python
```

Lokasi yang diharapkan untuk environment ini adalah:

``` text
/home/live/miniconda3/envs/phiTON/bin/python
```

Lokasi tersebut merupakan jalur yang diharapkan berdasarkan lokasi
environment; hasil `which python` belum tercatat dalam log.

### Keluar dari environment aktif

``` bash
conda deactivate
```

## 9. Langkah berikutnya: memasang pustaka Python

Log yang diringkas belum menunjukkan bahwa pustaka berikut sudah
dipasang. Saat environment `phiTON` aktif, pustaka dapat dipasang
dengan:

``` bash
conda install numpy pandas matplotlib jupyter -y
```

Untuk mendaftarkan environment sebagai kernel Jupyter:

``` bash
pip install ipykernel
```

Perintah di atas adalah langkah lanjutan yang disarankan, bukan
instalasi yang sudah terkonfirmasi berhasil dalam log.

## 10. Langkah berikutnya: menghubungkan Python ke VS Code

1.  Buka VS Code.
2.  Buka bagian Extensions.
3.  Pasang ekstensi **Python** dari Microsoft.
4.  Pasang **Pylance** (opsional, tetapi berguna untuk bantuan kode dan
    analisis tipe).
5.  Buka Command Palette dengan `Ctrl + Shift + P`.
6.  Pilih **Python: Select Interpreter**.
7.  Pilih interpreter milik environment `phiTON`.

Jika interpreter tidak muncul dalam daftar, masukkan jalurnya secara
manual:

``` text
/home/live/miniconda3/envs/phiTON/bin/python
```

**Status:** integrasi VS Code belum dikonfirmasi dalam log.
Langkah-langkah ini merupakan panduan untuk tahap berikutnya.

## 11. Menguji Python dan pustaka

Setelah pustaka terpasang dan interpreter VS Code dipilih, buat file
bernama `test_python.py` dengan isi:

``` python
import sys
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

print("Python berhasil dijalankan!")
print("Versi Python:", sys.version)
print("Lokasi Python:", sys.executable)
print("NumPy:", np.__version__)
print("Pandas:", pd.__version__)
```

Jalankan file tersebut dari terminal VS Code atau tombol Run Python
File.

Jika berhasil, program akan menampilkan versi Python, lokasi
interpreter, serta versi NumPy dan Pandas. Pengujian ini belum tercatat
dilakukan dalam log.

## 12. Status akhir berdasarkan log

  -----------------------------------------------------------------------
  Komponen / tahap                    Status
  ----------------------------------- -----------------------------------
  Installer Miniconda dijalankan      Selesai

  Miniconda terpasang di              Selesai
  `/home/live/miniconda3`             

  Inisialisasi `.bashrc`              Selesai

  `conda --version` menampilkan       Terverifikasi
  `26.9.4`                            

  Terms of Service channel `main` dan Selesai
  `r` diterima                        

  Environment `phiTON` dibuat         Selesai

  Python `3.12.16` terpasang di       Terverifikasi
  `phiTON`                            

  Environment `phiTON` berhasil       Terverifikasi
  diaktifkan                          

  Lokasi interpreter diperiksa dengan Belum terverifikasi
  `which python`                      

  NumPy, Pandas, Matplotlib, dan      Belum terverifikasi
  Jupyter dipasang                    

  `ipykernel` dipasang                Belum terverifikasi

  Interpreter `phiTON` dipilih di VS  Belum terverifikasi
  Code                                

  Script Python berhasil dijalankan   Belum terverifikasi
  dari VS Code                        
  -----------------------------------------------------------------------

## 13. Kesimpulan

Instalasi Miniconda pada MiniOS berhasil dilakukan di
`/home/live/miniconda3`. Setelah konfigurasi shell dimuat ulang dan
Terms of Service channel Anaconda diterima, environment bernama `phiTON`
berhasil dibuat dan diaktifkan dengan Python `3.12.16`.

Kendala yang muncul selama proses telah memiliki penyelesaian: lokasi
instalasi diganti dari `/home/` ke direktori khusus pengguna, `.bashrc`
dimuat ulang dengan `source ~/.bashrc`, dan Terms of Service diterima
untuk channel `main` serta `r`.

Tahap yang masih perlu dikerjakan adalah memasang pustaka Python,
memasang `ipykernel` bila diperlukan, memilih interpreter `phiTON` di VS
Code, lalu menjalankan script uji. Jika MiniOS digunakan sebagai live
system, periksa juga pengaturan persistence agar instalasi dan file
proyek tidak hilang setelah restart.
