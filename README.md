# POSKETREN NJ — Sistem Manajemen Ekspedisi Paket & Barang Pesantren

<div align="center">
  <img src="https://img.shields.io/badge/NestJS-11-E0234E?logo=nestjs" alt="NestJS" />
  <img src="https://img.shields.io/badge/Next.js-16-black?logo=next.js" alt="Next.js" />
  <img src="https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript" alt="TypeScript" />
  <img src="https://img.shields.io/badge/Prisma-5-2D3748?logo=prisma" alt="Prisma" />
  <img src="https://img.shields.io/badge/MySQL-8-4479A1?logo=mysql" alt="MySQL" />
  <img src="https://img.shields.io/badge/TailwindCSS-4-38bdf8?logo=tailwindcss" alt="Tailwind" />
</div>

## 📦 Tentang Aplikasi

**POSKETREN NJ** adalah sistem manajemen ekspedisi paket & barang untuk **Pondok Pesantren Nurul Jadid**. Mengelola alur masuk, distribusi, penyitaan, dan pengembalian paket santri secara digital dengan arsitektur **backend API (NestJS)** dan **frontend SPA (Next.js)**.

### Fitur Utama

| Fitur | Deskripsi |
|---|---|
| 📬 **Paket Reguler** | Input & tracking paket dari kurir ekspedisi |
| 🍱 **Jastip** | Pengelolaan paket titipan makanan/kebutuhan dengan proteksi kedaluwarsa |
| 🔍 **Public Tracking** | Lacak paket tanpa login via ID/resi/NIUP/nama |
| 📷 **Camera Scanner** | Scan barcode/QR resi via kamera HP (html5-qrcode) |
| ✅ **Validasi Serah Terima** | Verifikasi 2-langkah dengan kondisi fisik paket & PIN opsional |
| 📋 **Manifest Ekspedisi** | Manajemen database paket, filter, dan rekap |
| 🚨 **Penyitaan** | Log barang sitaan dengan klasifikasi (elektronik, media, dll) + bukti foto |
| 🔄 **Return (RTS)** | Manajemen paket kedaluwarsa dikembalikan ke kurir |
| 📊 **Dashboard Analytics** | KPI real-time (arus masuk, distribusi, penyitaan, RTS) dari database |
| 👥 **Basis Data Santri** | Manajemen master data santri (NIUP, asrama, wilayah, daerah) |
| 🔐 **Multi-Role RBAC** | Admin / Petugas Pos / Keamanan dengan hak akses berbeda |
| 🔗 **API Sync** | Sinkronisasi data santri dari sistem eksternal dengan mapping JSON |
| 📝 **Activity Logs** | Catat semua aktivitas operasional dengan paginasi |
| 🖼️ **Bukti Foto** | Upload foto bukti serah terima & penyitaan |

## 🔄 Siklus Hidup Barang / Alur Ekspedisi

Berikut adalah alur lengkap sirkulasi paket & barang di lingkungan Pondok Pesantren Nurul Jadid yang dikelola oleh sistem POSKETREN:

### Diagram Alur Status Paket

