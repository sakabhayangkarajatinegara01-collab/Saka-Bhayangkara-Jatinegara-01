# Dokumentasi Portal Saka Bhayangkara Jatinegara (CMS Admin)

Dokumen ini dibuat dari file `saka_bhayangkara_jatinegara_portal_admin_cms.html`.

## 1. Ringkasan

Portal satu halaman (single-file HTML) untuk Satuan Karya Pramuka Bhayangkara Ranting Jatinegara. Halaman publik dan panel admin berada dalam satu file. Panel admin dibuka lewat `#admin`, tanpa backend. Semua data disimpan di browser (`localStorage`) dan dapat diekspor menjadi HTML publik.

| Aspek | Detail |
|---|---|
| Bahasa | HTML, JavaScript vanilla, Tailwind CSS (CDN) |
| Ikon / Font | Font Awesome 6.5.1, Google Fonts (Inter, Plus Jakarta Sans) |
| Penyimpanan | `localStorage` key `saka_bhayangkara_jatinegara_cms_v2` |
| Sesi admin | `sessionStorage` key `saka_bhayangkara_admin_auth` |
| Akses admin | URL `#admin` + kode akses |
| Ekspor | Unduh HTML publik bersih (data tertanam di `defaultCMSData`) |
| Ukuran | Sekitar 1.700 baris, sekitar 100 KB |

---

## 2. Struktur Halaman

```mermaid
flowchart TB
    subgraph Publik
        NAV[Navbar dinamis]
        HERO[Hero + 4 kartu statistik]
        MAIN[Container bagian dinamis<br/>about, krida, schedule, social, contact, announcement]
        FOOT[Footer + tautan bagian + Portal Pengurus]
    end
    subgraph "Khusus Admin (#admin + login)"
        TOP[Admin top bar: aksi cepat]
        TOOL[Toolbar per bagian: naik, turun, sembunyikan, edit, hapus]
        BOT[Admin bottom bar: Buat Bagian Baru]
    end
    subgraph Modal
        M1[Login admin]
        M2[Editor Hero & Statistik]
        M3[Tambah / Edit Bagian]
        M4[Konfirmasi hapus / reset]
    end
    TOP --> MAIN
    TOOL --> MAIN
```

| Blok | ID / Elemen | Keterangan |
|---|---|---|
| Admin top bar | `#admin-workspace-bar` | Tombol: Edit Hero & Statistik, Tambah Bagian, toggle Edit Teks Langsung, Ekspor HTML Publik, Reset, Keluar |
| Navbar | `#main-navbar`, `#dynamic-nav-links`, `#mobile-nav-drawer` | Menu dibuat otomatis dari bagian yang tampil (judul dipotong 22 karakter + `...`) |
| Hero | `#hero-badge`, `#hero-title`, `#hero-subtitle`, `#hero-stats-grid` | Teks hero dan 4 kartu statistik |
| Konten | `#cms-sections-container` | Semua bagian dirender dari `cmsState.sections` |
| Admin bottom bar | `#admin-bottom-bar` | Tombol "+ Buat Bagian Baru" |
| Footer | `#footer-nav-links` | Tautan bagian otomatis; info markas dan teks hak cipta bersifat statis |
| Toast | `#toast-container` | Notifikasi sukses, error, atau info |

---

## 3. Model Data (ERD Logis)

Seluruh data berada dalam satu objek `cmsState`.

```mermaid
erDiagram
    CMS ||--|| HERO : "punya 1"
    CMS ||--o{ SECTION : "punya banyak (berurutan)"
    HERO ||--|{ STAT : "4 kartu"
    SECTION ||--o{ ABOUT_CARD : "type=about"
    SECTION ||--o{ KRIDA_ITEM : "type=krida"
    SECTION ||--o{ SCHEDULE_ITEM : "type=schedule"
    SECTION ||--o{ SOCIAL_ITEM : "type=social"

    CMS {
        object hero
        array sections
    }
    HERO {
        string badge
        string title
        string subtitle
    }
    STAT {
        string value
        string label
    }
    SECTION {
        string id "mis. sec-tentang / sec-custom-timestamp"
        string type "about, krida, schedule, social, contact, announcement"
        boolean visible "false = draft"
        string badge
        string title
        string subtitle
        string address "social dan contact"
        string phone "contact"
        string email "contact"
        string date "announcement"
        string content "announcement"
    }
    ABOUT_CARD {
        string icon "kelas Font Awesome"
        string title
        string desc
    }
    KRIDA_ITEM {
        string code "KRIDA 01..04"
        string name
        string icon
        string description
    }
    SCHEDULE_ITEM {
        string day
        string time
        string activity
        string location
    }
    SOCIAL_ITEM {
        string platform "nama kanal"
        string handle "username atau nomor"
        string url
        string icon "kelas Font Awesome"
        string color "gradien Tailwind"
    }
```

### 3.1 Jenis bagian (`section.type`)

