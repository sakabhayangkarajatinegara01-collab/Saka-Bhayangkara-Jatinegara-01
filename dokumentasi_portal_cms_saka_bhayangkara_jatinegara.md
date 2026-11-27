# Portal Resmi & Sistem Manajemen Konten (CMS) — Saka Bhayangkara Jatinegara

Dokumentasi teknis dan panduan penggunaan untuk aplikasi web satu file (*single-file web application*) **Saka Bhayangkara Ranting Jatinegara**.

---

## 1. Gambaran Umum Sistem

Aplikasi ini menggabungkan halaman profil publik **Saka Bhayangkara Ranting Jatinegara** dengan **Sistem Manajemen Konten (CMS) Dinamis** berbasis peramban (*browser-based*). Seluruh struktur HTML, gaya CSS (Tailwind CSS), dan logika JavaScript terintegrasi dalam satu file mandiri (`index.html`).

### Identitas & Tema Visual
* **Tema Utama:** Formal, Taktis, dan Profesional (*Slate-900* `#0f172a` dipadukan dengan *Amber-700* `#b45309`).
* **Tipografi:** `Plus Jakarta Sans` untuk judul (*heading*) dan `Inter` untuk teks isi (*body*).
* **Responsivitas:** Mendukung tampilan perangkat seluler (*mobile*), tablet, hingga layar desktop.

---

## 2. Fitur Utama CMS & Manajemen Bagian

Pengelola dapat menekan tombol **Mode Edit / Kelola Bagian** pada bilah navigasi atas untuk mengaktifkan fitur-fitur berikut:

1. **Pengeditan Teks Langsung (*Inline Editing*)**
   * Klik langsung pada judul *Hero*, subjudul, *badge*, judul bagian, maupun isi kartu (*card*) yang ditandai garis putus-putus untuk mengubah teks secara *real-time*.
2. **Pembuat Bagian Kustom (*Custom Section Builder*)**
   * Menambahkan bagian halaman baru melalui tombol **Tambah Bagian Baru** dengan 4 pilihan templat siap pakai:
     * **Media Sosial:** Menampilkan kartu akun Instagram, WhatsApp, YouTube, dan alamat sekretariat.
     * **Jadwal Latihan:** Menampilkan hari, jam pelaksanaan, materi kegiatan, dan lokasi latihan.
     * **Kontak & Markas:** Menampilkan nomor telepon/WhatsApp, email resmi, dan lokasi markas.
     * **Pengumuman / Kustom:** Blok pengumuman terbuka dengan tanggal dan isi informasi.
3. **Pengaturan Urutan & Penghapusan Bagian**
   * Setiap bagian memiliki kontrol untuk digeser ke atas/bawah (`Geser ke Atas` / `Geser ke Bawah`), diedit detailnya (`Edit Detail`), atau dihapus (`Hapus`).
   * Tautan pada **Navbar** dan **Footer** otomatis menyesuaikan dengan daftar bagian yang aktif.
4. **Penyimpanan Otomatis & Ekspor HTML Mandiri**
   * **`localStorage` Persistence:** Setiap perubahan tersimpan otomatis di peramban pengguna menggunakan kunci `saka_bhayangkara_jatinegara_cms_v1`.
   * **Ekspor HTML:** Mengunduh salinan file `.html` baru yang sudah menyematkan seluruh perubahan konten terbaru sebagai data bawaan (*default*).
   * **Reset Default:** Mengembalikan seluruh konten dan struktur bagian ke pengaturan awal.

---

## 3. Dokumentasi Struktur Entitas Basis Data (ERD)

Portal ini juga memuat dokumentasi rancangan entitas basis data untuk pengelolaan administrasi Saka Bhayangkara Jatinegara:

| Nama Entitas | Primary Key (PK) | Atribut Utama | Keterangan |
| :--- | :--- | :--- | :--- |
| **`ANGGOTA`** | `id_anggota` | `nta_pramuka`, `nama_lengkap`, `pangkalan_sekolah`, `id_krida` (FK), `status_aktif` | Menyimpan data anggota Penegak/Pandega aktif. |
| **`KRIDA`** | `id_krida` | `nama_krida`, `kode_krida`, `deskripsi_kompetensi`, `pamong_pembina` | Menyimpan 4 Krida utama (Tibmas, Lantas, PPB, TPTKP). |
| **`KEGIATAN_LATIHAN`** | `id_kegiatan` | `judul_kegiatan`, `tanggal_pelaksanaan`, `lokasi`, `jenis_giat` | Mencatat jadwal latihan rutin dan kegiatan bakti. |
| **`PRESENSI_TKK`** | `id_presensi` | `id_anggota` (FK), `id_kegiatan` (FK), `status_hadir`, `nilai_evaluasi` | Mencatat kehadiran dan evaluasi pencapaian TKK. |

---

## 4. Panduan Penggunaan Singkat

1. **Membuka Aplikasi:** Buka file `index.html` menggunakan peramban modern (Chrome, Edge, Firefox, atau Safari).
2. **Mengubah Konten:**
   * Klik **Mode Edit / Kelola Bagian** di pojok kanan atas.
   * Edit teks secara langsung atau klik **Tambah Bagian Baru** untuk menambahkan informasi seperti media sosial, jadwal latihan, atau pengumuman.
   * Klik **Selesai Edit (Simpan)** untuk kembali ke tampilan publik.
3. **Memublikasikan Perubahan Permanen:**
   * Saat berada di **Mode Edit**, klik **Ekspor HTML** pada panel kontrol atas untuk mengunduh file HTML terbaru yang siap diunggah ke *hosting* atau dibagikan.