```
                    ┌──────────────────────────────────┐
                    │      📬 KURIR / EKSPEDISI        │
                    │  (J&T, JNE, SiCepat, Pos, dll)   │
                    └────────────┬─────────────────────┘
                                 │
                                 ▼
              ┌──────────────────────────────────────┐
              │    1. PAKET DATANG DI POS PAKET      │
              │    Petugas Pos mencatat paket baru    │
              │         Status: diterima_petugas      │
              └────────────┬──────────────────────────┘
                           │
          ┌────────────────┼────────────────────┐
          │                │                    │
          ▼                ▼                    ▼
┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
│2a. REGULER       │ │2b. JASTIP        │ │2c. PAKET ANONIM  │
│Via kurir resmi   │ │Makanan/minuman   │ │Tanpa ID jelas    │
│(input_reguler)   │ │(input_jastip)    │ │(diidentifikasi)  │
│Resi otomatis     │ │Proteksi 24 jam   │ │Status: paket_anonim│
└────────┬─────────┘ └────────┬─────────┘ └────────┬─────────┘
         │                    │                    │
         └────────┬───────────┘                    │
                  │                                │
                  ▼                                │
    ┌─────────────────────────────┐                │
    │   3. DISTRIBUSI KE ASRAMA   │                │
    │   Pos Paket → Pengurus      │◄───────────────┘
    │   Status: diterima_pengurus │
    │   (via Validasi Perpindahan)│
    └─────────────┬───────────────┘
                  │
                  │
       ┌──────────┴──────────┐
       │                     │
       ▼                     ▼
┌──────────────────┐ ┌──────────────────┐
│4a. NORMAL        │ │4b. BERMASALAH    │
│                  │ │                  │
│Santri mengambil  │ │● Elektronik      │
│paket di pos/     │ │  terlarang       │
│pengurus asrama   │ │● Media tidak     │
│                  │ │  syar'i          │
│Verifikasi:       │ │● Senjata tajam   │
│• Kondisi fisik   │ │● Makanan luar   │
│  (baik/rusak)    │ │  tanpa izin      │
│• PIN opsional    │ │● dll.            │
│                  │ │                  │
│Status:           │ │Status: DISITA    │
│telah_diantarkan ✓│ │(via Petugas      │
│                  │ │ Keamanan)        │
└────────┬─────────┘ └────────┬─────────┘
         │                   │
         │                   ├────────────────────┐
         │                   │                    │
         │                   ▼                    ▼
         │          ┌──────────────────┐ ┌──────────────────┐
         │          │Dilepaskan ke     │ │Dikembalikan ke   │
         │          │wali santri       │ │kurir asal (RTS)  │
         │          │Status:           │ │Status:           │
         │          │dilepaskan        │ │dikembalikan      │
         │          └──────────────────┘ └──────────────────┘
         │
         │       ┌─────────────────────────────┐
         └──────►│    SELESAI (Telah Diantar)   │
                 │   Sirkulasi paket berakhir   │
                 └─────────────────────────────┘

         ┌─────────────────────────────────────────┐
         │  5. KADALUARSA / EXPIRED                │
         │  Paket tidak diambil > batas waktu      │
         │  → Status: kadaluarsa                   │
         │  → Diproses RTS (Return to Sender)      │
         │  → Status akhir: dikembalikan           │
         └─────────────────────────────────────────┘

           ┌─────────────────────────────────────┐
           │  6. PUBLIC TRACKING (Tanpa Login)    │
           │  Lacak via: ID Paket / No Resi /     │
           │  Nama Santri / NIUP                  │
           └─────────────────────────────────────┘
```

### Penjelasan Setiap Tahapan

#### Tahap 1: Penerimaan di Pos Paket (`diterima_petugas`)
- Kurir ekspedisi mengantarkan paket ke pos pondok
- Petugas Pos mencatat paket melalui form **Paket Reguler** atau **Jastip**
- Sistem otomatis menghasilkan ID paket unik:
  - **Reguler:** `PKG-YYYYMMDD-XXXXXXXX` (8 karakter hex random)
  - **Jastip:** `JSTP-YYMM-NNN` (berurutan per bulan)
- Data dicatat: No Resi, Kurir, Nama Santri, NIUP, Jenis Kelamin, Wilayah, Daerah

#### Tahap 2a: Paket Reguler Masuk
- Via menu **Paket Reguler Masuk** (`PaketMasukRegulerView`)
- Dari kurir resmi (J&T, JNE, SiCepat, Pos Indonesia, dll)
- Bisa scan barcode/QR resi menggunakan kamera HP
- Bisa scan / input massal

