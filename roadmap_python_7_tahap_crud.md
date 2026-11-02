# Panduan Praktik Python: 7 Tahap CRUD dari Fundamental sampai Mini ERP

## Tujuan

Panduan ini menggunakan pendekatan **Application-Based Learning**:
belajar Python dengan membangun aplikasi, lalu mempelajari teori yang
digunakan dalam konteks nyata.

Kita mengembangkan satu proyek secara bertahap:

**Inventory Management → Database CRUD → Mini ERP**

Setiap tahap adalah program mandiri. Jalankan dan pahami satu tahap
sebelum melanjutkan ke tahap berikutnya.

## Roadmap 7 Tahap

    Tahap Materi                   Target
  ------- ------------------------ ----------------------------------------------
        1 Procedural CRUD          Memahami logika Create, Read, Update, Delete
        2 Function CRUD            Memahami modularisasi dan pemisahan fungsi
        3 Validation & Exception   Menangani kesalahan input
        4 File/JSON CRUD           Menyimpan data secara permanen
        5 OOP CRUD                 Memahami class dan object
        6 Database CRUD            Memahami SQL dan SQLite
        7 Mini ERP                 Mengintegrasikan beberapa fitur bisnis

## Cara Belajar

Gunakan siklus berikut pada setiap tahap:

1.  **Pahami** --- pelajari tujuan fitur dan alur program.
2.  **Lengkapi** --- selesaikan kode yang belum lengkap.
3.  **Tulis ulang** --- bangun kembali tanpa melihat contoh.
4.  **Modifikasi** --- tambahkan kebutuhan atau aturan baru.
5.  **Terapkan** --- gunakan konsep yang sama pada aplikasi lain.

Jangan hanya mengukur keberhasilan dari kode yang bisa dijalankan.
Targetnya adalah mampu menjelaskan logika dan menulis ulang program
sendiri.

------------------------------------------------------------------------

# Tahap 1 --- Procedural CRUD

**Target:** Memahami bagaimana data disimpan, ditampilkan, diubah, dan
dihapus menggunakan Python dasar.

### Konsep yang dipelajari

-   Variable
-   List
-   Input
-   Conditional (`if`, `elif`, `else`)
-   `while` loop
-   `for` loop
-   Indexing
-   `append()` dan `pop()`

### Kode lengkap

``` python
data = []

while True:
    print("\\n=== INVENTORY MANAGEMENT ===")
    print("1. Create")
    print("2. Read")
    print("3. Update")
    print("4. Delete")
    print("0. Exit")

    pilihan = input("Pilih menu: ")

    # CREATE
    if pilihan == "1":
        nama = input("Nama barang: ")
        kategori = input("Kategori: ")
        harga = int(input("Harga: "))

        data.append([nama, kategori, harga])
        print("Data berhasil ditambahkan")

    # READ
    elif pilihan == "2":
        if not data:
            print("Data belum ada")
        else:
            for i, barang in enumerate(data, start=1):
                print(i, barang)

    # UPDATE
    elif pilihan == "3":
        for i, barang in enumerate(data, start=1):
            print(i, barang)

        nomor = int(input("Nomor barang: "))

        if 1 <= nomor <= len(data):
            nama = input("Nama baru: ")
            kategori = input("Kategori baru: ")
            harga = int(input("Harga baru: "))

            data[nomor - 1] = [nama, kategori, harga]
            print("Data berhasil diubah")
        else:
            print("Nomor tidak ditemukan")

    # DELETE
    elif pilihan == "4":
        for i, barang in enumerate(data, start=1):
            print(i, barang)

        nomor = int(input("Nomor barang: "))

        if 1 <= nomor <= len(data):
            data.pop(nomor - 1)
            print("Data berhasil dihapus")
        else:
            print("Nomor tidak ditemukan")

    elif pilihan == "0":
        print("Program selesai")
        break

    else:
        print("Pilihan tidak tersedia")
```

### Penjelasan

**CREATE**

``` python
data.append([nama, kategori, harga])
```

Menambahkan produk baru ke dalam list `data`. Setiap produk disimpan
sebagai list berisi nama, kategori, dan harga.

**READ**

``` python
for i, barang in enumerate(data, start=1):
    print(i, barang)
```

