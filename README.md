# **Rancangan REST API khusus untuk pengguna *Manajer Toko***.

Manajer toko punya akses terhadap:

* Login ke sistem
* Melihat data transaksi
* Melihat dan input data barang gudang
* Melihat data cabang toko
* Melihat dan input data karyawan
* Melihat laporan keuangan

---

## 📌 Autentikasi

### `POST /api/login`

Login pengguna (sama seperti kasir).

---

## 📦 Manajemen Barang Gudang

### `GET /api/inventory_barang`

Lihat semua stok barang per cabang.

**Query params:**

* `id_cabang_toko` (opsional)

**Response:**

```json
[
  {
    "id": 101,
    "kode_inventory": "INV-001",
    "stok": 50,
    "minimum_stok": 10,
    "hpp": 5000,
    "harga_barang": 8000,
    "tanggal_masuk": "2025-05-25T12:00:00",
    "id_barang": 12,
    "id_cabang_toko": 1
  }
]
```

---

### `POST /api/inventory_barang`

Input stok barang baru ke cabang tertentu.

**Request:**

```json
{
  "id_barang": 12,
  "kode_inventory": "INV-002",
  "stok": 100,
  "minimum_stok": 10,
  "hpp": 5000,
  "harga_barang": 8000,
  "id_cabang_toko": 1,
  "tanggal_masuk": "2025-05-25T14:00:00"
}
```

---

## 🧾 Transaksi Penjualan

### `GET /api/penjualan`

Lihat semua transaksi penjualan.

**Query params (opsional):**

* `id_cabang_toko`
* `tanggal_dari`
* `tanggal_sampai`

---

## 🏪 Manajemen Cabang Toko

### `GET /api/cabang_toko`

Lihat seluruh cabang toko.

**Response:**

```json
[
  {
    "id": 1,
    "nama_cabang": "Toko A",
    "alamat": "Jl. Raya No.1"
  }
]
```

---

## 👥 Manajemen Karyawan

### `GET /api/pengguna`

Lihat seluruh pengguna (karyawan) di cabang tertentu.

**Query:**

* `id_cabang_toko` (opsional)

**Response:**

```json
[
  {
    "id": 12,
    "nama": "Kasir A",
    "email": "kasir@email.com",
    "role": "kasir",
    "id_cabang_toko": 1
  }
]
```

---

### `POST /api/pengguna`

Tambah pengguna (karyawan) baru.

**Request:**

```json
{
  "nama": "Staff Gudang A",
  "email": "staff@email.com",
  "password": "rahasia123",
  "role_id": 3, // Misalnya: 3 = staff gudang
  "id_cabang_toko": 1
}
```

---

## 📊 Laporan Keuangan

### `GET /api/laporan/keuangan`

Ambil ringkasan laporan keuangan (berdasarkan transaksi penjualan).

**Query params:**

* `id_cabang_toko`
* `tanggal_dari`
* `tanggal_sampai`

**Response:**

```json
{
  "total_penjualan": 25000000,
  "jumlah_transaksi": 120,
  "rata_rata_transaksi": 208333.33
}
```

---

# **Rancangan REST API khusus untuk pengguna *Kasir***.
 
Fokus utama kasir di sistem ini adalah:

* Login ke sistem
* Input transaksi penjualan
* Melihat data transaksi yang pernah dilakukan

## 📌 Autentikasi (Login)

### POST `/api/login`

Login pengguna.

**Request:
```
{
  "email": "kasir@email.com",
  "password": "password"
}
```
Response:
```
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI...",
  "user": {
    "id": 12,
    "nama": "Kasir Toko A",
    "role": "kasir"
  }
}
```
---

## 🧾 Transaksi Penjualan

### POST `/api/penjualan`

Membuat transaksi baru.

Request:
```
{
  "id_cabang_toko": 1,
  "id_pengguna": 12,
  "tipe_pembayaran": "Cash",
  "items": [
    {
      "id_inventory_barang": 101,
      "kuantiti": 2,
      "harga_per_produk": 10000,
      "jenis_diskon": "Percent",
      "total_diskon": 10
    },
    {
      "id_inventory_barang": 102,
      "kuantiti": 1,
      "harga_per_produk": 25000,
      "jenis_diskon": "Amount",
      "total_diskon": 5000
    }
  ]
}
```
Response:
```
{
  "message": "Transaksi berhasil disimpan",
  "id_penjualan": 201
}
```
---

