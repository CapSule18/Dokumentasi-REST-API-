**1. Kasir**

Fokus utama kasir adalah login, input transaksi penjualan, dan melihat data transaksi yang pernah dilakukan.

**1.1. Autentikasi**

**POST `/api/login`**

Login pengguna.

**Method:**
`POST`

**Request:**
```json
{
    "email": "kasir@email.com",
    "password": "password"
}
```

**Response:**
```json
{
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI...",
    "user": {
        "id": 12,
        "nama": "Kasir Toko A",
        "role": "kasir"
    }
}
```
        

**1.2. Transaksi Penjualan**

**POST `/api/penjualan`**

Membuat transaksi baru.

**Method:**
`POST`

**Request:**
```json
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

**Response:**
```json
{
    "message": "Transaksi berhasil disimpan",
    "id_penjualan": 201
}
```
        

**1.3. Melihat Riwayat Transaksi**

**GET `/api/penjualan`**

Melihat semua transaksi (opsional: filter by kasir, tanggal).

**Method:**
`GET`

**Query Params (opsional):**
* `id_pengguna`
* `tanggal_dari`
* `tanggal_sampai`

**Contoh Pemanggilan:**
`/api/penjualan?id_pengguna=12&tanggal_dari=2025-05-01&tanggal_sampai=2025-05-25`

**Response:**
```json
[
    {
        "id": 201,
        "waktu": "2025-05-25T14:32:00",
        "total_penjualan": 45000,
        "tipe_pembayaran": "Cash",
        "id_cabang_toko": 1,
        "nomer_invoice": "INV-20250525-201"
    }
]
```

**1.4. Detail Transaksi**

**GET `/api/penjualan/{id}`**

Melihat detail transaksi tertentu.

**Method:**
`GET`

**Response:**
```json
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

**1.5. Profil Pengguna**

**GET `/api/profile`**

Melihat data pengguna yang sedang login.

**Method:**
`GET`