Membaca seluruh data. `enumerate()` memberikan nomor urut yang dimulai
dari 1.

**UPDATE**

``` python
data[nomor - 1] = [nama, kategori, harga]
```

Mengganti isi data pada index tertentu. Nomor yang dimasukkan pengguna
dikurangi satu karena index Python dimulai dari 0.

**DELETE**

``` python
data.pop(nomor - 1)
```

Menghapus produk berdasarkan index.

**Latihan:** Tambahkan satu produk, tampilkan, ubah harganya, lalu hapus
produk tersebut.

------------------------------------------------------------------------

# Tahap 2 --- Function CRUD

**Target:** Memisahkan setiap fitur menjadi function agar program lebih
terstruktur dan mudah dirawat.

### Konsep baru

-   `def`
-   Parameter
-   Argument
-   `return`
-   Function call
-   Pemisahan tanggung jawab

### Kode lengkap

``` python
data = []

def create(data):
    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = int(input("Harga: "))

    data.append([nama, kategori, harga])
    print("Data berhasil ditambahkan")


def read(data):
    if not data:
        print("Data belum ada")
        return

    for i, barang in enumerate(data, start=1):
        print(i, barang)


def update(data):
    read(data)

    if not data:
        return

    nomor = int(input("Nomor barang: "))

    if 1 <= nomor <= len(data):
        nama = input("Nama baru: ")
        kategori = input("Kategori baru: ")
        harga = int(input("Harga baru: "))

        data[nomor - 1] = [nama, kategori, harga]
        print("Data berhasil diubah")
    else:
        print("Nomor tidak ditemukan")


def delete(data):
    read(data)

    if not data:
        return

    nomor = int(input("Nomor barang: "))

    if 1 <= nomor <= len(data):
        data.pop(nomor - 1)
        print("Data berhasil dihapus")
    else:
        print("Nomor tidak ditemukan")


def main():
    while True:
        print("\\n=== INVENTORY MANAGEMENT ===")
        print("1. Create")
        print("2. Read")
        print("3. Update")
        print("4. Delete")
        print("0. Exit")

        pilihan = input("Pilih menu: ")

        if pilihan == "1":
            create(data)
        elif pilihan == "2":
            read(data)
        elif pilihan == "3":
            update(data)
        elif pilihan == "4":
            delete(data)
        elif pilihan == "0":
            break
        else:
            print("Pilihan tidak tersedia")


main()
```

### Penjelasan

Parameter adalah nama variabel yang didefinisikan pada function:

``` python
def create(data):
```

Argument adalah nilai yang dikirim ketika function dipanggil:

``` python
create(data)
```

Semua function menerima list `data` yang sama. Dengan demikian, setiap
fitur dapat membaca atau mengubah penyimpanan yang sama.

Struktur program:

``` text
main()
 ├── create(data)
 ├── read(data)
 ├── update(data)
 └── delete(data)
```

**Perbedaan tahap 1 dan 2:** Tahap 1 berfokus pada logika CRUD. Tahap 2
mengajarkan cara mengorganisasi logika tersebut.

------------------------------------------------------------------------

# Tahap 3 --- Validation & Exception

**Target:** Membuat program lebih tangguh ketika pengguna memasukkan
data yang tidak sesuai.

### Contoh masalah

-   Harga dimasukkan dalam bentuk huruf.
-   Nomor produk tidak tersedia.
-   Pengguna mencoba mengubah atau menghapus data yang belum ada.
-   Harga bernilai nol atau negatif.

### Konsep baru

-   `try`
-   `except`
-   `ValueError`
-   Validasi kondisi
-   `continue`
-   `return`

### Kode lengkap

