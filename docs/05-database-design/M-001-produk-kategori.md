# DATABASE DESIGN — M-001 Manajemen Produk & Kategori
## POS Rumah Makan — Point of Sale System untuk Restoran

---

| Informasi Dokumen | |
|---|---|
| **ID Modul** | M-001 |
| **Nama Modul** | Manajemen Produk & Kategori |
| **Bagian dari** | [README.md](README.md) (Database Design — index) |
| **Versi Dokumen** | 1.1 |
| **Tanggal Dibuat** | 2026-06-06 |
| **Terakhir Diperbarui** | 2026-06-06 |
| **PIC** | Project Lead |
| **Status** | Draft |

> Skema ditulis dalam format **SDF RESTForge** (`schema/<table>.js`). Konvensi penamaan, kolom audit, dan tipe data mengikuti [README.md](README.md) index. Kebutuhan sumber: [PRD M-001](../03-product-requirements/M-001-produk-kategori.md).
>
> **Catatan penamaan (v1.2):** Seluruh terminologi modul diseragamkan memakai istilah **produk**. Entitas yang dijual memakai domain **`product`** (sebelumnya `menu_item`); istilah lama untuk daftar hidangan kini sepenuhnya digantikan oleh **produk**, baik pada dokumen ini maupun PRD.

---

## 1. Ruang Lingkup (Scope)

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

## 3. Daftar Tabel (Table Summary)

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
    category_code:       'string:20 notnull unique',
    category_name:       'string:100 notnull unique',
    description:         'text',
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

**Catatan:** `category_code` dan `category_name` `unique` (FR-001, BR-001). Keunikan **case-insensitive** ditegakkan di layer RDF/aplikasi (lihat Bagian 5). `sort_order` mendukung pengurutan tampil (FR-003). `is_active` untuk nonaktif tanpa hapus (FR-004).

**Contoh data:**

| category_code | category_name | description | sort_order | is_active |
|---|---|---|---|---|
| MKN | Makanan | Hidangan utama | 1 | true |
| MIN | Minuman | Aneka minuman | 2 | true |
| CML | Cemilan | Camilan & gorengan | 3 | true |
| PKT | Paket Hemat | Paket bundling hemat | 4 | true |

> Contoh data merujuk relasi memakai **kode** agar mudah dibaca (mis. `product.product_category_id` ditunjuk lewat `category_code`). Di database, nilai `*_id` sesungguhnya berupa UUID `string:36`. Kolom audit (`created_at`, dst) diisi runtime dan tidak ditampilkan.

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
    photo_url:           'string:255',
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

**Catatan:** `product_category_id notnull` + FK `onDelete: restrict` mewujudkan BR-003 (produk wajib satu kategori) dan BR-002 (kategori tak bisa dihapus selama dipakai produk). `base_price` dijaga `>= 0` lewat CHECK (BR-004). `is_available` = status Habis/Tersedia manual (FR-010, BR-010); `is_active` = status aktif/nonaktif record (FR-009, FR-004), **bukan** penanda penghapusan. Pencegahan hapus produk yang masih dipakai transaksi ditangani FK `onDelete: restrict` dari tabel order (M-002), bukan `is_active` (BR-005). `photo_url` menyimpan path/URL foto (FR-008).

**Contoh data:**

| product_code | product_name | product_category_id | base_price | photo_url | is_available | is_active |
|---|---|---|---|---|---|---|
| PRD-001 | Nasi Goreng Spesial | MKN | 25000 | nasi-goreng-spesial.jpg | true | true |
| PRD-002 | Mie Ayam Bakso | MKN | 20000 | mie-ayam-bakso.jpg | true | true |
| PRD-003 | Ayam Geprek | MKN | 22000 | ayam-geprek.jpg | true | true |
| PRD-004 | Es Teh Manis | MIN | 5000 | es-teh-manis.jpg | true | true |
| PRD-005 | Es Jeruk | MIN | 8000 | es-jeruk.jpg | true | true |
| PRD-006 | Kopi Hitam | MIN | 7000 | kopi-hitam.jpg | true | true |
| PRD-007 | Kentang Goreng | CML | 15000 | kentang-goreng.jpg | true | true |
| PRD-008 | Paket Hemat A | PKT | 30000 | paket-hemat-a.jpg | false | true |

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

**Catatan:** `group_name` (mis. "Ukuran") + `option_name` (mis. "Besar") dengan `price_adjustment` (FR-011). Composite unique mencegah opsi varian ganda pada produk yang sama. FK `onDelete: cascade` — varian ikut terhapus bila produk dihapus. `price_adjustment >= 0` (BR-004).

**Contoh data:**

| product_id | group_name | option_name | price_adjustment | is_default | sort_order |
|---|---|---|---|---|---|
| PRD-001 | Porsi | Biasa | 0 | true | 1 |
| PRD-001 | Porsi | Jumbo | 8000 | false | 2 |
| PRD-004 | Ukuran | Reguler | 0 | true | 1 |
| PRD-004 | Ukuran | Jumbo | 3000 | false | 2 |
| PRD-006 | Ukuran | Reguler | 0 | true | 1 |
| PRD-006 | Ukuran | Large | 4000 | false | 2 |

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

**Catatan:** `is_required`, `min_select`, `max_select` mewujudkan aturan wajib/opsional & batas pilihan (FR-013, BR-008). CHECK menjaga `min_select >= 0` dan `max_select >= 1`. Konsistensi `min_select <= max_select` antar-field tidak didukung CHECK SDF dan ditegakkan di layer RDF/aplikasi (lihat Bagian 5).

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

## 5. Penegakan di Layer Lain (Enforcement Beyond Schema)

Sebagian aturan bisnis tidak dapat dinyatakan murni di SDF dan ditegakkan di layer RDF/aplikasi:

| Aturan | Penegakan |
|--------|-----------|
| BR-001 — keunikan nama kategori **case-insensitive** | Validasi RDF (`fieldValidation`) / query `UPPER()`; DB hanya menjamin unik case-sensitive |