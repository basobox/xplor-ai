# Dokumentasi Lengkap Instalasi Python dengan Miniconda di MiniOS Linux

Dokumen ini merangkum proses instalasi Miniconda, konfigurasi Conda, pembuatan environment Python 3.12, kendala yang muncul, solusi, serta tahapan lanjutan untuk menggunakan Python melalui VS Code.

> **Keterangan sumber:** Bagian proses dan status di bawah merujuk pada log terminal yang dibahas. Langkah yang belum dilakukan dalam log ditandai sebagai **langkah lanjutan**, bukan dianggap sudah berhasil.

---

## 1. Tujuan instalasi

Menyiapkan lingkungan Python yang terpisah dan mudah dikelola di MiniOS Linux dengan menggunakan Miniconda. Environment proyek yang dibuat bernama `phiTON` dan menggunakan Python 3.12.

## 2. Informasi sistem dan hasil akhir yang tercatat

| Komponen | Informasi |
|---|---|
| Sistem operasi | MiniOS Linux |
| Nama pengguna terminal | `live` |
| Direktori home | `/home/live` |
| Instalasi Miniconda | `/home/live/miniconda3` |
| Versi Conda | `26.9.4` |
| Versi Python di `base` | `3.14.9` |
| Nama environment proyek | `phiTON` |
| Versi Python di `phiTON` | `3.12.16` |
| Lokasi environment | `/home/live/miniconda3/envs/phiTON` |

Pada pemeriksaan awal, perintah `conda --version` belum dikenali oleh terminal. Ini menunjukkan Conda belum tersedia pada sesi shell saat itu.

---

## 3. Menjalankan installer Miniconda

Installer dijalankan dari terminal menggunakan:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

Installer yang dijalankan menampilkan identitas paket:

```text
Welcome to Miniconda3 py314_26.9.4-0
```

Lisensi installer diterima, kemudian proses meminta lokasi pemasangan Miniconda.

### 3.1 Kendala: lokasi instalasi `/home/` ditolak

Pada percobaan pertama, lokasi yang dimasukkan adalah:

```text
/home/
```

Installer menampilkan:

```text
ERROR: File or directory already exists: '/home/'
```

**Penyebab:** `/home/` merupakan direktori yang sudah ada. Installer membutuhkan direktori tujuan instalasi yang sesuai, bukan direktori induk tersebut.

**Solusi:** gunakan direktori khusus di dalam home pengguna. Pada percobaan selanjutnya, lokasi default berikut diterima:

```text
/home/live/miniconda3
```

Instalasi kemudian berjalan sampai selesai.

---

## 4. Inisialisasi Conda pada Bash

Setelah proses instalasi, installer menanyakan apakah Conda akan menginisialisasi shell. Jawaban yang diberikan adalah `yes`.

Installer memperbarui konfigurasi:

```text
/home/live/.bashrc
```

Tujuannya agar perintah `conda` dapat digunakan dari terminal Bash.

### 4.1 Kendala: `conda` masih tidak ditemukan

Setelah installer selesai, pemeriksaan berikut masih gagal:

```bash
conda --version
```

Pesan yang muncul:

```text
bash: conda: command not found
```

**Penyebab:** perubahan konfigurasi pada `.bashrc` belum dimuat ke sesi terminal yang sedang berjalan.

**Solusi:** jalankan:

```bash
source ~/.bashrc
```

Kemudian periksa kembali:

```bash
conda --version
```

Hasil yang tercatat:

```text
conda 26.9.4
```

Prompt terminal juga menunjukkan environment `(base)`. Ini menandakan Conda sudah dapat digunakan pada sesi tersebut.

---

## 5. Memahami environment `base`

Setelah Miniconda terpasang, Conda menyediakan environment bawaan bernama `base`.

Informasi yang tercatat:

- Conda: `26.9.4`
- Python pada `base`: `3.14.9`

Untuk proyek belajar atau pengembangan, lebih baik membuat environment terpisah. Dengan demikian, versi Python dan paket proyek tidak harus bercampur dengan environment `base`.

---

## 6. Membuat environment Python 3.12

Percobaan awal membuat environment bernama `belajar` dengan perintah:

```bash
conda create -n belajar python=3.12 -y
```

Namun, proses tersebut belum berhasil.

### 6.1 Kendala: Terms of Service Anaconda belum diterima

Conda menampilkan error:

```text
CondaToSNonInteractiveError
```

Pesan tersebut menunjukkan Terms of Service belum diterima untuk dua channel:

```text
https://repo.anaconda.com/pkgs/main
https://repo.anaconda.com/pkgs/r
```

Karena proses pembuatan environment berhenti, environment `belajar` belum terbentuk.

### 6.2 Kendala lanjutan: environment `belajar` tidak ditemukan

Setelah pembuatan environment gagal, perintah:

```bash
conda activate belajar
```

menghasilkan pesan:

```text
EnvironmentNameNotFound
```

**Penyebab:** environment `belajar` tidak pernah selesai dibuat. Jadi, error aktivasi tersebut merupakan akibat dari kegagalan proses pembuatan environment sebelumnya.

