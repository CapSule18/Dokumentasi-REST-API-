## Penjelasan Class Diagram

Class diagram pada sistem ini menggambarkan **struktur data dan hubungan antar entitas** dalam sistem manajemen toko yang digunakan oleh berbagai role pengguna (Kasir, Staff Gudang, Manajer Toko, Owner, dan Investor). Diagram ini merepresentasikan class (tabel/data model) beserta atribut dan relasi antar class-nya.

### 1. **Pengguna**

Mewakili semua user yang mengakses sistem.

* **Atribut:** `id_pengguna`, `nama`, `email`, `password`, `role`, `id_cabang_toko`
* **Relasi:**

  * Satu `pengguna` dapat melakukan banyak aktivitas tergantung peran, seperti membuat transaksi, mencatat barang gudang, dan input data.
  * Memiliki relasi dengan `CabangToko` (mengindikasikan lokasi kerja pengguna).

---

### 2. **CabangToko**

Mewakili cabang fisik tempat penjualan dan penyimpanan barang.

* **Atribut:** `id_cabang`, `nama_cabang`, `alamat`
* **Relasi:**

  * Satu `CabangToko` dimiliki oleh banyak `Pengguna` dan menyimpan banyak `InventoryBarang`.

---

### 3. **Barang**

Mewakili informasi produk/barang yang dijual.

* **Atribut:** `id_barang`, `nama_barang`, `merk`, `id_kategori_barang`
* **Relasi:**

  * Satu `Barang` masuk dalam satu `KategoriBarang`
  * Digunakan dalam `InventoryBarang` dan transaksi `PenjualanBarang`

---

### 4. **KategoriBarang**

Mengelompokkan barang dalam kategori tertentu (contoh: elektronik, makanan, pakaian).

* **Atribut:** `id_kategori_barang`, `nama_kategori`
* **Relasi:**

  * Satu `KategoriBarang` dapat memiliki banyak `Barang`

---

### 5. **InventoryBarang**

Mewakili stok fisik barang yang tersedia di cabang tertentu.

* **Atribut:** `id_inventory`, `kode_inventory`, `stok`, `hpp`, `harga_barang`, `minimum_stok`, `tanggal_masuk`, `id_barang`, `id_cabang_toko`
* **Relasi:**

  * Merujuk ke `Barang` dan `CabangToko`
  * Digunakan saat transaksi penjualan

---

### 6. **TransaksiBarangInventory**

Mencatat aktivitas keluar/masuk barang di gudang (penambahan stok atau pengurangan).

* **Atribut:** `id_transaksi`, `id_inventory_barang`, `jumlah`, `tanggal`, `catatan`
* **Relasi:**

  * Satu transaksi berkaitan dengan satu item `InventoryBarang`

---

### 7. **Penjualan**

Mewakili satu transaksi penjualan yang dilakukan oleh kasir.

* **Atribut:** `id_penjualan`, `kode_penjualan`, `tanggal`, `total_harga`, `id_pengguna`, `id_cabang_toko`
* **Relasi:**

  * Dilakukan oleh satu `Pengguna` dan terjadi di satu `CabangToko`
  * Terkait dengan banyak `PenjualanBarang`

---

### 8. **PenjualanBarang**

Mencatat barang apa saja yang dibeli dalam satu transaksi.

* **Atribut:** `id_penjualan_barang`, `id_penjualan`, `id_barang`, `jumlah`, `harga_satuan`, `subtotal`
* **Relasi:**

  * Merujuk ke `Penjualan` dan `Barang`

---