## 📋 Melihat Riwayat Transaksi

### GET `/api/penjualan`

Melihat semua transaksi (opsional: filter by kasir, tanggal).

Query params (opsional):

* id_pengguna
* tanggal_dari
* tanggal_sampai

Contoh:
`/api/penjualan?id_pengguna=12&tanggal_dari=2025-05-01&tanggal_sampai=2025-05-25`

Response:
```
[
  {
    "id": 201,
    "waktu": "2025-05-25T14:32:00",
    "total_penjualan": 45000,
    "tipe_pembayaran": "Cash",
    "id_cabang_toko": 1,
    "nomer_invoice": "INV-20250525-201"
  },
  ...
]
```
---

### GET `/api/penjualan/{id}`

Melihat detail transaksi tertentu.

Response:
```
{
  "id": 201,
  "waktu": "2025-05-25T14:32:00",
  "total_penjualan": 45000,
  "tipe_pembayaran": "Cash",
  "id_cabang_toko": 1,
  "nomer_invoice": "INV-20250525-201",
  "items": [
    {
      "id_inventory_barang": 101,
      "nama_barang": "Indomie Goreng",
      "kuantiti": 2,
      "harga_per_produk": 10000,
      "total_diskon": 10,
      "jenis_diskon": "Percent"
    }
  ]
}
```
---

## 🔐 Profil Pengguna

### GET `/api/profile`

Melihat data pengguna yang sedang login.

Response:
```
{
  "id": 12,
  "nama": "Kasir A",
  "email": "kasir@email.com",
  "role": "kasir",
  "cabang": {
    "id": 1,
    "nama_cabang": "Toko Utama"
  }
}
```

---

# **Rancangan REST API khusus untuk pengguna *Staff Gudang***.

Staff Gudang hanya memiliki akses ke:

* **Login**
* **Input barang gudang**
* **Melihat informasi data barang gudang**

---

## 📌 Autentikasi

### `POST /api/login`

Untuk login pengguna.

**Request:**

```json
{
  "email": "gudang@email.com",
  "password": "rahasia123"
}
```

**Response:**

```json
{
  "token": "jwt-token-di-sini",
  "user": {
    "id": 4,
    "nama": "Staff Gudang A",
    "role": "staff_gudang"
  }
}
```

---

## 📦 Barang Gudang (Inventory)

### `GET /api/inventory_barang`

Melihat daftar stok barang di cabang tempat dia bekerja.

**Query params (opsional):**

* `id_barang`
* `id_cabang_toko` (bisa diambil dari token saat login)

**Response:**

```json
[
  {
    "id": 1,
    "kode_inventory": "INV-001",
    "stok": 50,
    "minimum_stok": 10,
    "hpp": 5000,
    "harga_barang": 8000,
    "tanggal_masuk": "2025-05-25T12:00:00",
    "id_barang": 12,
    "id_cabang_toko": 1
  }
]
```

---

### `POST /api/inventory_barang`

Menambahkan stok barang baru ke cabang.

**Request:**

```json
{
  "id_barang": 12,
  "kode_inventory": "INV-002",
  "stok": 100,
  "minimum_stok": 20,
  "hpp": 6000,
  "harga_barang": 9000,
  "id_cabang_toko": 1,
  "tanggal_masuk": "2025-05-25T14:00:00"
}
```

**Catatan:**

* Data `id_cabang_toko` bisa diambil dari token user login.

---

### `GET /api/barang`

Melihat daftar master barang (nama, SKU, merk, dll).

---

## 🔁 Transaksi Barang Masuk / Keluar (Gudang)

### `POST /api/transaksi_barang_inventory`

Mencatat barang masuk atau keluar dari gudang.

**Request:**

```json
{
  "id_inventory_barang": 1,
  "jumlah": 50,
  "tanggal": "2025-05-25T16:00:00",
  "catatan": "Barang keluar untuk penyesuaian stok"
}
```

---

