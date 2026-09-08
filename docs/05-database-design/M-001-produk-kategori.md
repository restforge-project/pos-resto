# DATABASE DESIGN — M-001 Manajemen Produk & Kategori
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **ID Modul** | M-001 |
| **Nama Modul** | Manajemen Produk & Kategori |
| **Bagian dari** | [README.md](README.md) (Database Design — index) |
| **Versi Dokumen** | 1.7 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-09-08 |
| **PIC** | Project Lead |
| **Status** | Draft |

> Skema ditulis dalam format **SDF RESTForge** (`schema/<table>.js`). Konvensi penamaan, kolom audit, dan tipe data mengikuti [README.md](README.md) index. Kebutuhan sumber: [PRD M-001](../03-product-requirements/M-001-produk-kategori.md).
>
> **Catatan penamaan (v1.2):** Seluruh terminologi modul diseragamkan memakai istilah **produk**. Entitas yang dijual memakai domain **`product`** (sebelumnya `menu_item`); istilah lama untuk daftar hidangan kini sepenuhnya digantikan oleh **produk**, baik pada dokumen ini maupun PRD.
>
> **Catatan penyesuaian (v1.3):** `category_code` dihapus — kategori diidentifikasi lewat `category_name` saja (FR-001 v1.2). Kolom foto pada `product_category` dan `product` memakai tipe SDF `json` (JSONB pada PostgreSQL) karena mengikuti kontrak fitur upload RESTForge, yang menyimpan **array metadata file**, bukan string path. Lihat Bagian 6.

---

## 1. Ruang Lingkup

Modul M-001 memerlukan lima tabel: satu untuk kategori produk, satu untuk produk (item yang dijual), satu untuk varian, dan dua untuk modifier (grup + opsi). Tabel-tabel ini menjadi katalog yang dikonsumsi modul Order (M-002), Pembayaran (M-005), dan Inventori (M-006).

## 2. Diagram Relasi Entitas (ERD)

```
┌────────────────────┐ 1   ∞ ┌───────────┐ 1      ∞ ┌────────────────────┐
│  product_category  │───────│  product  │──────────│  product_variant   │
└────────────────────┘       └─────┬─────┘          └────────────────────┘
                                   │ 1
                                   │
                                   │ ∞
                    ┌──────────────────────────┐ 1  ∞ ┌───────────────────────────┐
                    │  product_modifier_group  │──────│  product_modifier_option  │
                    └──────────────────────────┘      └───────────────────────────┘

  product_category 1—∞ product          : satu kategori berisi banyak produk (FR-005, BR-003)
  product 1—∞ product_variant           : satu produk punya banyak opsi varian (FR-011)
  product 1—∞ product_modifier_group    : satu produk punya banyak grup modifier (FR-012)
  product_modifier_group 1—∞ product_modifier_option : satu grup berisi banyak opsi (FR-012)
```

## 3. Daftar Tabel

| Tabel | Deskripsi | Relasi | Sumber |
|-------|-----------|--------|--------|
| `product_category` | Kategori produk | induk dari `product` | FR-001..004 |
| `product` | Produk/item yang dijual + harga dasar & status | `belongsTo product_category` | FR-005..010 |
| `product_variant` | Opsi varian produk (mis. ukuran) | `belongsTo product` | FR-011 |
| `product_modifier_group` | Grup modifier (wajib/opsional, min/maks) | `belongsTo product` | FR-012/013 |
| `product_modifier_option` | Opsi dalam grup modifier | `belongsTo product_modifier_group` | FR-012 |

---

## 4. Definisi Skema (SDF)