### 6.3 Solusi: menerima Terms of Service

Dua perintah berikut dijalankan untuk menerima Terms of Service dari channel yang diminta:

```bash
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main
```

```bash
conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r
```

Keduanya memberikan hasil bahwa persetujuan telah diterima.

> Persetujuan Terms of Service merupakan tindakan yang dilakukan pengguna pada konfigurasi Conda. Pastikan selalu membaca dan menyetujui ketentuan layanan yang berlaku sebelum menggunakannya.

---

## 7. Membuat environment `phiTON`

Setelah Terms of Service diterima, environment berhasil dibuat menggunakan nama `phiTON`:

```bash
conda create -n phiTON python=3.12 -y
```

Hasil proses instalasi menunjukkan:

| Item | Hasil |
|---|---|
| Nama environment | `phiTON` |
| Versi Python | `3.12.16` |
| Lokasi environment | `/home/live/miniconda3/envs/phiTON` |

**Penting:** nama environment yang berhasil dibuat dalam log adalah `phiTON`, bukan `belajar`.

### 7.1 Mengaktifkan environment

Jalankan:

```bash
conda activate phiTON
```

Setelah berhasil, prompt terminal menampilkan:

```text
(phiTON)
```

Tanda tersebut menunjukkan environment `phiTON` sedang aktif.

### 7.2 Memastikan versi Python

Jalankan:

```bash
python --version
```

Hasil yang tercatat:

```text
Python 3.12.16
```

Dengan demikian, pembuatan dan aktivasi environment `phiTON` dengan Python 3.12.16 telah terverifikasi berhasil.

---

## 8. Perintah penting untuk penggunaan sehari-hari

### 8.1 Memuat konfigurasi Bash

Jika perintah Conda belum dikenali setelah membuka terminal:

```bash
source ~/.bashrc
```

### 8.2 Memeriksa versi Conda

```bash
conda --version
```

### 8.3 Melihat daftar environment

```bash
conda env list
```

Alternatif:

```bash
conda info --envs
```

### 8.4 Mengaktifkan environment proyek

```bash
conda activate phiTON
```

### 8.5 Memeriksa versi Python

```bash
python --version
```

### 8.6 Memeriksa lokasi Python yang sedang digunakan

Jalankan perintah berikut secara terpisah:

```bash
which python
```

Untuk environment `phiTON`, lokasi yang diharapkan adalah:

```text
/home/live/miniconda3/envs/phiTON/bin/python
```

**Catatan:** log yang dibahas belum memperlihatkan hasil `which python`, sehingga jalur tersebut merupakan lokasi yang diharapkan berdasarkan lokasi environment, bukan hasil pemeriksaan yang sudah terkonfirmasi.

Untuk pemeriksaan yang lebih pasti dari dalam Python:

```bash
python -c "import sys; print(sys.executable)"
```

### 8.7 Keluar dari environment aktif

```bash
conda deactivate
```

---

## 9. Langkah lanjutan: memasang pustaka Python

Log instalasi belum mengonfirmasi bahwa pustaka ilmiah dan komputasi sudah dipasang. Jika ingin menggunakan NumPy, Pandas, Matplotlib, dan Jupyter, aktifkan dahulu environment:

```bash
conda activate phiTON
```

Kemudian pasang paket:

```bash
conda install numpy pandas matplotlib jupyter -y
```

Untuk memasang kernel agar environment dapat digunakan dari Jupyter Notebook:

```bash
pip install ipykernel
```

Jika ingin mendaftarkan environment sebagai kernel Jupyter dengan nama yang mudah dikenali:

```bash
python -m ipykernel install --user --name phiTON --display-name "Python (phiTON)"
```

Dua perintah terkait `ipykernel` di atas adalah panduan lanjutan. Keberhasilannya belum tercatat dalam log instalasi.

---

## 10. Langkah lanjutan: menghubungkan Python ke VS Code

Integrasi VS Code belum terkonfirmasi selesai. Berikut langkah yang dapat dilakukan setelah VS Code tersedia di sistem.

1. Buka VS Code.
2. Buka menu **Extensions**.
3. Instal ekstensi **Python** dari Microsoft.
4. Instal **Pylance** untuk dukungan analisis kode dan pelengkapan otomatis.
5. Buka Command Palette dengan `Ctrl + Shift + P`.
6. Pilih **Python: Select Interpreter**.
7. Pilih interpreter dari environment `phiTON`.

Jika interpreter tidak muncul dalam daftar, masukkan lokasi berikut secara manual:

```text
/home/live/miniconda3/envs/phiTON/bin/python
```

Pastikan terminal VS Code juga menggunakan environment yang benar. Jika perlu, buka terminal baru lalu jalankan:

```bash
conda activate phiTON
```

---

## 11. Menguji instalasi Python

Setelah paket dipasang dan interpreter VS Code dipilih, buat file bernama `test_python.py`.

Isi file:

```python
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

Jalankan file melalui terminal:

```bash
python test_python.py
```

Atau gunakan tombol **Run Python File** di VS Code.

Jika berhasil, program akan menampilkan informasi versi Python, lokasi interpreter, serta versi NumPy dan Pandas.

**Catatan:** pengujian script ini merupakan langkah verifikasi berikutnya dan belum tercatat dilakukan dalam log yang dirangkum. Jika muncul `ModuleNotFoundError`, kemungkinan paket yang disebut belum terpasang pada environment yang sedang aktif. Periksa environment dengan:

```bash
conda activate phiTON
```

Lalu pasang paket yang dibutuhkan di environment tersebut.

---

## 12. Troubleshooting ringkas

| Gejala / pesan | Kemungkinan penyebab berdasarkan proses | Tindakan |
|---|---|---|
| `conda: command not found` sebelum instalasi | Miniconda belum tersedia | Jalankan installer Miniconda |
| Installer menolak `/home/` | Lokasi tujuan merupakan direktori yang sudah ada | Gunakan `/home/live/miniconda3` |
| `conda: command not found` setelah instalasi | Konfigurasi shell belum dimuat ulang | Jalankan `source ~/.bashrc` |
| `CondaToSNonInteractiveError` | Terms of Service channel belum diterima | Jalankan perintah `conda tos accept` untuk channel `main` dan `r` |
| `EnvironmentNameNotFound` untuk `belajar` | Pembuatan environment `belajar` gagal sebelumnya | Buat environment setelah menyelesaikan kendala ToS; environment yang berhasil pada log adalah `phiTON` |
| Modul tidak ditemukan saat menjalankan script | Paket belum terpasang di environment aktif atau interpreter yang dipakai berbeda | Aktifkan `phiTON`, pasang paket, dan periksa `sys.executable` |
| Interpreter tidak muncul di VS Code | VS Code belum menemukan environment Conda | Pilih interpreter secara manual menggunakan jalur `phiTON` |

---

## 13. Catatan khusus tentang MiniOS dan penyimpanan

Direktori instalasi berada di:

```text
/home/live/miniconda3
```

Nama pengguna `live` dapat mengindikasikan bahwa sistem sedang berjalan dalam sesi live. Namun, log terminal saja tidak cukup untuk memastikan apakah perubahan akan bertahan setelah restart.

Jika MiniOS dijalankan tanpa konfigurasi persistence, file atau instalasi pada sesi live mungkin tidak bertahan setelah reboot. Karena itu:

- Periksa apakah MiniOS menggunakan persistence.
- Simpan proyek dan dokumen penting pada partisi atau media penyimpanan yang persisten.
- Jangan menganggap instalasi Miniconda akan selalu tersedia setelah restart sebelum persistence diuji.

---

## 14. Checklist status instalasi

| Tahap | Status berdasarkan log |
|---|---|
| Installer Miniconda dijalankan | Selesai |
| Miniconda dipasang di `/home/live/miniconda3` | Selesai |
| Inisialisasi shell melalui `.bashrc` | Selesai |
| `source ~/.bashrc` dijalankan | Selesai |
| `conda --version` menampilkan `26.9.4` | Terverifikasi |
| Terms of Service channel `main` diterima | Terverifikasi |
| Terms of Service channel `r` diterima | Terverifikasi |
| Environment `phiTON` dibuat | Selesai |
| Python `3.12.16` terpasang di `phiTON` | Terverifikasi |
| Environment `phiTON` diaktifkan | Terverifikasi |
| Hasil `which python` diperiksa | Belum terkonfirmasi |
| NumPy, Pandas, Matplotlib, Jupyter dipasang | Belum terkonfirmasi |
| `ipykernel` dipasang dan didaftarkan | Belum terkonfirmasi |
| Interpreter `phiTON` dipilih di VS Code | Belum terkonfirmasi |
| Script uji berhasil dijalankan dari VS Code | Belum terkonfirmasi |
| Persistence setelah reboot diuji | Belum terkonfirmasi |

---

## 15. Kesimpulan

Instalasi dasar Miniconda pada MiniOS Linux berhasil dilakukan. Miniconda terpasang pada `/home/live/miniconda3`, Conda versi `26.9.4` dapat dijalankan, dan environment proyek bernama `phiTON` berhasil dibuat dengan Python `3.12.16`.

Ada tiga kendala utama yang diselesaikan selama instalasi:

1. **Lokasi instalasi tidak sesuai:** `/home/` ditolak, lalu diganti dengan `/home/live/miniconda3`.
2. **Conda belum dikenali setelah instalasi:** konfigurasi shell dimuat ulang menggunakan `source ~/.bashrc`.
3. **Terms of Service belum diterima:** persetujuan diberikan untuk channel Anaconda `main` dan `r`, sehingga pembuatan environment dapat dilanjutkan.

Environment `belajar` tidak berhasil dibuat. Environment yang benar-benar berhasil dibuat dan diaktifkan adalah `phiTON`.

Tahap dasar instalasi Python sudah berhasil. Agar siap digunakan untuk belajar dan mengembangkan proyek, tahap berikutnya adalah memasang pustaka yang diperlukan, menghubungkan interpreter `phiTON` ke VS Code, dan menjalankan script uji. Persistence MiniOS juga perlu diperiksa agar instalasi dan proyek tetap tersedia setelah restart.