### `GET /api/transaksi_barang_inventory`

Melihat riwayat transaksi keluar/masuk barang gudang.

**Query params (opsional):**

* `id_inventory_barang`
* `tanggal_dari`
* `tanggal_sampai`

---

# **Rancangan REST API khusus untuk pengguna *Manajer Toko***.

**Owner** memiliki akses ke:

* Login
* Input data cabang toko baru
* Melihat semua data:

  * Transaksi
  * Barang gudang
  * Cabang toko
  * Karyawan
  * Laporan keuangan

---

## 📌 Autentikasi

### `POST /api/login`

Login untuk semua role, termasuk Owner.

```json
{
  "email": "owner@email.com",
  "password": "rahasia123"
}
```

---

## 🏪 Cabang Toko

### `POST /api/cabang_toko`

Menambahkan cabang toko baru.

```json
{
  "nama_cabang": "Cabang Yogyakarta",
  "alamat": "Jl. Malioboro No. 1"
}
```

### `GET /api/cabang_toko`

Melihat semua cabang toko.

---

## 📦 Barang Gudang (Inventory)

### `GET /api/inventory_barang`

Melihat semua stok barang dari semua cabang.

**Optional query params:**

* `id_barang`
* `id_cabang_toko`

---

### `GET /api/barang`

Melihat master data barang.

### `GET /api/kategori_barang`

Melihat semua kategori barang.

---

## 🔁 Transaksi Barang Gudang

### `GET /api/transaksi_barang_inventory`

Melihat semua riwayat transaksi barang di gudang.

**Optional query:**

* `id_cabang_toko`
* `tanggal_dari`, `tanggal_sampai`

---

## 💳 Transaksi Penjualan

### `GET /api/penjualan`

Melihat semua transaksi penjualan.

```bash
GET /api/penjualan?start_date=2025-05-01&end_date=2025-05-25
```

### `GET /api/penjualan/{id}`

Detail penjualan per transaksi.

---

## 👥 Karyawan

### `GET /api/pengguna`

Melihat semua data karyawan (seluruh user sistem).

### `GET /api/roles`

Melihat daftar role pengguna (kasir, staff gudang, manajer, dll).

---

## 📊 Laporan Keuangan

(Laporan keuangan ini diturunkan dari transaksi penjualan dan stok gudang)

### `GET /api/laporan/keuangan`

Melihat rekap laporan keuangan seluruh cabang.

```bash
GET /api/laporan/keuangan?tahun=2025&bulan=5
```

**Response:**

```json
{
  "total_penjualan": 150000000,
  "total_hpp": 90000000,
  "total_laba": 60000000
}
```

---

# **Rancangan REST API khusus untuk pengguna *Manajer Toko***.

Investor hanya bisa:

* Login
* Melihat **Informasi Laporan Keuangan**

---

## 🔐 Autentikasi

### `POST /api/login`

Login untuk semua role, termasuk investor.

```json
{
  "email": "investor@email.com",
  "password": "rahasia123"
}
```

---

## 📊 Laporan Keuangan

Data laporan keuangan diambil dari rekap penjualan dan stok barang berdasarkan `penjualan`, `penjualan_barang`, dan `inventory_barang`.

### `GET /api/laporan/keuangan`

Lihat ringkasan laporan keuangan seluruh cabang.

**Query Parameters (opsional):**

* `bulan`: angka (1–12)
* `tahun`: 4 digit tahun (default: tahun ini)

**Contoh:**

```http
GET /api/laporan/keuangan?bulan=5&tahun=2025
```

**Response contoh:**

```json
{
  "total_penjualan": 180000000,
  "total_hpp": 110000000,
  "total_laba": 70000000,
  "periode": "Mei 2025"
}
```

### `GET /api/laporan/keuangan/cabang`

Lihat laporan keuangan per cabang.

**Contoh:**

```http
GET /api/laporan/keuangan/cabang?id_cabang=3&bulan=5&tahun=2025
```

**Response contoh:**

```json
{
  "id_cabang": 3,
  "nama_cabang": "Cabang Bandung",
  "total_penjualan": 50000000,
  "total_hpp": 30000000,
  "total_laba": 20000000
}
```

---
