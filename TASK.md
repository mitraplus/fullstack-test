<p align="center">
  <span style="display:inline-flex; align-items:center; justify-content:center; gap:10px;">
    <img src="./logo_mitraplus.webp" alt="Mitraplus Logo" width="45" />
    <img src="./logo_mitraplus_typography.png" alt="Mitraplus Typography" width="220" />
  </span>
</p>

<h1 align="center">Test Fullstack Mitraplus</h1>


Tugas yang harus diselesaikan: modul **Master Barang** dari database, API (Express + Sequelize), sampai React yang **memanggil API** (bukan mock data). Baca dokumen ini sampai habis sebelum mulai mengerjakan.

| Aspek | Teknologi |
|--------|-----------|
| Stack | Node.js (Express) · React · GitHub |
| Database | ORM - Sequelize, **MSSQL atau PostgreSQL** (pilih salah satu) |
| UI | **MUI (Material UI)** atau **Tailwind CSS** (pilih salah satu) |
| Modul demo | Master Barang — CRUD lengkap |
| Sifat dokumen | Dibagikan kepada rekrutan |

---

## 1. Ringkasan peran

Kami mencari **fullstack**: API REST dengan Node (Express), database MSSQL atau PostgreSQL lewat Sequelize, dan React di depan yang benar-benar berkomunikasi dengan API. Alur kerja melalui **GitHub**: branch sendiri, lalu PR.

| Area | Yang kami lihat |
|------|------------------|
| **GitHub** | Branch jelas, commit bermakna, PR ada konteks |
| **Backend (Node + Express)** | CRUD jalan, input divalidasi, error tidak kacau |
| **Database (Sequelize)** | Model + migrasi/sync, query aman (bukan string mentah sembarangan) |
| **Frontend (React)** | Tabel + form, CRUD nyambung ke API sungguhan |
| **CRUD Master Barang** | Satu modul utuh: daftar, tambah, ubah, nonaktif/hapus |

---

## 2. GitHub

### 2.1 Wajib

- Fork repo `mitraplus/fullstack-test` ke akun GitHub Anda.
- Clone repo hasil fork, lalu buat **branch fitur** dengan nama yang jelas (mis. `feature/master-barang`).
- **Pesan commit**: jelaskan apa yang berubah dan mengapa — hindari satu kata kosong seperti `fix` atau `update` berulang.
- **Pull Request** dari fork ke repo utama (`mitraplus/fullstack-test`, branch `main`): jelaskan tujuan, cara jalankan aplikasi, env atau migrasi baru bila ada.
- **`.gitignore`** wajib mencakup `.env`, `node_modules/`, hasil build. **Jangan** commit data rahasia.

### 2.2 Struktur branch

```
mitraplus/fullstack-test:main                     ← branch utama (protected)
  └── (fork) kandidat/fullstack-test:main         ← repo fork kandidat
        └── feature/master-barang                 ← branch kerja rekrutan
              └── PR → mitraplus/fullstack-test:main (deskripsi lengkap)
```

Kirim **link PR**, bukan ZIP. Sertakan juga **link repository fork** Anda. Kami akan membaca diff, riwayat commit, dan isi deskripsi PR.

---

## 3. Backend — Node.js · Express · Sequelize

### 3.1 Skema tabel: `m_barang`

Buat tabel `m_barang` dengan kolom di bawah lewat model Sequelize (plus **migrasi atau sync** — terserah, asal jelas di README).

| Kolom | Tipe data | Constraint | Keterangan |
|-------|-----------|------------|------------|
| `id` | INTEGER / BIGINT | PK, auto increment | Primary key |
| `kode_barang` | VARCHAR(50) | UNIQUE, NOT NULL | Kode unik barang |
| `nama_barang` | VARCHAR(200) | NOT NULL | Nama lengkap barang |
| `id_kategori` | INTEGER | FK → `m_kategori.id`, NOT NULL | Relasi ke master kategori |
| `satuan` | VARCHAR(30) | NOT NULL | Contoh: pcs, kg, liter, box |
| `harga_beli` | DECIMAL(18,2) | NOT NULL, DEFAULT 0 | Harga pembelian |
| `harga_jual` | DECIMAL(18,2) | NOT NULL, DEFAULT 0 | Harga penjualan |
| `is_aktif` | BOOLEAN / TINYINT(1) | NOT NULL, DEFAULT true | Status aktif/nonaktif |
| `created_at` | TIMESTAMP | NOT NULL | Otomatis Sequelize |
| `updated_at` | TIMESTAMP | NOT NULL | Otomatis Sequelize |

**`m_kategori`**: cukup sederhana — `id`, `nama_kategori`. Sediakan **seed minimal tiga kategori** agar dropdown di frontend bisa dicoba.

### 3.2 Endpoint REST API — `/api/barang`

Semua rute memakai prefix `/api`. Balasan JSON memakai **envelope yang sama** di semua endpoint (lihat 3.3).