| Type | Field khusus | Dapat dibuat baru? | Cara edit |
|---|---|---|---|
| `about` | `cards[]` (icon, title, desc) | Tidak (bawaan) | Modal edit atau inline |
| `krida` | `items[]` (code, name, icon, description) | Tidak (bawaan) | Modal edit atau inline |
| `schedule` | `schedules[]` (day, time, activity, location) | Ya | Modal edit atau inline |
| `social` | `socials[]`, `address` | Ya | Modal edit atau inline |
| `contact` | `phone`, `email`, `address` | Ya | Modal edit atau inline |
| `announcement` | `date`, `content` | Ya | Modal edit atau inline |

### 3.2 Data bawaan (`defaultCMSData`)

| Bagian | `id` | Isi bawaan |
|---|---|---|
| Hero | – | Badge "Pangkalan Polsek Jatinegara • Polres Metro Jakarta Timur"; 4 statistik: 4 Krida, Polsek, Penegak, 24/7 |
| Tentang | `sec-tentang` | 3 kartu: Visi & Misi, Struktur & Pangkalan, Bakti & Pengabdian |
| Krida | `sec-krida` | Tibmas, Lantas, PPB, TPTKP |
| Jadwal | `sec-jadwal` | Sabtu 13.00–17.00 (Latihan Rutin); Minggu tentatif 07.00–11.00 (Bakti Masyarakat) |
| Media sosial | `sec-medsos` | Instagram, WhatsApp, YouTube + alamat sanggar |
| Kontak | `sec-kontak` | Telepon, email, alamat Polsek Jatinegara |

### 3.3 Contoh JSON ringkas

```json
{
  "hero": {
    "badge": "Pangkalan Polsek Jatinegara • Polres Metro Jakarta Timur",
    "title": "…",
    "subtitle": "…",
    "stats": [ { "value": "4 Krida", "label": "Bidang Keterampilan Khusus" } ]
  },
  "sections": [
    {
      "id": "sec-medsos",
      "type": "social",
      "visible": true,
      "badge": "KANAL RESMI",
      "title": "Media Sosial & Dokumentasi",
      "subtitle": "…",
      "socials": [
        {
          "platform": "Instagram Resmi",
          "handle": "@sakabhayangkara_jatinegara",
          "url": "https://instagram.com",
          "icon": "fa-brands fa-instagram",
          "color": "from-pink-600 to-amber-600"
        }
      ],
      "address": "Sanggar Bakti Saka Bhayangkara: …"
    }
  ]
}
```

---

## 4. Alur dan Fitur

### 4.1 Routing & login admin

```mermaid
flowchart TD
    A[Buka halaman] --> B{Hash = #admin?}
    B -- Tidak --> P[Tampilan publik]
    B -- Ya --> C{Sudah login?}
    C -- Ya --> AD[Mode admin aktif]
    C -- Tidak --> L[Modal login]
    L -- kode benar --> AD
    L -- salah --> E[Pesan error]
    L -- Kembali ke Publik --> P
    AD -- Keluar / Lihat Publik --> P
```

- Tautan masuk: **Portal Pengurus** di footer, atau langsung ke `…/index.html#admin`.
- Mode admin aktif jika `hash == #admin` **dan** `isAdminAuthenticated == true`.
- Admin menampilkan semua bagian (termasuk draft). Publik hanya menampilkan bagian dengan `visible !== false`.

### 4.2 Fitur panel admin

| Fitur | Fungsi | Keterangan |
|---|---|---|
| Edit teks langsung | `toggleInlineEdit()`, `attachInlineListeners()` | Klik teks di halaman untuk mengedit (contenteditable); tersimpan otomatis saat kehilangan fokus (`blur`). Default: ON |
| Editor Hero & Statistik | `openHeroModal()`, `handleHeroFormSubmit()` | Badge, judul, subjudul, dan 4 kartu statistik |
| Tambah bagian | `openSectionModal()`, `handleSectionFormSubmit()` | Pilih jenis (Sosial, Jadwal, Kontak, Pengumuman) + judul, badge, subjudul, status |
| Edit bagian | `openEditSectionModal(id)` | Bagian bawaan (about, krida) hanya bisa ubah teks kartu; jenis tidak bisa diganti |
| Urutkan bagian | `moveSection(index, ±1)` | Tukar posisi dengan bagian sebelah |
| Tampil / sembunyikan | `toggleSectionVisibility(id)` | Status DRAFT atau PUBLIK; menu navigasi ikut menyesuaikan |
| Hapus bagian | `confirmDeleteSection(id)` | Memakai dialog konfirmasi |
| Reset | `openResetConfirmModal()` | Mengembalikan `defaultCMSData` |
| Ekspor HTML publik | `exportCleanPublicHTML()` | Lihat 4.3 |

### 4.3 Alur penyimpanan & ekspor

