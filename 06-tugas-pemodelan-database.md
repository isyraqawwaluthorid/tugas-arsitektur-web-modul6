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