| Method | Endpoint | Deskripsi | Status sukses |
|--------|----------|-----------|---------------|
| GET | `/api/barang` | Daftar barang (+ filter & paginasi) | 200 |
| GET | `/api/barang/:id` | Detail satu barang | 200 |
| POST | `/api/barang` | Tambah barang baru | 201 |
| PUT | `/api/barang/:id` | Ubah data barang | 200 |
| DELETE | `/api/barang/:id` | Nonaktifkan barang (soft delete) | 200 |
| GET | `/api/kategori` | Daftar kategori untuk dropdown | 200 |

### 3.3 Kontrak response JSON

Satu pola response untuk semua endpoint.

**Sukses — list (`GET /api/barang`)**

```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "kode_barang": "BRG-001",
      "nama_barang": "Kertas A4 80gsm",
      "kategori": { "id": 2, "nama_kategori": "Alat Tulis" },
      "satuan": "rim",
      "harga_beli": 45000.0,
      "harga_jual": 55000.0,
      "is_aktif": true,
      "created_at": "2026-01-10T08:00:00.000Z",
      "updated_at": "2026-01-10T08:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 42,
    "totalPages": 5
  }
}
```

**Sukses — single / mutasi (POST, PUT, DELETE)**

```json
{
  "success": true,
  "message": "Barang berhasil disimpan.",
  "data": { }
}
```

**Error — validasi (400)**

```json
{
  "success": false,
  "message": "Validasi gagal.",
  "errors": [
    { "field": "kode_barang", "message": "Kode barang sudah digunakan." },
    { "field": "harga_jual", "message": "Harga jual harus lebih dari 0." }
  ]
}
```

**Error — not found (404) & server (500)**

```json
{ "success": false, "message": "Barang tidak ditemukan." }
```

```json
{ "success": false, "message": "Terjadi kesalahan pada server." }
```

### 3.4 Aturan validasi backend

| Field | Validasi wajib |
|-------|----------------|
| `kode_barang` | Required, unik (cek DB), max 50 karakter, hanya alfanumerik + tanda hubung |
| `nama_barang` | Required, max 200 karakter |
| `id_kategori` | Required, harus ada di `m_kategori` |
| `satuan` | Required, max 30 karakter |
| `harga_beli` | Required, angka ≥ 0 |
| `harga_jual` | Required, angka ≥ `harga_beli` |
| `is_aktif` | Boolean, default `true` pada create |

### 3.5 Query parameter — `GET /api/barang`

| Parameter | Tipe | Contoh | Keterangan |
|-----------|------|--------|------------|
| `page` | integer | `1` | Halaman saat ini (default: 1) |
| `limit` | integer | `10` | Per halaman (default: 10, max: 100) |
| `search` | string | `kertas` | Cari di `kode_barang` atau `nama_barang` (LIKE) |
| `is_aktif` | boolean | `true` / `false` | Filter status (opsional) |
| `id_kategori` | integer | `2` | Filter per kategori (opsional) |

---

## 4. Frontend — React · MUI atau Tailwind

### 4.1 Struktur halaman Master Barang

Satu modul: halaman **daftar** + **form** tambah/ubah. Routing: **React Router** atau **TanStack Router**.

**Halaman daftar — `/master/barang`**

- Tabel: No, Kode, Nama, Kategori, Satuan, Harga Beli/Jual, Status (chip), Aksi (Ubah / Hapus).
- Pencarian di atas tabel — **debounce minimal 300 ms** baru panggil API.
- Filter kategori dan status (aktif / semua).
- Paginasi mengikuti field API (`page`, `totalPages`).
- Tombol **Tambah Barang** ke halaman form.
- Loading saat fetch; **empty state** jika kosong.
- Jika API error, beri tahu user (toast atau alert).

**Halaman form — `/master/barang/tambah` & `/master/barang/:id/ubah`**

- Field: Kode, Nama, Kategori (dropdown dari `GET /api/kategori`), Satuan, Harga Beli/Jual, Status aktif (toggle).
- Sebelum kirim: required, angka wajar, harga jual tidak di bawah harga beli. Error per field di bawah input.
- Mode ubah: isi form dari `GET /api/barang/:id`.
- Sukses simpan → kembali ke daftar + notifikasi singkat (toast).
- Error server (mis. kode bentrok) di field atau toast.
- Batal → kembali ke daftar tanpa simpan.

### 4.2 Kolom tabel yang ditampilkan

| Kolom UI | Source field API | Keterangan |
|----------|------------------|------------|
| No | index | Nomor urut pada halaman saat ini |
| Kode Barang | `kode_barang` | Monospace / badge |
| Nama Barang | `nama_barang` | — |
| Kategori | `kategori.nama_kategori` | Dari relasi include |
| Satuan | `satuan` | — |
| Harga Beli | `harga_beli` | Format Rupiah (Rp X.XXX) |
| Harga Jual | `harga_jual` | Format Rupiah |
| Status | `is_aktif` | Aktif vs nonaktif (warna jelas) |
| Aksi | — | Ubah + konfirmasi Hapus/Nonaktifkan |

### 4.3 Alur CRUD di frontend