#### Tahap 2b: Paket Jastip Masuk
- Via menu **Layanan Jastip Santri** (`PaketMasukJastipView`)
- Untuk makanan/minuman titipan (GoFood, GrabFood, dll)
- Dilengkapi **proteksi kedaluwarsa 24 jam** — ada label peringatan waktu
- Mencatat kategori makanan dan jam kedatangan
- Print label resi khusus jastip

#### Tahap 2c: Paket Anonim (`paket_anonim`)
- Paket tanpa identitas penerima yang jelas atau tidak terdaftar di data pesantren
- Ditandai khusus untuk diidentifikasi lebih lanjut

#### Tahap 3: Distribusi ke Asrama (`diterima_pengurus`)
- Via menu **Validasi & Penyerahan** (`PackageValidationView`)
- Petugas Pos menyalurkan paket ke pengurus asrama
- Bisa menggunakan **Camera Scanner** untuk scan barcode/resi
- Status berubah dari `diterima_petugas` → `diterima_pengurus`
- Opsi menentukan posisi rak/lemari penyimpanan
- Bisa scan massal

#### Tahap 4a: Serah Terima ke Santri (`telah_diantarkan`)
- Santri mengambil paket di pos atau pengurus wilayah/keamanan
- Via menu **Validasi & Penyerahan**
- Proses verifikasi 2 langkah:
  1. **Kondisi Fisik:** Pilih Baik/Utuh atau Rusak/Cacat
  2. **PIN Opsional:** Verifikasi identitas santri (6 digit PIN)
- Nama petugas penyerah dicatat
- Nama penerima barang dicatat
- Waktu serah terima tercatat otomatis (timestamp real-time)

#### Tahap 4b: Penyitaan oleh Keamanan (`disita`)
- Jika paket mengandung barang terlarang sesuai aturan pondok
- Via menu **Log Penyitaan Keamanan** (`ConfiscatedView`)
- Klasifikasi alasan penyitaan:
  - 🔌 **Elektronik Terlarang** (HP, konsol game, dll)
  - 📚 **Media Tidak Syar'i** (buku, novel, majalah)
  - 🔪 **Senjata / Alat Tajam**
  - 🍕 **Makanan Luar Tanpa Izin**
  - 📋 **Pelanggaran Aturan Lainnya**
- Dicatat: Nama Pelanggar, deskripsi barang, bukti foto (upload dari galeri / langsung foto dari kamera)
- Status lanjutan: bisa **dilepaskan** ke wali santri atau **dikembalikan** ke kurir

#### Tahap 5: Return to Sender / RTS (`kadaluarsa` → `dikembalikan`)
- Via menu **Protokol RTS** (`RtsView`)
- Paket yang melebihi batas waktu penyimpanan (kedaluwarsa)
- Petugas memproses pengembalian ke kurir asal
- Dicatat: kurir pengembalian, nomor resi baru RTS, bukti foto serah terima(opsional)
- Status akhir: `dikembalikan`

#### Tahap 6: Public Tracking (Pelacakan Publik)
- Via halaman **Public Tracking** (`PublicTrackingView`)
- Tidak perlu login — bisa diakses siapa saja
- Lacak paket berdasarkan:
  - ID Paket (contoh: `PKG-20260714-AB12CD34`)
  - No Resi Ekspedisi
  - Nama Santri
  - NIUP (Nomor Induk Unik Pesantren)
- Menampilkan kronologi lengkap sirkulasi paket

### Status Paket (Complete State Machine)

