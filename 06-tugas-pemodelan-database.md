# Tugas Mandiri: Perancangan ERD E-Library Kampus

**Nama:** Isyraq Awwal Uthorid
**NIM:** D121241075

## 1. Deskripsi Skenario

Sistem E-Library Kampus mengelola proses peminjaman buku perpustakaan. Sistem perlu
menyimpan data mahasiswa sebagai peminjam, data buku beserta penerbitnya, dan riwayat
setiap transaksi peminjaman hingga pengembalian buku.

## 2. Identifikasi Entitas dan Relasi

Berdasarkan skenario, teridentifikasi empat entitas utama:

| Entitas | Deskripsi |
|---|---|
| Mahasiswa | Pihak yang meminjam buku |
| Buku | Koleksi buku yang tersedia di perpustakaan |
| Penerbit | Pihak yang menerbitkan buku |
| Transaksi Peminjaman | Catatan peminjaman dan pengembalian buku oleh mahasiswa |

Relasi antar entitas:

| Relasi | Kardinalitas | Keterangan |
|---|---|---|
| Penerbit - Buku | 1 ke N | Satu penerbit dapat menerbitkan banyak buku, satu buku hanya diterbitkan oleh satu penerbit |
| Mahasiswa - Transaksi Peminjaman | 1 ke N | Satu mahasiswa dapat melakukan banyak transaksi peminjaman |
| Buku - Transaksi Peminjaman | 1 ke N | Satu buku dapat dipinjam berkali-kali pada transaksi yang berbeda |

## 3. Diagram ERD

```mermaid
erDiagram
    PENERBIT ||--o{ BUKU : menerbitkan
    MAHASISWA ||--o{ TRANSAKSI_PEMINJAMAN : melakukan
    BUKU ||--o{ TRANSAKSI_PEMINJAMAN : dipinjam

    PENERBIT {
        int id_penerbit PK
        varchar nama_penerbit
        varchar kota
        varchar no_telepon
        varchar email
    }
    BUKU {
        varchar kode_buku PK
        varchar judul
        varchar pengarang
        int id_penerbit FK
        int tahun_terbit
        varchar isbn
        varchar kategori
        int stok
    }
    MAHASISWA {
        varchar nim PK
        varchar nama
        varchar program_studi
        int angkatan
        varchar email
        varchar no_telepon
    }
    TRANSAKSI_PEMINJAMAN {
        int id_transaksi PK
        varchar nim FK
        varchar kode_buku FK
        date tanggal_pinjam
        date tanggal_jatuh_tempo
        date tanggal_kembali
        varchar status
        int denda
    }
```

## 4. Atribut Entitas, Primary Key, dan Foreign Key

### 4.1 Mahasiswa

| Atribut | Keterangan |
|---|---|
| nim | Primary Key |
| nama | - |
| program_studi | - |
| angkatan | - |
| email | - |
| no_telepon | - |

### 4.2 Penerbit

| Atribut | Keterangan |
|---|---|
| id_penerbit | Primary Key |
| nama_penerbit | - |
| kota | - |
| no_telepon | - |
| email | - |

### 4.3 Buku

| Atribut | Keterangan |
|---|---|
| kode_buku | Primary Key |
| judul | - |
| pengarang | - |
| id_penerbit | Foreign Key -> Penerbit(id_penerbit) |
| tahun_terbit | - |
| isbn | - |
| kategori | - |
| stok | - |

### 4.4 Transaksi Peminjaman

| Atribut | Keterangan |
|---|---|
| id_transaksi | Primary Key |
| nim | Foreign Key -> Mahasiswa(nim) |
| kode_buku | Foreign Key -> Buku(kode_buku) |
| tanggal_pinjam | - |
| tanggal_jatuh_tempo | - |
| tanggal_kembali | - |
| status | - |
| denda | - |

## 5. Simulasi Normalisasi

### 5.1 Unnormalized Form (UNF)

Data mentah dari petugas perpustakaan dicatat sebagai satu tabel peminjaman, di mana satu
mahasiswa dapat mencatat beberapa buku yang dipinjam sekaligus dalam satu baris (atribut
berulang / *repeating group*):

| NIM | Nama | Prodi | Buku Dipinjam |
|---|---|---|---|
| D121001 | Andi | Teknik Informatika | {B001, Basis Data, Penerbit A, 2026-08-01, 2026-08-10}, {B002, Struktur Data, Penerbit B, 2026-08-01, -} |
| D121002 | Budi | Sistem Informasi | {B003, Jaringan Komputer, Penerbit A, 2026-08-03, -} |

Bentuk ini belum memenuhi UNF sebagai basis data relasional karena kolom "Buku Dipinjam"
menyimpan lebih dari satu nilai (multivalued) dalam satu sel.

### 5.2 First Normal Form (1NF)

Repeating group dipecah sehingga setiap baris hanya berisi satu nilai atomik per kolom (satu
baris untuk satu buku yang dipinjam):