``` python
data = []

def input_harga():
    while True:
        try:
            harga = int(input("Harga: "))

            if harga <= 0:
                print("Harga harus lebih dari 0")
                continue

            return harga

        except ValueError:
            print("Masukkan angka yang valid")


def input_nomor(data):
    while True:
        try:
            nomor = int(input("Nomor barang: "))

            if 1 <= nomor <= len(data):
                return nomor - 1

            print("Nomor tidak ditemukan")

        except ValueError:
            print("Nomor harus berupa angka")


def create():
    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = input_harga()

    data.append([nama, kategori, harga])
    print("Data berhasil ditambahkan")


def read():
    if not data:
        print("Data belum ada")
        return

    for i, barang in enumerate(data, start=1):
        print(i, barang)


def update():
    read()

    if not data:
        return

    index = input_nomor(data)

    data[index][0] = input("Nama baru: ")
    data[index][1] = input("Kategori baru: ")
    data[index][2] = input_harga()

    print("Data berhasil diubah")


def delete():
    read()

    if not data:
        return

    index = input_nomor(data)

    data.pop(index)
    print("Data berhasil dihapus")


while True:
    print("\\n=== INVENTORY ===")
    print("1. Create")
    print("2. Read")
    print("3. Update")
    print("4. Delete")
    print("0. Exit")

    pilihan = input("Pilih: ")

    if pilihan == "1":
        create()
    elif pilihan == "2":
        read()
    elif pilihan == "3":
        update()
    elif pilihan == "4":
        delete()
    elif pilihan == "0":
        break
    else:
        print("Menu tidak tersedia")
```

### Penjelasan

**Exception handling**

``` python
try:
    harga = int(input("Harga: "))
except ValueError:
    print("Masukkan angka yang valid")
```

Program mencoba mengubah input menjadi integer. Jika pengguna memasukkan
teks yang tidak bisa dikonversi, Python menghasilkan `ValueError`. Blok
`except` menangani kesalahan itu agar program tidak langsung berhenti.

**Validation**

``` python
if harga <= 0:
    print("Harga harus lebih dari 0")
    continue
```

Ini bukan exception. Ini pemeriksaan aturan bisnis. `continue` membuat
perulangan kembali ke awal sehingga pengguna dapat mencoba lagi.

**Perbedaan penting:** Exception menangani kesalahan saat proses
berjalan, sedangkan validation memeriksa apakah nilai input memenuhi
aturan program.

------------------------------------------------------------------------

# Tahap 4 --- File/JSON CRUD

**Target:** Menyimpan data secara permanen sehingga data tetap tersedia
setelah program ditutup.

Pada tahap sebelumnya, data berada di memori:

``` python
data = []
```

Ketika program ditutup, isi list hilang. Pada tahap ini kita menyimpan
data ke file JSON.

### Konsep baru

-   Module
-   `import json`
-   `import os`
-   `open()`
-   `json.load()`
-   `json.dump()`
-   File handling

### Kode lengkap

``` python
import json
import os

FILE = "inventory.json"


def load_data():
    if not os.path.exists(FILE):
        return []

    with open(FILE, "r") as file:
        return json.load(file)


def save_data(data):
    with open(FILE, "w") as file:
        json.dump(data, file, indent=4)


def create(data):
    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = int(input("Harga: "))

    barang = {
        "nama": nama,
        "kategori": kategori,
        "harga": harga
    }

    data.append(barang)
    save_data(data)
    print("Data berhasil disimpan")


def read(data):
    if not data:
        print("Data belum ada")
        return

    for i, barang in enumerate(data, start=1):
        print(i, barang)


def update(data):
    read(data)

    if not data:
        return

    nomor = int(input("Nomor barang: "))

    if 1 <= nomor <= len(data):
        barang = data[nomor - 1]

        barang["nama"] = input("Nama baru: ")
        barang["kategori"] = input("Kategori baru: ")
        barang["harga"] = int(input("Harga baru: "))

        save_data(data)
        print("Data berhasil diperbarui")
    else:
        print("Nomor tidak ditemukan")


def delete(data):
    read(data)

    if not data:
        return

    nomor = int(input("Nomor barang: "))

    if 1 <= nomor <= len(data):
        data.pop(nomor - 1)
        save_data(data)
        print("Data berhasil dihapus")
    else:
        print("Nomor tidak ditemukan")


def main():
    data = load_data()

    while True:
        print("\\n=== INVENTORY JSON ===")
        print("1. Create")
        print("2. Read")
        print("3. Update")
        print("4. Delete")
        print("0. Exit")

        pilihan = input("Pilih: ")

        if pilihan == "1":
            create(data)
        elif pilihan == "2":
            read(data)
        elif pilihan == "3":
            update(data)
        elif pilihan == "4":
            delete(data)
        elif pilihan == "0":
            break
        else:
            print("Menu tidak tersedia")


main()
```