| Status | Makna | Dapat Diubah Ke |
|---|---|---|
| `diterima_petugas` | Paket baru tercatat di Pos Paket | `diterima_pengurus`, `disita`, `kadaluarsa`, `dikembalikan`, `paket_anonim` |
| `diterima_pengurus` | Paket sudah di wilayah/pengurus | `telah_diantarkan`, `disita`, `kadaluarsa`, `dikembalikan` |
| `telah_diterima` | ✅ Paket sudah diterima santri | Status akhir (sirkulasi selesai) |
| `disita` | Barang disita keamanan pondok | `dilepaskan`, `dikembalikan` |
| `dilepaskan` | Barang sitaan dikembalikan ke wali | Status akhir |
| `kadaluarsa` | Melebihi batas waktu penyimpanan | `dikembalikan` |
| `dikembalikan` | Dikembalikan ke kurir asal (RTS) | Status akhir |
| `paket_anonim` | Paket tanpa identitas jelas | `diterima_petugas` (jika teridentifikasi) |
| `pos_paket` / `kembali_ke_pos_paket` | Paket kembali ke pos untuk redistribusi | `diterima_pengurus` |

### Alur Khusus Jastip (Makanan)

```
GoFood/GrabFood/Ojek datang
        │
        ▼
  Petugas Pos cek kategori & jam tiba
        │
        ▼
  Registrasi di menu Jastip
  ⚠️ Wajib didistribusikan ≤ 24 jam!
        │
        ▼
  Dilewatkan ke pengurus asrama
        │
        ▼
  Santri mengambil (serah terima)
  Status: telah_diterima
```

### Alur Khusus Penyitaan

```
Keamanan memeriksa paket
        │
        ▼
  Buka barang di hadapan santri
        │
        ▼
  Klasifikasi: Elektronik/Media/Senjata/Makanan/dll
        │
        ▼
  Catat detail barang + foto bukti
  Status: disita
        │
        ├──────────────────┐
        ▼                  ▼
  Dilepaskan ke      Dikembalikan ke
  wali santri        kurir asal (RTS)
  (dilepaskan)       (dikembalikan)
```

## 🛠️ Tech Stack

### Backend (`PosketrenNJ-backend/`)
- **Framework:** NestJS v11, TypeScript
- **ORM:** Prisma v5
- **Database:** MySQL 8
- **Auth:** JWT (httpOnly cookie, 8 jam), bcryptjs (cost-12)
- **Rate Limiting:** In-memory (5 percobaan / 15 menit per IP)
- **Validasi:** Class-validator, class-transformer (via NestJS pipes)

### Frontend (`PosketrenNJ-frontend/`)
- **Framework:** Next.js v16 App Router
- **Styling:** TailwindCSS v4, Framer Motion v12
- **Scanner:** html5-qrcode
- **Icons:** lucide-react
- **HTTP:** fetch API + axios (interceptor 401/403)

## 🏗️ Arsitektur

```
┌─────────────────────────────────────────────────────────────────┐
│                        FRONTEND (Next.js)                        │
│  http://localhost:3000                                            │
│                                                                   │
│  ┌─────────────┐ ┌──────────────┐ ┌───────────────────────────┐  │
│  │ Public       │ │ Login/Auth   │ │ Dashboard & Admin Panel   │  │
│  │ TrackingView │ │ LoginView    │ │ (Sidebar, 10 menu tabs)   │  │
│  └──────┬───────┘ └──────┬───────┘ └─────────────┬─────────────┘  │
│         │                │                       │                │
│         └────────────────┼───────────────────────┘                │
│                          │                                       │
│                   fetch() / axios                                │
│              withCredentials: true                               │
└──────────────────────────┼───────────────────────────────────────┘
                           │
              ┌────────────┴────────────┐
              │   API Proxy (Next.js)    │
              │  next.config.mjs →       │
              │  /api/* → localhost:8080  │
              └────────────┬────────────┘
                           │
┌──────────────────────────┴───────────────────────────────────────┐
│                     BACKEND (NestJS v11)                          │
│  http://localhost:8080/api                                        │
│                                                                   │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────────────────┐  │
│  │ Auth     │ │ Packages │ │ Students │ │ Operators           │  │
│  │ Module   │ │ Module   │ │ Module   │ │ Module              │  │
│  ├──────────┤ ├──────────┤ ├──────────┤ ├────────────────────┤  │
│  │ JWT +    │ │ CRUD +   │ │ CRUD +   │ │ Manajemen User     │  │
│  │ bcrypt   │ │ Upload   │ │ Normalize│ │ (Admin only)       │  │
│  │ Rate     │ │ Proof    │ │          │ │                    │  │
│  │ Limit    │ │ Image    │ │          │ │                    │  │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┬─────────┘  │
│                                                     │            │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐            │            │
│  │Manifests │ │   Logs   │ │Analytics │  ┌─────────┴─────────┐  │
│  │ Module   │ │  Module  │ │ Module   │  │   ApiConfig       │  │
│  ├──────────┤ ├──────────┤ ├──────────┤  │   Module + Cron   │  │
│  │ Tracking  │ │Activity │ │ Dashboard│  │   Sync Scheduler  │  │
│  │ Status   │ │ Logging  │ │ KPI/Stat │  │                   │  │
│  │ Flow     │ │          │ │          │  │                   │  │
│  └──────────┘ └──────────┘ └──────────┘  └───────────────────┘  │
│                                                                   │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │                  Prisma ORM + MySQL 8                     │    │
│  │  Tables: operators, students, packages, activity_logs,    │    │
│  │         manifests, api_config                             │    │
│  └──────────────────────────────────────────────────────────┘    │
└───────────────────────────────────────────────────────────────────┘
```
## 🔐 Keamanan