**Response:**
```json
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

**2. Staff Gudang**

Staff Gudang memiliki akses ke login, input barang gudang, dan melihat informasi data barang gudang.

**2.1. Autentikasi**

**POST `/api/login`**

Untuk login pengguna.

**Method:**
`POST`

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

**2.2. List Barang Gudang (Inventory)**

**GET `/api/inventory_barang`**

Melihat daftar stok barang di cabang tempat dia bekerja.

**Method:**
`GET`

**Query Params (opsional):**
* `id_barang`

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

**2.3. Menambahkan stok barang baru ke cabang.**

**POST `/api/inventory_barang`**

**Method:**
`POST`

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

Data `id_cabang_toko` bisa diambil dari token user login.

**2.4. List Master Data Barang.**

**GET `/api/barang`**

Melihat daftar master barang (nama, SKU, merk, dll).

**Method:**
`GET`

**Response:**
```json
[
    {
        "id": 1,
        "nama_barang": "SSD SATA SAMSUNG 512GB",
        "sku": "SAMSUNG-SATA-512-83298491812",
        "merk": "Samsung",
        "id_kategori_barang": 1,
        "category": {
            "kategori": "Part PC"
        }
    }
]
```

**2.5. Transaksi Barang Masuk / Keluar (Gudang)**

**POST `/api/transaksi_barang_inventory`**

Mencatat barang masuk atau keluar dari gudang.

**Method:**
`POST`

**Request:**
```json
{
    "id_inventory_barang": 1,
    "jumlah": -50,
    "tanggal": "2025-05-25T16:00:00",
    "catatan": "Barang keluar untuk penyesuaian stok"
}
```

**2.6. Riwayat Transaksi Barang Masuk / Keluar (Gudang)**

**GET `/api/transaksi_barang_inventory`**

Melihat riwayat transaksi keluar/masuk barang gudang.

**Method:**
`GET`

**Query Params (opsional):**
* `id_inventory_barang`
* `tanggal_dari`
* `tanggal_sampai`

**Response:**
```json
[
    {
        "id_inventory_barang": 1,
        "jumlah": -50,
        "tanggal": "2025-05-25T16:00:00",
        "catatan": "Barang keluar untuk penyesuaian stok"
    },
]
```


**3. Manajer Toko**

Manajer toko punya akses terhadap login, melihat data transaksi, melihat dan input data barang gudang, melihat data cabang toko, melihat dan input data karyawan, serta melihat laporan keuangan.

**3.1. Autentikasi**

**POST `/api/login`**

Login pengguna (sama seperti kasir).

**Method:**
`POST`

**3.2. Manajemen Barang Gudang**

**GET `/api/inventory_barang`**

Lihat semua stok barang per cabang.

**Method:**
`GET`

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

**3.3. Manajemen Barang Gudang**

**POST `/api/inventory_barang`**

Input stok barang baru ke cabang tertentu.

**Method:**
`POST`

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

**3.4. Transaksi Penjualan**

**GET `/api/penjualan`**

Lihat semua transaksi penjualan.

**Method:**
`GET`

**Query Params (opsional):**
* `id_cabang_toko`
* `tanggal_dari`
* `tanggal_sampai`

**3.5. Manajemen Cabang Toko**

**GET `/api/cabang_toko`**

Lihat seluruh cabang toko.

**Method:**
`GET`

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
        
**3.6. List Karyawan**

**GET `/api/pengguna`**

Lihat seluruh pengguna (karyawan) di cabang tertentu.

**Method:**
`GET`

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
        
**3.7. Tambah Data Karyawan**

**POST `/api/pengguna`**

Tambah pengguna (karyawan) baru.

**Method:**
`POST`

**Request:**
```json
{
    "nama": "Staff Gudang A",
    "email": "staff@email.com",
    "password": "rahasia123",
    "role_id": 3, // Misalnya: 3 = staff gudang
}
```

**3.8. Laporan Keuangan**

**GET `/api/laporan/keuangan`**

Ambil ringkasan laporan keuangan (berdasarkan transaksi penjualan).

**Method:**
`GET`

**Query Params:**
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
        

**4. Owner**

Owner memiliki akses ke login, input data cabang toko baru, dan melihat semua data transaksi, barang gudang, cabang toko, karyawan, serta laporan keuangan.

**4.1. Autentikasi**

**POST `/api/login`**
Login untuk semua role, termasuk Owner.

**Method:**
`POST`

**Request:**
```json
{
    "email": "owner@email.com",
    "password": "rahasia123"
}
```

**4.2. Menambah Cabang Toko**

**POST `/api/cabang_toko`**

Menambahkan cabang toko baru.

**Method:**
`POST`

**Request:**
```json
{
    "nama_cabang": "Cabang Yogyakarta",
    "alamat": "Jl. Malioboro No. 1"
}
```

**4.3. List Cabang Toko**

**GET `/api/cabang_toko`**

Melihat semua cabang toko.

**Method:**
`GET`

**4.4. List Barang Gudang (Inventory)**

**GET `/api/inventory_barang`**

Melihat semua stok barang dari semua cabang.

**Method:**
`GET`

**Query Params (opsional):**
* `id_barang`
* `id_cabang_toko`

**4.5. List Barang Gudang (Inventory)**

**GET `/api/barang`**

Melihat master data barang.

**Method:**
`GET`

**4.6. List Transaksi Barang Gudang**

**GET `/api/transaksi_barang_inventory`**

Melihat semua riwayat transaksi barang di gudang.

**Method:**
`GET`

**Query (opsional):**
* `id_cabang_toko`
* `tanggal_dari`, `tanggal_sampai`

**4.7. List Transaksi Penjualan**

**GET `/api/penjualan`**

Melihat semua transaksi penjualan.

**Method:**
`GET`

**Contoh Pemanggilan:**
`GET /api/penjualan?start_date=2025-05-01&end_date=2025-05-25`

**4.8. Transaksi Penjualan**

**GET `/api/penjualan/{id}`**

Detail penjualan per transaksi.

**Method:**
`GET`

**4.9. List Karyawan**

**GET `/api/pengguna`**
Melihat semua data karyawan (seluruh user sistem).

**Method:**
`GET`

**4.10. Laporan Keuangan**

**GET `/api/laporan/keuangan`**
Melihat rekap laporan keuangan seluruh cabang.

**Method:**
`GET`

**Contoh Pemanggilan:**
`GET /api/laporan/keuangan?tahun=2025&bulan=5`

**Response:**
```json
{
"total_penjualan": 150000000,
"total_hpp": 90000000,
"total_laba": 60000000
}
```

**5. Investor**

Investor hanya bisa login dan melihat Informasi Laporan Keuangan.

**5.1. Autentikasi**

**POST `/api/login`**
Login untuk semua role, termasuk investor.

**Method:**
`POST`

**Request:**
```json
{
"email": "investor@email.com",
"password": "rahasia123"
}
```

**5.2. Laporan Keuangan**

**GET `/api/laporan/keuangan`**

Lihat ringkasan laporan keuangan seluruh cabang.

**Method:**
`GET`

**Query Params (opsional):**
* `bulan`: angka (1–12)
* `tahun`: 4 digit tahun (default: tahun ini)

**Contoh Pemanggilan:**
`GET /api/laporan/keuangan?bulan=5&tahun=2025`

**Response:**
```json
{
    "total_penjualan": 180000000,
    "total_hpp": 110000000,
    "total_laba": 70000000,
    "periode": "Mei 2025"
}
```

**5.3. Laporan Keuangan per cabang**

**GET `/api/laporan/keuangan/cabang/{id_cabang}`**

Lihat laporan keuangan per cabang.

**Method:**
`GET`

**Contoh Pemanggilan:**
`GET /api/laporan/keuangan/cabang/3?bulan=5&tahun=2025`

**Response:**

```json
{
    "id_cabang": 3,
    "nama_cabang": "Cabang Bandung",
    "total_penjualan": 50000000,
    "total_hpp": 30000000,
    "total_laba": 20000000
}
```