| Operasi | Trigger | API | Hasil di UI |
|---------|---------|-----|-------------|
| Create | Submit form tambah | `POST /api/barang` | Redirect daftar + toast sukses |
| Read (list) | Buka halaman / ganti filter | `GET /api/barang?page=&search=` | Tabel ter-refresh |
| Read (detail) | Klik Ubah | `GET /api/barang/:id` | Form terisi |
| Update | Submit form ubah | `PUT /api/barang/:id` | Redirect daftar + toast |
| Delete / nonaktif | Hapus + konfirmasi | `DELETE /api/barang/:id` | Baris hilang / status berubah |

### 4.4 Nilai tambah frontend

- **TanStack Query** untuk cache dan invalidate setelah simpan/hapus.
- **TypeScript** untuk model data dan response API.
- Tabel/form dirancang agar bisa dipakai ulang di modul lain jika memungkinkan.
- Layar kecil: tabel boleh scroll horizontal.

---

## 5. Alur yang harus jalan

Checklist dari nol sampai PR. Reviewer harus bisa mengulang hanya mengikuti **README Anda** — tanpa langkah rahasia.

1. **Setup** — Clone → `npm install` → salin `.env.example` → migrasi/sync Sequelize → seed kategori → jalankan BE dan FE. Semua langkah ini harus ada di README.
2. **Daftar barang** — Buka `/master/barang` → FE memanggil `GET /api/barang?page=1&limit=10` → tabel + paginasi; status terlihat (chip atau sejenisnya).
3. **Cari & filter** — Ketik di search (mis. `kertas`) → setelah debounce, request dengan `search` → tabel update. Filter kategori kirim `id_kategori`.
4. **Tambah** — Form baru → `POST /api/barang` → jika lolos validasi, **201** → kembali ke daftar + notifikasi sukses.
5. **Error duplikat** — `POST` lagi dengan `kode_barang` sama → BE **400** + `errors` → FE tunjukkan di field kode.
6. **Ubah** — Load `GET /api/barang/:id` → simpan `PUT` → sukses kembali ke daftar.
7. **Nonaktif / hapus** — Konfirmasi → `DELETE /api/barang/:id` (soft delete `is_aktif=false`) → UI mencerminkan status baru.
8. **PR** — Push branch ke fork → buka PR dari fork ke `mitraplus/fullstack-test:main` → deskripsi singkat + cara tes + kebutuhan env + screenshot daftar & form.

---

## 6. Matriks penilaian

Total **100 poin**; angka per blok dapat disesuaikan secara internal.

| Area | Poin | Kriteria minimum | Kriteria unggul |
|------|------|------------------|-----------------|
| GitHub (PR & commit) | 15 | Branch terpisah, PR ada deskripsi, tidak ada secret ter-commit | Commit atomik & bermakna, README lengkap, CI/Actions jalan |
| Backend (Express + Sequelize) | 30 | 6 endpoint jalan, validasi dasar, error code benar | Validasi lengkap, envelope konsisten, query efisien, transaksi |
| Database (schema & relasi) | 15 | `m_barang` sesuai skema, FK ke kategori | Migrasi terstruktur, seed, index pada `kode_barang` |
| Frontend (React + UI) | 25 | Tabel + form terhubung API, loading & error state ada | React Query, TypeScript, komponen reusable, UX halus |
| Alur CRUD (end-to-end) | 15 | Create, read, update, delete/nonaktif tanpa error | Validasi sinkron FE-BE, konfirmasi delete, toast feedback |

---

## 7. Stack & cara kirim tugas

### 7.1 Stack wajib

| Layer | Teknologi | Catatan |
|-------|-----------|---------|
| Runtime | Node.js ≥ 18 | — |
| Framework | Express.js | Wajib |
| ORM | Sequelize v6+ | Wajib; raw query diperbolehkan sebagai pelengkap |
| Database | MSSQL atau PostgreSQL | Pilih salah satu |
| Frontend | React (Vite atau CRA) | Wajib |
| UI | MUI v5+ atau Tailwind CSS v3+ | Pilih salah satu |
| Routing | React Router v6+ atau TanStack Router | — |
| HTTP client | Axios atau Fetch API | — |

### 7.2 Ketentuan submission

- Kirim **link PR** dari fork ke repo utama (`mitraplus/fullstack-test`) — **bukan** arsip ZIP.
- Sertakan **link repository fork** Anda.
- Target merge: `main` repo utama dari branch kerja di fork (mis. `feature/master-barang`).
- **README wajib**: install, env, migrasi, seed, cara jalankan BE/FE; lampirkan `.env.example` dengan nilai palsu; **screenshot** daftar + form.
- `.env` asli jangan masuk git.
- Reviewer harus bisa menjalankan proyek **hanya dari README**.
- Batas waktu adalah 2 minggu, mulai saat task diberikan.

### 7.3 Yang tidak jadi fokus

- Desain kemasan (animasi fancy, tema custom) **tidak** menentukan lulus. Yang penting: fitur jalan, kode rapi, API konsisten. Komponen MUI atau utilitas Tailwind sudah cukup — tidak perlu CSS kustom berlapis.

---

*Good luck*