- ✅ JWT disimpan sebagai `httpOnly` cookie (aman dari XSS)
- ✅ Password di-hash dengan bcryptjs (cost factor 12)
- ✅ Semua query menggunakan Prisma ORM (prepared statements — SQL injection safe)
- ✅ Rate limiting login: 5 percobaan / 15 menit per IP (in-memory)
- ✅ RBAC di layer API (NestJS Guards & Decorators) dan UI
- ✅ Global 401/403 handling di frontend interceptor
- ✅ Validasi input dengan class-validator + NestJS pipes
- ✅ File upload: validasi MIME type & ukuran (max 5MB)

## 👥 Role & Hak Akses

| Role | Dashboard | Input Paket | Validasi | Penyitaan | RTS | Manajemen User | DB Santri | Sinkronisasi |
|---|---|---|---|---|---|---|---|---|
| `admin` | ✅ Full | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `petugaspos` | ✅ | ✅ | ✅ | ❌ | ✅ | ❌ | ❌ | ❌ |
| `keamanan` | ✅ | ❌ | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ |

## 📡 API Endpoints

Semua endpoint berada di bawah prefix `/api` pada port **8080**.

| Method | Endpoint | Auth | Deskripsi |
|---|---|---|---|
| `POST` | `/api/auth/login` | - | Login (rate limited, 5x/15min) |
| `POST` | `/api/auth/logout` | - | Logout (hapus cookie) |
| `GET` | `/api/auth/me` | Any | Cek status login & profile |
| `GET` | `/api/packages` | Any | List semua paket (public readable) |
| `POST` | `/api/packages` | Any | Tambah paket baru (auto-generate ID) |
| `PUT` | `/api/packages/:id` | Any | Update status & data paket |
| `DELETE` | `/api/packages/:id` | Admin | Hapus paket |
| `POST` | `/api/packages/:id/proof` | Any | Upload foto bukti (max 5MB) |
| `DELETE` | `/api/packages/:id/proof` | Any | Hapus foto bukti |
| `GET` | `/api/packages/:id/proof` | Any | Ambil foto bukti |
| `GET` | `/api/students` | Any | List data santri |
| `POST` | `/api/students` | Admin | Tambah santri baru |
| `PUT` | `/api/students/:niup` | Admin | Update data santri |
| `DELETE` | `/api/students/:niup` | Admin | Hapus santri |
| `GET` | `/api/operators` | Admin | List operator |
| `POST` | `/api/operators` | Admin | Tambah operator baru |
| `GET` | `/api/logs` | Any | Activity logs (pagination: `?page=&limit=`) |
| `POST` | `/api/logs` | Any | Tambah log aktivitas |
| `GET` | `/api/analytics` | Any | Dashboard KPI & statistik dari DB |
| `GET` | `/api/config` | Admin | Baca konfigurasi API sync |
| `PUT` | `/api/config` | Admin | Update konfigurasi API sync |
| `GET` | `/api/manifests` | Any | Data manifest paket |
| `POST` | `/api/manifests` | Any | Tambah data manifest |
| `GET` | `/api/cron` | - | Endpoint trigger cron/sync (health check) |