| NIM | Nama | Prodi | Kode_Buku | Judul_Buku | Nama_Penerbit | Tanggal_Pinjam | Tanggal_Kembali |
|---|---|---|---|---|---|---|---|
| D121001 | Andi | Teknik Informatika | B001 | Basis Data | Penerbit A | 2026-08-01 | 2026-08-10 |
| D121001 | Andi | Teknik Informatika | B002 | Struktur Data | Penerbit B | 2026-08-01 | - |
| D121002 | Budi | Sistem Informasi | B003 | Jaringan Komputer | Penerbit A | 2026-08-03 | - |

Sudah 1NF, tetapi masih terdapat redundansi: data Nama dan Prodi berulang setiap kali NIM
yang sama meminjam buku, begitu pula data Judul_Buku dan Nama_Penerbit berulang setiap kali
Kode_Buku yang sama muncul.

### 5.3 Second Normal Form (2NF)

Kunci pada tabel 1NF bersifat komposit, yaitu (NIM, Kode_Buku, Tanggal_Pinjam). Atribut
Nama dan Prodi hanya bergantung pada sebagian kunci (NIM saja), bukan pada keseluruhan
kunci komposit -- ini disebut dependensi parsial dan melanggar 2NF. Begitu pula
Judul_Buku dan Nama_Penerbit hanya bergantung pada Kode_Buku.

Untuk memenuhi 2NF, tabel dipecah menjadi tiga:

**Mahasiswa** (NIM, Nama, Prodi)

**Buku** (Kode_Buku, Judul_Buku, Nama_Penerbit)

**Peminjaman** (NIM, Kode_Buku, Tanggal_Pinjam, Tanggal_Kembali)

Setiap atribut non-kunci kini bergantung penuh pada kunci di tabelnya masing-masing.

### 5.4 Third Normal Form (3NF)

Pada tabel Buku hasil 2NF, atribut Nama_Penerbit sebenarnya tidak bergantung langsung pada
Kode_Buku, melainkan bergantung pada penerbit yang menerbitkan buku tersebut (Kode_Buku ->
Id_Penerbit -> Nama_Penerbit). Ini adalah dependensi transitif dan melanggar 3NF.

Untuk memenuhi 3NF, atribut penerbit dipisahkan ke tabel tersendiri dan dihubungkan melalui
foreign key:

**Mahasiswa** (NIM, Nama, Prodi)

**Penerbit** (Id_Penerbit, Nama_Penerbit)

**Buku** (Kode_Buku, Judul_Buku, Id_Penerbit*)

**Transaksi_Peminjaman** (Id_Transaksi, NIM*, Kode_Buku*, Tanggal_Pinjam, Tanggal_Kembali)

*keterangan: atribut bertanda \* adalah foreign key.*

Seluruh tabel kini bebas dari dependensi parsial maupun transitif, sehingga struktur data
telah memenuhi 3NF dan siap dijadikan rancangan tabel akhir pada bagian 6.

## 6. Rancangan Tabel Akhir

### 6.1 Tabel Mahasiswa

| Kolom | Tipe Data | Keterangan |
|---|---|---|
| nim | VARCHAR(15) | Primary Key |
| nama | VARCHAR(100) | NOT NULL |
| program_studi | VARCHAR(50) | NOT NULL |
| angkatan | SMALLINT | NOT NULL |
| email | VARCHAR(100) | UNIQUE |
| no_telepon | VARCHAR(15) | - |

### 6.2 Tabel Penerbit

| Kolom | Tipe Data | Keterangan |
|---|---|---|
| id_penerbit | INT | Primary Key, AUTO_INCREMENT |
| nama_penerbit | VARCHAR(100) | NOT NULL |
| kota | VARCHAR(50) | - |
| no_telepon | VARCHAR(15) | - |
| email | VARCHAR(100) | - |

### 6.3 Tabel Buku

| Kolom | Tipe Data | Keterangan |
|---|---|---|
| kode_buku | VARCHAR(10) | Primary Key |
| judul | VARCHAR(150) | NOT NULL |
| pengarang | VARCHAR(100) | NOT NULL |
| id_penerbit | INT | Foreign Key -> penerbit(id_penerbit) |
| tahun_terbit | SMALLINT | - |
| isbn | VARCHAR(20) | UNIQUE |
| kategori | VARCHAR(50) | - |
| stok | INT | NOT NULL, DEFAULT 0 |

### 6.4 Tabel Transaksi_Peminjaman

| Kolom | Tipe Data | Keterangan |
|---|---|---|
| id_transaksi | INT | Primary Key, AUTO_INCREMENT |
| nim | VARCHAR(15) | Foreign Key -> mahasiswa(nim) |
| kode_buku | VARCHAR(10) | Foreign Key -> buku(kode_buku) |
| tanggal_pinjam | DATE | NOT NULL |
| tanggal_jatuh_tempo | DATE | NOT NULL |
| tanggal_kembali | DATE | NULL (diisi saat buku dikembalikan) |
| status | ENUM('dipinjam','dikembalikan','terlambat') | NOT NULL, DEFAULT 'dipinjam' |
| denda | INT | DEFAULT 0 |