### 4.1 `product_category` — `schema/product_category.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('product_category', {
  schema: 'public',

  fields: {
    product_category_id: 'string:36 pk',
    category_name:       'string:100 notnull unique',
    description:         'text',
    photo_url:           'json',
    sort_order:          'integer notnull default:0',
    is_active:           'boolean default:true',
    created_at:          'timestamp default:now()',
    created_by:          'string:100',
    updated_at:          'timestamp',
    updated_by:          'string:100'
  },

  indexes: [
    'is_active'
  ]
});
```

**Catatan:** `category_name` `unique` sebagai satu-satunya identitas kategori (FR-001, BR-001); kode kategori tidak dipakai. Keunikan **case-insensitive** ditegakkan di layer RDF/aplikasi (lihat Bagian 5). `photo_url` menampung array metadata file foto kategori (lihat Bagian 6). `sort_order` mendukung pengurutan tampil (FR-003). `is_active` untuk nonaktif tanpa hapus (FR-004).

**Contoh data:**

| category_name | description | sort_order | is_active |
|---|---|---|---|
| Makanan | Hidangan utama | 1 | true |
| Minuman | Aneka minuman | 2 | true |
| Cemilan | Camilan & gorengan | 3 | true |
| Paket Hemat | Paket bundling hemat | 4 | true |

> Contoh data merujuk relasi memakai **nama kategori** dan **kode produk** agar mudah dibaca (mis. `product.product_category_id` ditunjuk lewat `category_name`). Di database, nilai `*_id` sesungguhnya berupa UUID `string:36`. Kolom audit (`created_at`, dst) dan `photo_url` diisi runtime dan tidak ditampilkan.

### 4.2 `product` — `schema/product.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('product', {
  schema: 'public',

  fields: {
    product_id:          'string:36 pk',
    product_code:        'string:20 notnull unique',
    product_name:        'string:100 notnull',
    product_category_id: 'string:36 notnull',
    description:         'text',
    base_price:          'decimal:15,2 notnull default:0',
    photo_url:           'json',
    is_available:        'boolean default:true',
    is_active:           'boolean default:true',
    sort_order:          'integer notnull default:0',
    created_at:          'timestamp default:now()',
    created_by:          'string:100',
    updated_at:          'timestamp',
    updated_by:          'string:100'
  },

  checks: [
    { field: 'base_price', gte: 0 }
  ],

  indexes: [
    'product_category_id',
    'is_active',
    'is_available'
  ],

  relations: {
    product_category: {
      type: 'belongsTo',
      target: 'product_category',
      localKey: 'product_category_id',
      references: 'product_category_id',
      onDelete: 'restrict',
      onUpdate: 'restrict'
    }
  }
});
```

**Catatan:** `product_code notnull unique` mewujudkan kode produk wajib dan unik (FR-005, BR-011). `product_category_id notnull` + FK `onDelete: restrict` mewujudkan BR-003 (produk wajib satu kategori) dan BR-002 (kategori tak bisa dihapus selama dipakai produk). `base_price` dijaga `>= 0` lewat CHECK (BR-004). `is_available` = status Habis/Tersedia manual (FR-010, BR-010); `is_active` = status aktif/nonaktif record (FR-009, FR-004), **bukan** penanda penghapusan. Pencegahan hapus produk yang masih dipakai transaksi ditangani FK `onDelete: restrict` dari tabel order (M-002), bukan `is_active` (BR-005). `photo_url` menampung array metadata file foto produk (FR-008, lihat Bagian 6).

**Contoh data:**

| product_code | product_name | product_category_id | base_price | is_available | is_active |
|---|---|---|---|---|---|
| PRD-001 | Nasi Goreng Spesial | Makanan | 25000 | true | true |
| PRD-002 | Mie Ayam Bakso | Makanan | 20000 | true | true |
| PRD-003 | Ayam Geprek | Makanan | 22000 | true | true |
| PRD-004 | Es Teh Manis | Minuman | 5000 | true | true |
| PRD-005 | Es Jeruk | Minuman | 8000 | true | true |
| PRD-006 | Kopi Hitam | Minuman | 7000 | true | true |
| PRD-007 | Kentang Goreng | Cemilan | 15000 | true | true |
| PRD-008 | Paket Hemat A | Paket Hemat | 30000 | false | true |

### 4.3 `product_variant` — `schema/product_variant.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('product_variant', {
  schema: 'public',

  fields: {
    product_variant_id: 'string:36 pk',
    product_id:         'string:36 notnull',
    group_name:         'string:50 notnull',
    option_name:        'string:50 notnull',
    price_mode:         "string:10 notnull default:'adjustment'",
    price_adjustment:   'decimal:15,2 notnull default:0',
    is_default:         'boolean default:false',
    sort_order:         'integer notnull default:0',
    is_active:          'boolean default:true',
    created_at:         'timestamp default:now()',
    created_by:         'string:100',
    updated_at:         'timestamp',
    updated_by:         'string:100'
  },

  uniques: [
    ['product_id', 'group_name', 'option_name']
  ],

  checks: [
    { field: 'price_mode', in: ['adjustment', 'absolute'] },
    { field: 'price_adjustment', gte: 0 }
  ],

  indexes: [
    'product_id'
  ],

  relations: {
    product: {
      type: 'belongsTo',
      target: 'product',
      localKey: 'product_id',
      references: 'product_id',
      onDelete: 'cascade',
      onUpdate: 'restrict'
    }
  }
});
```

**Catatan:** `group_name` (mis. "Ukuran") + `option_name` (mis. "Besar") dengan harga per opsi (FR-011). `price_mode` menentukan arti `price_adjustment`: pada `adjustment` nilainya selisih yang ditambahkan ke `product.base_price`, pada `absolute` nilainya harga jual opsi itu sendiri. Nama kolom `price_adjustment` dipertahankan agar data dan endpoint yang ada tidak perlu dimigrasi. CHECK `in` membatasi mode pada dua nilai itu; `price_adjustment >= 0` (BR-004). Aturan harga absolut minimal sama dengan harga dasar (BR-012) melibatkan dua tabel sehingga ditegakkan di layer aplikasi (lihat Bagian 5). Composite unique mencegah opsi varian ganda pada produk yang sama. FK `onDelete: cascade`, yaitu varian ikut terhapus bila produk dihapus.

**Contoh data:**

| product_id | group_name | option_name | price_mode | price_adjustment | is_default | sort_order | Harga opsi |
|---|---|---|---|---|---|---|---|
| PRD-001 | Porsi | Biasa | adjustment | 0 | true | 1 | 25000 |
| PRD-001 | Porsi | Jumbo | adjustment | 8000 | false | 2 | 33000 |
| PRD-004 | Ukuran | Reguler | adjustment | 0 | true | 1 | 5000 |
| PRD-004 | Ukuran | Jumbo | absolute | 8000 | false | 2 | 8000 |
| PRD-006 | Ukuran | Reguler | adjustment | 0 | true | 1 | 7000 |
| PRD-006 | Ukuran | Large | absolute | 11000 | false | 2 | 11000 |

Kolom "Harga opsi" bukan kolom tabel, melainkan hasil hitung FR-014 dari `base_price` produk dan baris varian: mode `adjustment` menjumlahkan, mode `absolute` memakai nilainya langsung.

### 4.4 `product_modifier_group` — `schema/product_modifier_group.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('product_modifier_group', {
  schema: 'public',

  fields: {
    product_modifier_group_id: 'string:36 pk',
    product_id:                'string:36 notnull',
    group_name:                'string:50 notnull',
    is_required:               'boolean default:false',
    min_select:                'integer notnull default:0',
    max_select:                'integer notnull default:1',
    sort_order:                'integer notnull default:0',
    is_active:                 'boolean default:true',
    created_at:                'timestamp default:now()',
    created_by:                'string:100',
    updated_at:                'timestamp',
    updated_by:                'string:100'
  },

  uniques: [
    ['product_id', 'group_name']
  ],

  checks: [
    { field: 'min_select', gte: 0 },
    { field: 'max_select', gte: 1 }
  ],

  indexes: [
    'product_id'
  ],

  relations: {
    product: {
      type: 'belongsTo',
      target: 'product',
      localKey: 'product_id',
      references: 'product_id',
      onDelete: 'cascade',
      onUpdate: 'restrict'
    }
  }
});
```

**Catatan:** `is_required`, `min_select`, `max_select` mewujudkan aturan wajib/opsional & batas pilihan (FR-013, BR-008). CHECK menjaga `min_select >= 0` dan `max_select >= 1`. Konsistensi `min_select <= max_select` antar-field dan syarat `min_select >= 1` pada grup wajib tidak dapat dinyatakan di CHECK SDF maupun `fieldValidation` RDF, sehingga hanya ditegakkan di client (lihat Bagian 5).

**Contoh data:**

| product_id | group_name | is_required | min_select | max_select | sort_order |
|---|---|---|---|---|---|
| PRD-003 | Level Pedas | true | 1 | 1 | 1 |
| PRD-001 | Topping | false | 0 | 3 | 1 |
| PRD-002 | Tambahan | false | 0 | 2 | 1 |

### 4.5 `product_modifier_option` — `schema/product_modifier_option.js`

```javascript
'use strict';