### Penjelasan

**Membaca file**

``` python
data = load_data()
```

Fungsi `load_data()` membaca file JSON dan mengembalikan datanya menjadi
struktur Python.

**Menyimpan file**

``` python
json.dump(data, file, indent=4)
```

`json.dump()` mengubah data Python menjadi JSON dan menuliskannya ke
file.

Contoh isi `inventory.json`:

``` json
[
    {
        "nama": "Sunlight",
        "kategori": "Sabun",
        "harga": 5000
    }
]
```

  Sebelum                       Sesudah
  ----------------------------- -----------------
  Data di RAM                   Data di file
  Hilang saat program ditutup   Tetap tersimpan
  Menggunakan list              List dan JSON

Catatan: jalankan program dari direktori yang sama agar file JSON mudah
ditemukan. Contoh ini belum menangani file JSON yang rusak atau input
harga yang tidak valid; konsep validasi dari Tahap 3 dapat digabungkan
kemudian.

------------------------------------------------------------------------

# Tahap 5 --- OOP CRUD

**Target:** Memahami bagaimana data dan perilaku digabungkan dalam
Class.

### Konsep baru

-   Class
-   Object
-   `__init__`
-   `self`
-   Attribute
-   Method

### Kode lengkap

``` python
class Inventory:

    def __init__(self):
        self.data = []

    def create(self):
        nama = input("Nama barang: ")
        kategori = input("Kategori: ")
        harga = int(input("Harga: "))

        barang = {
            "nama": nama,
            "kategori": kategori,
            "harga": harga
        }

        self.data.append(barang)
        print("Data berhasil ditambahkan")

    def read(self):
        if not self.data:
            print("Data belum ada")
            return

        for i, barang in enumerate(self.data, start=1):
            print(i, barang)

    def update(self):
        self.read()

        if not self.data:
            return

        nomor = int(input("Nomor barang: "))

        if 1 <= nomor <= len(self.data):
            barang = self.data[nomor - 1]

            barang["nama"] = input("Nama baru: ")
            barang["kategori"] = input("Kategori baru: ")
            barang["harga"] = int(input("Harga baru: "))

            print("Data berhasil diubah")
        else:
            print("Nomor tidak ditemukan")

    def delete(self):
        self.read()

        if not self.data:
            return

        nomor = int(input("Nomor barang: "))

        if 1 <= nomor <= len(self.data):
            self.data.pop(nomor - 1)
            print("Data berhasil dihapus")
        else:
            print("Nomor tidak ditemukan")


# Membuat object
app = Inventory()


while True:
    print("\\n=== INVENTORY OOP ===")
    print("1. Create")
    print("2. Read")
    print("3. Update")
    print("4. Delete")
    print("0. Exit")

    pilihan = input("Pilih: ")

    if pilihan == "1":
        app.create()
    elif pilihan == "2":
        app.read()
    elif pilihan == "3":
        app.update()
    elif pilihan == "4":
        app.delete()
    elif pilihan == "0":
        break
    else:
        print("Menu tidak tersedia")
```

### Penjelasan OOP dari dasar

**1. Class**

``` python
class Inventory:
```

Class adalah cetak biru untuk membuat object.

**2. Constructor**

``` python
def __init__(self):
    self.data = []
```

`__init__` dijalankan ketika object dibuat. Di sini, object memperoleh
attribute `data` yang berisi list kosong.

**3. Self**

``` python
self.data = []
```

`self` mengacu pada object yang sedang bekerja. `self.data` berarti
attribute `data` milik object tersebut.

**4. Method**

``` python
def create(self):
```

Method adalah function yang didefinisikan di dalam class.

**5. Object**

``` python
app = Inventory()
```

Membuat object bernama `app` dari class `Inventory`.

Kemudian:

``` python
app.create()
```

Memanggil method `create()` milik object tersebut.

  -----------------------------------------------------------------------
  Procedural                          OOP
  ----------------------------------- -----------------------------------
  Function                            Method

  Data terpisah                       Data menjadi attribute object

  `create(data)`                      `app.create()`

  Program berpusat pada fungsi        Program mengorganisasi data dan
                                      perilaku dalam object
  -----------------------------------------------------------------------

