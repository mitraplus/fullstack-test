<p align="center">
  <img src="./logo_mitraplus.webp" alt="Mitraplus" width="45" />
  <img src="./logo_mitraplus_typography.png" alt="Mitraplus" width="200" />
</p>

<h1 align="center">Fullstack Test - Mitraplus</h1>

Repositori ini merupakan tes teknis untuk kandidat posisi Fullstack Developer di Mitraplus. Mohon baca dokumen ini secara menyeluruh sebelum memulai pengerjaan.

## Ruang Lingkup Tes

Kandidat diminta membangun satu modul Master Barang secara utuh, mulai dari API (Express), hingga halaman React yang terhubung langsung ke API tersebut secara nyata, bukan frontend yang mock data sendiri. Tes ini tidak memerlukan database engine, data disimpan sebagai mock/in-memory di sisi backend (lihat file **[data/barang.json](./data/barang.json)** dan **[data/kategori.json](./data/kategori.json)**). Tujuan tes ini adalah untuk menilai kemampuan kandidat dalam mengerjakan alur kerja fullstack secara menyeluruh, khususnya kualitas API dan integrasi dengan frontend.

Spesifikasi lengkap, meliputi skema tabel, kontrak response API, aturan validasi, hingga halaman yang harus dibuat di frontend, tersedia di **[TASK.md](./TASK.md)**. Dokumen README ini hanya berfungsi sebagai ringkasan awal. Acuan resmi tetap mengikuti TASK.md.

## Gambaran Umum

- **Backend:** Node.js dengan Express, data disimpan sebagai array in-memory yang di-load dari file JSON (tidak perlu database engine atau ORM).
- **Frontend:** React, menggunakan MUI atau Tailwind (pilih salah satu), dengan routing bebas menggunakan React Router atau TanStack Router.
- **Modul:** Master Barang, mencakup fitur daftar, tambah, ubah, dan nonaktifkan/hapus. Terdapat relasi ke Master Kategori.
- Detail endpoint, format response, dan aturan validasi dijelaskan pada bagian 3 TASK.md.
- Detail halaman dan alur frontend dijelaskan pada bagian 4 TASK.md.

## Prosedur Pengerjaan

1. Fork repositori ini ke akun GitHub masing-masing.
2. Clone hasil fork tersebut, kemudian buat branch baru dengan nama yang jelas, misalnya `feature/master-barang`.
3. Kerjakan sesuai spesifikasi pada TASK.md. Gunakan pesan commit yang deskriptif dan menjelaskan perubahan yang dilakukan, hindari pesan generik seperti `fix` atau `update` yang berulang.
4. Setelah selesai, buka Pull Request dari repositori fork ke repositori ini (`mitraplus/fullstack-test`, branch `main`). Sertakan pada deskripsi PR: ringkasan pekerjaan, cara menjalankan aplikasi, serta kebutuhan environment yang perlu disiapkan reviewer.
5. Kirimkan tautan PR beserta tautan repositori fork. Pengumpulan dalam bentuk arsip ZIP tidak diterima.

Batas waktu pengerjaan adalah 2 minggu sejak tes ini diberikan.

## Kriteria Penilaian

Penilaian tidak hanya didasarkan pada apakah aplikasi berjalan, tetapi juga mencakup:

- Riwayat commit dan isi Pull Request, apakah alur pengerjaan tergambar jelas atau hanya terdiri dari satu commit besar.
- Backend: fungsi CRUD berjalan, struktur data dan relasi ke kategori benar, input tervalidasi, dan response error tersusun rapi (bukan stack trace mentah).
- Frontend: tabel dan form benar-benar terhubung ke API, disertai loading state dan penanganan error yang memadai.
- README pada proyek hasil pengerjaan kandidat. Reviewer harus dapat menjalankan proyek tersebut hanya dengan mengikuti README yang disediakan.

Rincian bobot penilaian per area tersedia pada bagian 6 TASK.md.

## Pertanyaan Umum

**Apakah boleh menggunakan stack di luar yang disebutkan?**
Stack inti (Node.js/Express, React) bersifat wajib. Untuk pilihan UI library, silakan memilih salah satu opsi yang tersedia, tidak diperkenankan mencampur keduanya. Database engine dan ORM tidak digunakan pada tes ini.

**Apakah tampilan antarmuka harus sempurna secara desain?**
Tidak. Tampilan kustom yang kompleks bukan merupakan kriteria penilaian utama. Hal yang lebih diutamakan adalah fitur berjalan dengan baik, struktur kode rapi, dan API konsisten. Komponen bawaan MUI atau utility class Tailwind standar sudah mencukupi.

---

Selamat mengerjakan.