## 📊 Skema Database (ERD) & Relasi

Aplikasi ini menggunakan database MySQL dengan ORM Prisma. Berikut adalah rancangan tabel dan relasi database keras (Foreign Key Constraints) yang diterapkan:

```
erDiagram
    OPERATORS {
        string id PK
        string username UNIQUE
        string password_hash
        string name
        enum role
        string photo
        int login_attempts
        datetime locked_until
        datetime created_at
        datetime updated_at
    }

    STUDENTS {
        string niup PK
        string nama_lengkap
        string jenis_kelamin
        string daerah
        string wilayah
        datetime created_at
        datetime updated_at
    }

    PACKAGES {
        string id PK
        string trackingNumber INDEX
        string recipientId
        string recipientName
        enum asrama
        string wilayah
        string daerah
        enum type
        string status
        string dateReceived
        enum kondisi
        string notes
        string category
        string arrivalTime
        string confiscatedReason
        string confiscatedBy
        string confiscatedNotes
        string confiscatedDate
        string confiscatedEvidenceUrl
        string returnCourier
        string returnTrackingNumber
        string returnProofUrl
        string receiverName
        string receiverTime
        string manifestId FK
        string circulationType
        boolean isExternalRecipient
        string manualRecipientName
        string proof_image
        string proof_image_mimetype
        datetime created_at
        datetime updated_at
    }

    MANIFESTS {
        string id PK
        string packageId FK "UNIQUE"
        string status
        string notes
        datetime created_at
        datetime updated_at
    }

    ACTIVITY_LOGS {
        string id PK
        string type
        string title
        string description
        string timestamp
        string operator
        datetime created_at
    }

    API_CONFIG {
        int id PK
        string baseUrl
        string token
        enum syncFrequency
        string lastSyncTime
        enum lastSyncStatus
        int updatedCount
        json mapping_json
        datetime updated_at
    }

    PACKAGES }o--|o MANIFESTS : "manifestId -> id"
    MANIFESTS |o--|o PACKAGES : "packageId -> id"
```

### Detil Relasi Penting:
1. **Packages to Manifests (`manifestId -> id`)**: Relasi satu-ke-banyak (One-to-Many). Beberapa paket dapat dikelompokkan ke dalam satu manifest ekspedisi pengiriman.
2. **Manifests to Packages (`packageId -> id`)**: Relasi satu-ke-satu (One-to-One) dengan constraint `unique` pada `packageId` di tabel `manifests`. Menghindari race condition pembuatan manifest ganda untuk paket yang sama.
3. **Indexes**: Indeks non-cluster ditambahkan pada kolom `trackingNumber` pada tabel `packages` guna meningkatkan performa query pelacakan paket.

---

## 🔌 Spesifikasi Parameter API (DTO)

Validasi input ketat dilakukan pada tingkat API menggunakan `class-validator` dan `class-transformer` melalui global NestJS `ValidationPipe`.

### 1. Paket DTOs

#### CreatePackageDto (`POST /api/packages`)
Digunakan untuk mendaftarkan paket reguler atau jastip baru.