**Catatan:** OOP pada contoh ini masih menggunakan penyimpanan RAM. JSON
dan OOP belum digabungkan agar konsepnya dapat dipelajari secara
terpisah.

------------------------------------------------------------------------

# Tahap 6 --- Database CRUD dengan SQLite

**Target:** Memahami bagaimana aplikasi Python berkomunikasi dengan
database.

Pada tahap JSON, data disimpan sebagai file. Sekarang data disimpan
dalam database SQLite.

### Konsep baru

-   Database
-   Table
-   Primary Key
-   SQL
-   `INSERT`
-   `SELECT`
-   `UPDATE`
-   `DELETE`
-   `sqlite3`

### Kode lengkap

``` python
import sqlite3

# Koneksi database
conn = sqlite3.connect("inventory.db")
cursor = conn.cursor()

# Membuat tabel
cursor.execute("""
CREATE TABLE IF NOT EXISTS inventory (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nama TEXT NOT NULL,
    kategori TEXT NOT NULL,
    harga INTEGER NOT NULL
)
""")

conn.commit()


# CREATE
def create():
    nama = input("Nama barang: ")
    kategori = input("Kategori: ")
    harga = int(input("Harga: "))

    cursor.execute("""
    INSERT INTO inventory (nama, kategori, harga)
    VALUES (?, ?, ?)
    """, (nama, kategori, harga))

    conn.commit()
    print("Data berhasil ditambahkan")


# READ
def read():
    cursor.execute("SELECT * FROM inventory")
    data = cursor.fetchall()

    if not data:
        print("Data belum ada")
        return

    for barang in data:
        print(barang)


# UPDATE
def update():
    read()

    nomor = int(input("ID barang: "))
    nama = input("Nama baru: ")
    kategori = input("Kategori baru: ")
    harga = int(input("Harga baru: "))

    cursor.execute("""
    UPDATE inventory
    SET nama = ?, kategori = ?, harga = ?
    WHERE id = ?
    """, (nama, kategori, harga, nomor))

    conn.commit()

    if cursor.rowcount > 0:
        print("Data berhasil diubah")
    else:
        print("ID tidak ditemukan")


# DELETE
def delete():
    read()

    nomor = int(input("ID barang: "))

    cursor.execute(
        "DELETE FROM inventory WHERE id = ?",
        (nomor,)
    )

    conn.commit()

    if cursor.rowcount > 0:
        print("Data berhasil dihapus")
    else:
        print("ID tidak ditemukan")


# MAIN
while True:
    print("\\n=== INVENTORY DATABASE ===")
    print("1. Create")
    print("2. Read")
    print("3. Update")
    print("4. Delete")
    print("0. Exit")

    pilihan = input("Pilih: ")

    if pilihan == "1":
        create()
    elif pilihan == "2":
        read()
    elif pilihan == "3":
        update()
    elif pilihan == "4":
        delete()
    elif pilihan == "0":
        break
    else:
        print("Menu tidak tersedia")


conn.close()
```

### Penjelasan

**1. Membuat koneksi**

``` python
conn = sqlite3.connect("inventory.db")
```

Membuka database `inventory.db`. Jika belum ada, SQLite akan membuatnya.

**2. Membuat tabel**

``` sql
CREATE TABLE IF NOT EXISTS inventory
```

Membuat tabel inventory apabila belum tersedia.

**3. CREATE menggunakan INSERT**

``` python
cursor.execute("""
INSERT INTO inventory (nama, kategori, harga)
VALUES (?, ?, ?)
""", (nama, kategori, harga))
```

Memasukkan data ke database. Tanda `?` adalah placeholder untuk
parameter. Penggunaan parameter membantu mencegah SQL injection.

**4. READ menggunakan SELECT**

``` python
cursor.execute("SELECT * FROM inventory")
data = cursor.fetchall()
```

Mengambil seluruh baris dari tabel.

**5. UPDATE**

``` sql
UPDATE inventory
SET nama = ?, kategori = ?, harga = ?
WHERE id = ?
```

Mengubah data berdasarkan ID.

**6. DELETE**

``` sql
DELETE FROM inventory WHERE id = ?
```