```mermaid
flowchart LR
    D[defaultCMSData<br/>di dalam HTML] -->|boot| S[cmsState]
    LS[(localStorage<br/>saka_bhayangkara_jatinegara_cms_v2)] -->|jika valid, menimpa| S
    ADM[Edit admin / inline] --> S
    S -->|saveCMSState| LS
    S -->|renderApp| UI[Halaman]
    S -->|exportCleanPublicHTML| EX[File HTML publik baru<br/>defaultCMSData diganti isi cmsState]
```

Ekspor bekerja dengan merender tampilan publik sementara (admin dimatikan), mengambil `outerHTML`, lalu mengganti blok `const defaultCMSData = {...};` dengan state terbaru dalam format JSON. File diunduh sebagai `saka-bhayangkara-jatinegara-publik-YYYY-MM-DD.html`.

---

## 5. Referensi Fungsi JavaScript

| Kelompok | Fungsi | Peran |
|---|---|---|
| State | `loadCMSState()`, `saveCMSState(notify)` | Baca dan tulis `localStorage`; validasi `hero` dan `sections` |
| Auth & routing | `isAdminRoute()`, `isInActiveAdminMode()`, `handleHashRouting()`, `navigateToAdmin()`, `handleAdminLogin()`, `cancelAdminLogin()`, `exitAdminMode()` | Gerbang `#admin` |
| Render | `renderApp()`, `renderSectionTemplate()`, `renderNavigation()`, `toggleMobileMenu()` | Menggambar ulang seluruh halaman dari state |
| Inline edit | `toggleInlineEdit()`, `attachInlineListeners()` | Menyimpan hasil edit teks langsung ke state lewat atribut `data-*` |
| Hero | `openHeroModal()`, `closeHeroModal()`, `handleHeroFormSubmit()` | Edit hero dan statistik |
| Bagian | `moveSection()`, `toggleSectionVisibility()`, `confirmDeleteSection()`, `openSectionModal()`, `openEditSectionModal()`, `closeSectionModal()`, `renderBuiltInStructuredFields()`, `renderTemplateFormFields()`, `handleSectionFormSubmit()` | CRUD bagian |
| Konfirmasi | `openResetConfirmModal()`, `closeConfirmModal()` | Dialog umum |
| Ekspor | `exportCleanPublicHTML()` | Unduh HTML publik |
| Util | `showToast()`, `escapeHTML()` | Notifikasi dan pencegahan XSS pada teks |

Atribut `data-*` yang dipakai inline edit:

| Atribut | Arti |
|---|---|
| `data-hero-field` | Field hero (`badge`, `title`, `subtitle`) |
| `data-hero-stat` + `data-stat-field` | Indeks statistik + `value` atau `label` |
| `data-sec-id` + `data-field` | Bagian + field di tingkat bagian |
| `data-array` + `data-idx` | Nama array (`cards`, `items`, `schedules`, `socials`) + indeks elemen |

---

## 6. Catatan Teknis & Keterbatasan

1. **Kode akses ada di sisi klien.** `ADMIN_PASSCODE = 'bhayangkara2026'` terlihat di source, dan `admin123` juga diterima. Pesan error dan kotak info di modal login menampilkan kode akses. Ini hanya penghalang tampilan, bukan keamanan sungguhan.
2. **Template media sosial bersifat tetap.** Form tambah/edit bagian *social* hanya menyediakan 3 kanal (Instagram, WhatsApp, YouTube). Ikon dan warna ditetapkan otomatis. Tidak ada tombol tambah/hapus kanal (misalnya TikTok) lewat modal. Menyimpan bagian ini lewat modal juga menimpa seluruh array `socials` dan mengembalikan nama platform ke default.
3. **Template jadwal terbatas 2 baris.** Form jadwal hanya memuat 2 entri; baris yang kegiatannya kosong diabaikan.
4. **Footer sebagian statis.** Teks deskripsi, alamat markas, "Latihan Rutin: Setiap Sabtu", dan hak cipta tidak ikut data CMS.
5. **Gambar tidak didukung.** Model data tidak memiliki upload logo, foto pengurus, atau galeri; ikon memakai Font Awesome.
6. **Data per-browser.** Perubahan tersimpan di `localStorage` perangkat yang dipakai. Agar permanen untuk semua pengunjung, hasil ekspor harus diunggah ulang (misalnya ke GitHub Pages).
7. **Ketergantungan CDN.** Tailwind, Font Awesome, dan Google Fonts dimuat dari internet; tanpa koneksi tampilan akan rusak.
8. **Nama menu dipotong.** Menu desktop memotong judul bagian lebih dari 24 karakter.

## 7. Cara Pakai Singkat

1. Buka `index.html#admin` dan masukkan kode akses.
2. Ubah teks langsung di halaman, atau pakai **Edit Hero & Statistik** dan **Edit Bagian**.
3. Tambah bagian lewat **Tambah Bagian**, atur urutan dengan tombol panah, dan gunakan **Sembunyikan** untuk draft.
4. Klik **Ekspor HTML Publik**, lalu unggah file hasil unduhan ke hosting.