| Field | Tipe | Validasi | Deskripsi |
|---|---|---|---|
| `id` | `string` | Opsional | ID paket kustom (jika tidak diset, sistem akan men-generate otomatis) |
| `trackingNumber` | `string` | **Wajib** | Nomor resi ekspedisi dari kurir |
| `recipientId` | `string` | **Wajib** | NIUP santri penerima paket |
| `recipientName` | `string` | **Wajib** | Nama lengkap santri penerima |
| `asrama` | `string` | **Wajib** | Pilihan enum: `'Putra'` atau `'Putri'` |
| `wilayah` | `string` | **Wajib** | Wilayah asrama |
| `daerah` | `string` | **Wajib** | Daerah asrama |
| `type` | `string` | Opsional | Pilihan enum: `'regular'` (default) atau `'jastip'` |
| `status` | `string` | Opsional | Status awal paket (default: `'diterima_petugas'`) |
| `dateReceived` | `string` | **Wajib** | Tanggal paket diterima pos |
| `kondisi` | `string` | Opsional | Pilihan enum: `'baik'` (default) atau `'rusak'` |
| `notes` | `string` | Opsional | Catatan tambahan |
| `category` | `string` | Opsional | Kategori barang / makanan (khusus jastip) |
| `arrivalTime` | `string` | Opsional | Waktu kedatangan barang (khusus jastip) |
| `isExternalRecipient` | `boolean` | Opsional | Flag penerima eksternal (bukan santri terdaftar) |
| `manualRecipientName` | `string` | Opsional | Nama penerima manual jika eksternal |

#### UpdatePackageDto (`PUT /api/packages/:id`)
Digunakan untuk meng-update status atau data penyerahan/penyitaan paket.

| Field | Tipe | Validasi | Deskripsi |
|---|---|---|---|
| `status` | `string` | **Wajib** | Status sirkulasi paket baru |
| `kondisi` | `string` | Opsional | Pilihan enum: `'baik'` atau `'rusak'` |
| `confiscatedReason` | `string` | Opsional | Alasan barang disita |
| `confiscatedNotes` | `string` | Opsional | Deskripsi barang yang disita |
| `confiscatedBy` | `string` | Opsional | Nama petugas penyita |
| `confiscatedDate` | `string` | Opsional | Tanggal penyitaan dilakukan |
| `confiscatedEvidenceUrl` | `string` | Opsional | Tautan URL gambar bukti sita |
| `returnCourier` | `string` | Opsional | Nama kurir pengembalian (RTS) |
| `returnTrackingNumber` | `string` | Opsional | Resi pengembalian RTS baru |
| `receiverName` | `string` | Opsional | Nama penerima (saat diserahkan) |
| `receiverTime` | `string` | Opsional | Waktu pengambilan paket |

### 2. Santri DTOs

#### CreateStudentDto (`POST /api/students`)
Digunakan oleh administrator untuk menambahkan santri baru secara manual.

| Field | Tipe | Validasi | Deskripsi |
|---|---|---|---|
| `niup` | `string` | **Wajib** | Nomor Induk Unik Pesantren (Primary Key) |
| `nama_lengkap` | `string` | **Wajib** | Nama lengkap santri |
| `jenis_kelamin` | `string` | **Wajib** | Jenis kelamin santri |
| `daerah` | `string` | **Wajib** | Nama daerah santri |
| `wilayah` | `string` | **Wajib** | Wilayah asrama |

#### UpdateStudentDto (`PUT /api/students/:niup`)
Digunakan untuk memperbarui data biodata santri terdaftar.

| Field | Tipe | Validasi | Deskripsi |
|---|---|---|---|
| `nama_lengkap` | `string` | **Wajib** | Nama lengkap santri baru |
| `jenis_kelamin` | `string` | **Wajib** | Jenis kelamin santri |
| `daerah` | `string` | **Wajib** | Nama daerah santri |
| `wilayah` | `string` | **Wajib** | Wilayah asrama |

---

## 📝 Lisensi

Internal use — Pondok Pesantren Nurul Jadid © 2026