Menghapus data berdasarkan ID.

  JSON                    SQLite
  ----------------------- -----------------------
  File penyimpanan        Database
  `json.load()`           `SELECT`
  `json.dump()`           `INSERT` dan `UPDATE`
  List dan dictionary     Tabel dan baris
  Tidak menggunakan SQL   Menggunakan SQL

Catatan: contoh ini fokus pada SQL dan SQLite. Validasi input dan
penanganan exception dari Tahap 3 perlu ditambahkan untuk membuatnya
lebih tangguh.

------------------------------------------------------------------------

# Tahap 7 --- Mini ERP

**Target:** Mengintegrasikan beberapa fitur bisnis dalam satu aplikasi.

Kita mengembangkan Inventory Management menjadi Mini ERP dengan dua
modul:

1.  **Inventory Management:** mengelola produk dan stok.
2.  **Sales Management:** mencatat transaksi penjualan.

Kedua modul menggunakan database SQLite yang sama.

### Gambaran arsitektur

``` text
MAIN MENU
   |
   +---- INVENTORY
   |       +---- Create Product
   |       +---- Read Product
   |       +---- Update Product
   |       +---- Delete Product
   |
   +---- SALES
   |       +---- Create Transaction
   |       +---- Sales Report
   |
   +---- SQLITE DATABASE
           +---- inventory
           +---- sales
```

### Alur bisnis

1.  Tambahkan produk Sunlight dengan stok 100.
2.  Catat penjualan 5 produk.
3.  Stok otomatis berkurang menjadi 95.
4.  Laporan penjualan menampilkan jumlah produk terjual dan omzet.

### Kode lengkap

