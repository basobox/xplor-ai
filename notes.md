





# Judul Utama H1 (Heading 1)

## Sub-judul H2 (Heading 2)

### Sub-judul H3 (Heading 3)

#### Heading H4 sampai H6
##### Heading H5
###### Heading H6

---

## 1. Format Teks (Emphasis)

Teks ini ditulis dalam format **Tebal (Bold)** atau __Tebal__.
Teks ini ditulis dalam format *Miring (Italic)* atau _Miring_.
Teks ini menggabungkan keduanya menjadi ***Tebal dan Miring***.
Teks ini dicoret menggunakan ~~Strikethrough~~.

---

## 2. List / Daftar

### Daftar Tanpa Nomor (Unordered List)
* Item pertama
* Item kedua
  * Sub-item kedua titik satu
  * Sub-item kedua titik dua
- Pilihan simbol lain (menggunakan tanda minus)
+ Pilihan simbol lain (menggunakan tanda plus)

### Daftar Bernomor (Ordered List)
1. Langkah pertama
2. Langkah kedua
3. Langkah ketiga
   1. Sub-langkah 3a (Gunakan indentasi 3 spasi)
   2. Sub-langkah 3b

### Daftar Tugas (Task List / Checkbox)
- [x] Tugas yang sudah selesai
- [ ] Tugas yang belum selesai
- [ ] Tugas penting lainnya

---

## 3. Membuat Tautan (Link) & Gambar

### Link / Tautan
* Kunjungi situs resmi [Google](https://www.google.com)
* Link otomatis: <https://wikipedia.org>

### Gambar
Format gambar mirip dengan link, namun diawali tanda seru `!`.

![Logo Markdown](https://wikimedia.org "Ini adalah deskripsi gambar")

---

## 4. Kutipan (Blockquote)

> "Simple is better than complex."
> — *The Zen of Python*

> Ini adalah kutipan bertingkat.
>> Ini adalah bagian dalam kutipan bertingkat.

---

## 5. Blok Kode (Code Block)

### Kode dalam Baris (Inline Code)
Untuk menjalankan perintah instalasi, ketik `npm install` pada terminal Anda.

### Blok Kode Multi-Baris (Syntax Highlighting)
Gunakan tiga buah *backtick* (```) diikuti dengan nama bahasa pemrogramannya.

```javascript
// Contoh kode JavaScript
const salam = "Halo Dunia!";
function tampilkanSalam(nama) {
    console.log(`${salam}, nama saya ${nama}.`);
}
tampilkanSalam("Budi");
```

```html
<!-- Contoh kode HTML -->
<div class="kontainer">
    <h1>Selamat Datang</h1>
    <p>Ini adalah paragraf.</p>
</div>
```

---

## 6. Tabel (Table)

Kolom dapat diatur rata kiri, rata tengah, atau rata kanan menggunakan tanda titik dua (`:`).

| Nama Produk | Kategori | Harga (IDR) | Status |
| :--- | :---: | ---: | :---: |
| Laptop Asus | Elektronik | 12.500.000 | [x] Ready |
| Tetikus Logi | Aksesoris | 350.000 | [ ] Kosong |
| Meja Kerja | Furnitur | 1.200.000 | [x] Ready |

---

## 7. Catatan Kaki (Footnote)

Ini adalah sebuah pernyataan yang membutuhkan penjelasan tambahan di bagian bawah halaman.[^1]

[^1]: Ini adalah isi teks catatan kaki yang diletakkan di akhir dokumen.

---

## 8. Mengabaikan Format (Escaping Characters)

Jika Anda ingin menampilkan karakter khusus Markdown (seperti `*` atau `#`) apa adanya tanpa mengubahnya menjadi format teks, gunakan tanda *backslash* (`\`) sebelum karakter tersebut.

\* Teks ini diawali tanda bintang asli, bukan format list.
\# Ini bukan heading, melainkan tanda pagar biasa.
