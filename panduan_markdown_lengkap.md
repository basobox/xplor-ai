# Panduan Sintaks Markdown dan Hasil Output

Dokumen ini berisi kumpulan sintaks Markdown lengkap beserta contoh hasil keseluruhannya setelah diformat. Anda dapat menyalin bagian sintaks di dalam kotak kode untuk digunakan pada dokumen Anda sendiri.

---

## 1. Heading (Judul)

### Sintaks
```markdown
# Judul Utama H1 (Heading 1)
## Sub-judul H2 (Heading 2)
### Sub-judul H3 (Heading 3)
```

### Hasil Output
# Judul Utama H1 (Heading 1)
## Sub-judul H2 (Heading 2)
### Sub-judul H3 (Heading 3)

---

## 2. Format Teks (Emphasis)

### Sintaks
```markdown
Teks ini ditulis dalam format **Tebal (Bold)**.
Teks ini ditulis dalam format *Miring (Italic)*.
Teks ini menggabungkan keduanya menjadi ***Tebal dan Miring***.
Teks ini dicoret menggunakan ~~Strikethrough~~.
```

### Hasil Output
Teks ini ditulis dalam format **Tebal (Bold)**.  
Teks ini ditulis dalam format *Miring (Italic)*.  
Teks ini menggabungkan keduanya menjadi ***Tebal dan Miring***.  
Teks ini dicoret menggunakan ~~Strikethrough~~.

---

## 3. List / Daftar (Unordered & Ordered)

### Sintaks
```markdown
* Item pertama
* Item kedua
  * Sub-item kedua titik satu

1. Langkah pertama
2. Langkah kedua
   1. Sub-langkah 2a
```

### Hasil Output
* Item pertama
* Item kedua
  * Sub-item kedua titik satu

1. Langkah pertama
2. Langkah kedua
   1. Sub-langkah 2a

---

## 4. Daftar Tugas (Task List / Checkbox)

### Sintaks
```markdown
- [x] Tugas yang sudah selesai
- [ ] Tugas yang belum selesai
```

### Hasil Output
- [x] Tugas yang sudah selesai
- [ ] Tugas yang belum selesai

---

## 5. Tautan (Link) & Gambar

### Sintaks
```markdown
Kunjungi situs resmi [Google](https://www.google.com)

![Logo Markdown](https://upload.wikimedia.org/wikipedia/commons/4/48/Markdown-mark.svg)
```

### Hasil Output
Kunjungi situs resmi [Google](https://www.google.com)

![Logo Markdown](https://upload.wikimedia.org/wikipedia/commons/4/48/Markdown-mark.svg)

---

## 6. Kutipan (Blockquote)

### Sintaks
```markdown
> "Simple is better than complex."
> — *The Zen of Python*
```

### Hasil Output
> "Simple is better than complex."
> — *The Zen of Python*

---

## 7. Kode (Inline & Code Block)

### Sintaks
```markdown
Ketik `npm install` pada terminal Anda.

```javascript
const salam = "Halo Dunia!";
console.log(salam);
\```
```

### Hasil Output
Ketik `npm install` pada terminal Anda.

```javascript
const salam = "Halo Dunia!";
console.log(salam);
```

---

## 8. Tabel (Table)

### Sintaks
```markdown
| Nama Produk | Kategori | Harga (IDR) |
| :--- | :---: | ---: |
| Laptop Asus | Elektronik | 12.500.000 |
| Tetikus Logi | Aksesoris | 350.000 |
```

### Hasil Output
| Nama Produk | Kategori | Harga (IDR) |
| :--- | :---: | ---: |
| Laptop Asus | Elektronik | 12.500.000 |
| Tetikus Logi | Aksesoris | 350.000 |

---

## 9. Catatan Kaki (Footnote)

### Sintaks
```markdown
Ini adalah sebuah pernyataan yang butuh penjelasan tambahan.[^1]

[^1]: Ini adalah isi teks catatan kaki di akhir dokumen.
```

### Hasil Output
Ini adalah sebuah pernyataan yang butuh penjelasan tambahan.[^1]

[^1]: Ini adalah isi teks catatan kaki di akhir dokumen.

---

## 10. Garis Pembatas & Escape Character

### Sintaks
```markdown
---
\* Teks ini diawali tanda bintang asli, bukan format list.
```

### Hasil Output
---
\* Teks ini diawali tanda bintang asli, bukan format list.