``` python
import sqlite3

conn = sqlite3.connect("mini_erp.db")
conn.execute("PRAGMA foreign_keys = ON")
cursor = conn.cursor()

# DATABASE
cursor.execute("""
CREATE TABLE IF NOT EXISTS inventory (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nama TEXT NOT NULL,
    harga INTEGER NOT NULL,
    stok INTEGER NOT NULL
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS sales (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    produk_id INTEGER NOT NULL,
    jumlah INTEGER NOT NULL,
    total INTEGER NOT NULL,
    FOREIGN KEY (produk_id) REFERENCES inventory(id)
)
""")

conn.commit()


# INVENTORY: CREATE
def create_product():
    nama = input("Nama produk: ")
    harga = int(input("Harga: "))
    stok = int(input("Stok awal: "))

    if harga <= 0 or stok < 0:
        print("Harga harus positif dan stok tidak boleh negatif")
        return

    cursor.execute("""
    INSERT INTO inventory (nama, harga, stok)
    VALUES (?, ?, ?)
    """, (nama, harga, stok))

    conn.commit()
    print("Produk berhasil ditambahkan")


# INVENTORY: READ
def read_product():
    cursor.execute("SELECT * FROM inventory")
    products = cursor.fetchall()

    if not products:
        print("Data belum ada")
        return

    print("\\n=== DAFTAR PRODUK ===")

    for product in products:
        print(
            f"ID: {product[0]} | "
            f"Nama: {product[1]} | "
            f"Harga: {product[2]} | "
            f"Stok: {product[3]}"
        )


# INVENTORY: UPDATE
def update_product():
    read_product()

    product_id = int(input("ID produk: "))
    nama = input("Nama baru: ")
    harga = int(input("Harga baru: "))
    stok = int(input("Stok baru: "))

    if harga <= 0 or stok < 0:
        print("Harga atau stok tidak valid")
        return

    cursor.execute("""
    UPDATE inventory
    SET nama = ?, harga = ?, stok = ?
    WHERE id = ?
    """, (nama, harga, stok, product_id))

    conn.commit()

    if cursor.rowcount > 0:
        print("Produk berhasil diperbarui")
    else:
        print("Produk tidak ditemukan")


# INVENTORY: DELETE
def delete_product():
    read_product()

    product_id = int(input("ID produk: "))

    cursor.execute(
        "SELECT COUNT(*) FROM sales WHERE produk_id = ?",
        (product_id,)
    )

    if cursor.fetchone()[0] > 0:
        print("Produk memiliki riwayat transaksi, tidak dapat dihapus")
        return

    cursor.execute(
        "DELETE FROM inventory WHERE id = ?",
        (product_id,)
    )

    conn.commit()

    if cursor.rowcount > 0:
        print("Produk berhasil dihapus")
    else:
        print("Produk tidak ditemukan")


# SALES: CREATE TRANSACTION
def create_sales():
    read_product()

    product_id = int(input("ID produk: "))
    jumlah = int(input("Jumlah penjualan: "))

    if jumlah <= 0:
        print("Jumlah harus lebih dari 0")
        return

    try:
        cursor.execute("BEGIN")

        cursor.execute("""
        SELECT harga, stok
        FROM inventory
        WHERE id = ?
        """, (product_id,))

        product = cursor.fetchone()

        if product is None:
            raise ValueError("Produk tidak ditemukan")

        harga, stok = product

        if jumlah > stok:
            raise ValueError("Stok tidak mencukupi")

        total = harga * jumlah

        cursor.execute("""
        UPDATE inventory
        SET stok = stok - ?
        WHERE id = ?
        """, (jumlah, product_id))

        cursor.execute("""
        INSERT INTO sales (produk_id, jumlah, total)
        VALUES (?, ?, ?)
        """, (product_id, jumlah, total))

        conn.commit()

        print("Transaksi berhasil")
        print("Total penjualan:", total)

    except (ValueError, sqlite3.Error) as error:
        conn.rollback()
        print("Transaksi gagal:", error)


# SALES: REPORT
def sales_report():
    cursor.execute("""
    SELECT
        inventory.nama,
        SUM(sales.jumlah),
        SUM(sales.total)
    FROM sales
    JOIN inventory
    ON sales.produk_id = inventory.id
    GROUP BY inventory.id
    """)

    reports = cursor.fetchall()

    if not reports:
        print("Belum ada transaksi")
        return

    print("\\n=== LAPORAN PENJUALAN ===")

    for nama, jumlah, total in reports:
        print(
            f"Produk: {nama} | "
            f"Terjual: {jumlah} | "
            f"Omzet: Rp{total}"
        )


# MAIN MENU
while True:
    print("\\n=== MINI ERP ===")
    print("1. Tambah Produk")
    print("2. Lihat Produk")
    print("3. Update Produk")
    print("4. Hapus Produk")
    print("5. Transaksi Penjualan")
    print("6. Laporan Penjualan")
    print("0. Keluar")

    pilihan = input("Pilih menu: ")

    if pilihan == "1":
        create_product()

    elif pilihan == "2":
        read_product()

    elif pilihan == "3":
        update_product()

    elif pilihan == "4":
        delete_product()

    elif pilihan == "5":
        create_sales()

    elif pilihan == "6":
        sales_report()

    elif pilihan == "0":
        break

    else:
        print("Menu tidak tersedia")


conn.close()
```

### Penjelasan konsep Mini ERP

**1. Dua tabel yang saling berhubungan**

Tabel `inventory` menyimpan produk. Tabel `sales` menyimpan transaksi
penjualan.

Kolom `produk_id` pada tabel `sales` merupakan Foreign Key yang mengacu
pada ID produk di tabel `inventory`.

**2. Menghubungkan transaksi dengan stok**

``` python
cursor.execute("""
UPDATE inventory
SET stok = stok - ?
WHERE id = ?
""", (jumlah, product_id))
```

Jika stok awal 100 dan terjual 5, stok menjadi 95.

**3. Menghitung total penjualan**

``` python
total = harga * jumlah
```

Jika harga Rp5.000 dan jumlah 5, total penjualan adalah Rp25.000.

**4. Transaction dan Rollback**

``` python
conn.commit()
```

Menyimpan perubahan transaksi.

``` python
conn.rollback()
```

Membatalkan perubahan jika proses gagal.

Pengurangan stok dan pencatatan penjualan harus berhasil bersama-sama.
Inilah alasan keduanya diletakkan dalam satu transaksi database.

**5. JOIN**

``` sql
SELECT
    inventory.nama,
    SUM(sales.jumlah),
    SUM(sales.total)
FROM sales
JOIN inventory
ON sales.produk_id = inventory.id
GROUP BY inventory.id
```