module.exports = ({ defineModel }) => defineModel('product_modifier_option', {
  schema: 'public',

  fields: {
    product_modifier_option_id: 'string:36 pk',
    product_modifier_group_id:  'string:36 notnull',
    option_name:                'string:50 notnull',
    extra_price:                'decimal:15,2 notnull default:0',
    is_default:                 'boolean default:false',
    sort_order:                 'integer notnull default:0',
    is_active:                  'boolean default:true',
    created_at:                 'timestamp default:now()',
    created_by:                 'string:100',
    updated_at:                 'timestamp',
    updated_by:                 'string:100'
  },

  uniques: [
    ['product_modifier_group_id', 'option_name']
  ],

  checks: [
    { field: 'extra_price', gte: 0 }
  ],

  indexes: [
    'product_modifier_group_id'
  ],

  relations: {
    product_modifier_group: {
      type: 'belongsTo',
      target: 'product_modifier_group',
      localKey: 'product_modifier_group_id',
      references: 'product_modifier_group_id',
      onDelete: 'cascade',
      onUpdate: 'restrict'
    }
  }
});
```

**Catatan:** Opsi modifier (mis. "Keju", "Telur") dengan `extra_price` (FR-012). Composite unique mencegah opsi ganda dalam satu grup. `extra_price >= 0` (BR-004). FK `onDelete: cascade` ke grup.

**Contoh data:**

| product_modifier_group_id (produk · grup) | option_name | extra_price | is_default | sort_order |
|---|---|---|---|---|
| PRD-003 · Level Pedas | Level 1 | 0 | true | 1 |
| PRD-003 · Level Pedas | Level 3 | 0 | false | 2 |
| PRD-003 · Level Pedas | Level 5 | 0 | false | 3 |
| PRD-003 · Level Pedas | Level 10 | 0 | false | 4 |
| PRD-001 · Topping | Telur Mata Sapi | 4000 | false | 1 |
| PRD-001 · Topping | Keju | 5000 | false | 2 |
| PRD-001 · Topping | Sosis | 6000 | false | 3 |
| PRD-002 · Tambahan | Bakso Extra | 5000 | false | 1 |
| PRD-002 · Tambahan | Pangsit | 4000 | false | 2 |

---

## 5. Penegakan di Layer Lain

Sebagian aturan bisnis tidak dapat dinyatakan murni di SDF dan ditegakkan di layer RDF/aplikasi:

| Aturan | Penegakan |
|--------|-----------|
| BR-001 — keunikan nama kategori **case-insensitive** | Validasi RDF (`fieldValidation`) / query `UPPER()`; DB hanya menjamin unik case-sensitive |
| FR-001, FR-008 — batas format dan ukuran file foto | `uploadConfig` di payload RDF (`allowedTypes`, `maxFileSize`, `maxFiles`); database hanya menyimpan metadata, tanpa validasi file |
| BR-012 — harga varian mode `absolute` minimal sama dengan `product.base_price` | Validasi client pada form varian (`js/variants.js`); CHECK tidak dapat membandingkan kolom lintas tabel, dan `fieldValidation` RDF hanya menilai field pada record yang sama |
| BR-008 — `min_select <= max_select` pada grup modifier | Validasi client pada form modifier (`js/modifiers.js`). CHECK SDF hanya membandingkan satu kolom dengan nilai literal, dan constraint lintas field pada `fieldValidation` RDF (`before`/`after`) hanya berlaku untuk tipe date. Backend tidak menolak nilai yang melanggar; validasi order pada M-002 menjadi pengaman berikutnya |
| BR-008 — grup wajib (`is_required = true`) mensyaratkan `min_select >= 1` | Validasi client pada form modifier (`js/modifiers.js`), dengan keterbatasan yang sama seperti baris di atas |

---

## 6. Penyimpanan Foto (File Storage)

Kolom `photo_url` pada `product_category` dan `product` mengikuti kontrak fitur upload RESTForge, bukan kolom path biasa.

| Aspek | Ketentuan |
|-------|-----------|
| Tipe SDF | `json` → JSONB pada PostgreSQL (MySQL: JSON, Oracle: CLOB, SQLite: TEXT) |
| Isi kolom | **Array** metadata file: `key`, `originalName`, `fileName`, `contentType`, `size`, `url`, `provider`, `uploadedAt`, `uploadedBy` |
| Aktivasi endpoint | `action.upload: true` + `uploadConfig` pada payload RDF; menghasilkan `/upload` dan `/upload-delete` |
| Storage provider | `STORAGE_PROVIDER=s3` pada `config/db-connection.env`; bucket `pos-storage-26` region `ap-southeast-3`, dengan key mengikuti pola `{project}/{module}/{endpoint}/{prefix}/{yyyy-mm}/{uuid}-{nama-file}`. Nilai `url` pada metadata berupa URL bucket S3, sehingga static file serving tidak diperlukan |
| Prasyarat | `STORAGE_ENABLED=true`, kredensial S3 (`S3_BUCKET`, `S3_REGION`, `S3_ACCESS_KEY_ID`, `S3_SECRET_ACCESS_KEY`), `UPLOAD_TEMP_DIR` untuk buffer Multer, serta Redis aktif untuk orphan file tracking dan cleanup job |
| Hapus record | File ikut dihapus otomatis saat `/delete` selama `uploadConfig.deleteOnRecordDelete` bernilai `true` (default) |

Endpoint `/upload` tidak menyentuh database. Client meng-upload file lebih dulu, lalu mengirim metadata hasil upload sebagai nilai field `photo_url` pada `/create` atau `/update`.

> **Status verifikasi (2026-08-30):** akses bucket sudah diuji dan berfungsi untuk operasi `HeadBucket`, `ListObjectsV2`, `PutObject`, `GetObject`, dan `DeleteObject`. Redis pada `localhost:6380` belum dapat dipakai karena `REDIS_PASSWORD` masih kosong sementara instance-nya menuntut autentikasi (`NOAUTH Authentication required`), sehingga orphan file tracking dan cleanup job belum aktif.

> **Catatan:** tipe kolom tidak boleh diganti menjadi `string`. Validator payload RESTForge hanya menerima `columnType` bernilai `jsonb`, `json`, `clob`, atau `text` untuk field upload.

---

## Riwayat Perubahan

| Versi | Tanggal | Perubahan | PIC |
|-------|---------|-----------|-----|
| 1.1 | 2026-06-06 | Penyelarasan hasil review dokumen | Project Lead |
| 1.2 | 2026-06-06 | Terminologi modul diseragamkan menjadi **produk** (`menu_item` → `product`) | Project Lead |
| 1.3 | 2026-08-30 | `category_code` dihapus dari `product_category`; `photo_url` ditambahkan pada `product_category` dan diubah menjadi `json` pada `product` mengikuti kontrak fitur upload; Bagian 6 ditambahkan | Project Lead |
| 1.4 | 2026-08-30 | Bagian 6: storage provider diubah dari local menjadi AWS S3 (bucket `pos-storage-26`, region `ap-southeast-3`); prasyarat dan status verifikasi akses bucket ditambahkan | Project Lead |
| 1.5 | 2026-09-08 | Catatan 4.2: `product_code` ditautkan ke FR-005 dan BR-011 setelah kode produk ditambahkan ke PRD ([issue #01](../20-issue/issue-01-product-code-wajib-tidak-ada-di-prd.md)) | Project Lead |
| 1.6 | 2026-09-08 | `price_mode` (`adjustment`/`absolute`) ditambahkan pada `product_variant` beserta CHECK `in`; catatan dan contoh data 4.3 disesuaikan; Bagian 5 memuat penegakan BR-012 ([issue #02](../20-issue/issue-02-mode-harga-varian-absolut-tidak-didukung-schema.md)) | Project Lead |
| 1.7 | 2026-09-08 | Catatan 4.4 dan Bagian 5: aturan `min_select <= max_select` dan `min_select >= 1` pada grup wajib dinyatakan hanya ditegakkan di client, bukan di RDF ([issue #04](../20-issue/issue-04-min-select-max-select-hanya-divalidasi-frontend.md)) | Project Lead |