`JOIN` menggabungkan informasi produk dan transaksi untuk membuat
laporan penjualan.

**Catatan pengembangan:** Ini adalah Mini ERP edukasi, belum ERP
produksi. Untuk aplikasi nyata, laporan historis sebaiknya menyimpan
snapshot nama dan harga produk pada saat transaksi. Validasi, penanganan
exception, pengelolaan koneksi, dan keamanan transaksi juga perlu
diperkuat.

------------------------------------------------------------------------

# Ringkasan: Apa yang Dipelajari dari 7 Tahap?

    Tahap Kemampuan yang diperoleh
  ------- --------------------------------------------------
        1 Mengelola data menggunakan struktur dasar Python
        2 Membagi program menjadi function
        3 Menangani kesalahan dan validasi
        4 Membaca dan menyimpan data ke file
        5 Mengorganisasi program menggunakan OOP
        6 Mengelola database menggunakan SQL
        7 Mengintegrasikan proses bisnis

## Peta perkembangan konsep Python

  -----------------------------------------------------------------------
  Tahap                               Materi utama
  ----------------------------------- -----------------------------------
  1\. Python Fundamentals             List, Tuple, Dictionary, Loop,
                                      Conditional

  2\. Modular Programming             Function, Parameter, Argument,
                                      Return

  3\. Error Handling                  Validation, Try, Except

  4\. Data Persistence                File, JSON, Serialization

  5\. Object-Oriented Programming     Class, Object, Self, Constructor,
                                      Method

  6\. Database Programming            SQLite, SQL, CRUD, JOIN

  7\. Business Application            Inventory, Sales, Transaction,
                                      Reporting
  -----------------------------------------------------------------------

## Checklist penguasaan

-   [ ] Tahap 1 --- Procedural CRUD
-   [ ] Tahap 2 --- Function CRUD
-   [ ] Tahap 3 --- Validation & Exception
-   [ ] Tahap 4 --- File/JSON CRUD
-   [ ] Tahap 5 --- OOP CRUD
-   [ ] Tahap 6 --- SQLite CRUD
-   [ ] Tahap 7 --- Mini ERP

Centang satu tahap setelah mampu membangun dan menjelaskan program tanpa
bergantung pada contoh.

## Strategi latihan yang direkomendasikan

Jangan menyelesaikan ketujuh tahap sekaligus. Fokus satu tahap pada satu
waktu.

Untuk setiap tahap:

1.  Jalankan program dan amati hasilnya.
2.  Jelaskan fungsi setiap bagian dengan kata-kata sendiri.
3.  Ubah satu bagian kecil dan amati dampaknya.
4.  Tulis ulang fitur tanpa melihat contoh.
5.  Tambahkan satu kebutuhan baru.
6.  Uji kondisi normal dan kondisi salah.

Setelah Inventory berhasil, buat Todo List dengan pola CRUD yang sama.
Tujuannya membuktikan bahwa yang dipahami adalah pola pemrogramannya,
bukan sekadar menghafal kode Inventory.

## Hubungan dengan target Data Science dan AI/ML

Roadmap ini juga menjadi fondasi untuk perjalanan ke Data Science:

-   Tahap 1--3: membangun logika pemrograman.
-   Tahap 4--6: memahami pengelolaan dan penyimpanan data.
-   Tahap 7: memahami proses bisnis, transaksi, dan data operasional.
-   Tahap selanjutnya: Pandas, NumPy, SQL lebih lanjut, EDA, Feature
    Engineering, dan Machine Learning.

## Kesimpulan

Jangan belajar Python hanya untuk mengetahui sebanyak mungkin sintaks.
Belajarlah Python untuk membangun sesuatu, lalu gunakan teori untuk
memahami mengapa program tersebut bekerja.

**Prinsip utama:**

> Pahami → Lengkapi → Tulis Ulang → Modifikasi → Terapkan.

Fokus dahulu menyelesaikan Tahap 1 sampai 3. Jangan terburu-buru masuk
OOP sebelum logika CRUD procedural dan function benar-benar dipahami.

Ketika masuk OOP, logika bisnisnya sudah dikenal. Hal baru yang
dipelajari adalah cara mengorganisasi data dan perilaku menggunakan
class dan object.
