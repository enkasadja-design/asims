# KoSIMS - Kontrol Sistem Informasi Manajemen Sekolah

Aplikasi berbasis web modern multi-fungsi untuk mengelola data akademik, kesiswaan, kepegawaian (HRIS & penggajian), serta tata kelola keuangan sekolah (RKAS, tagihan rutin SPP, dan tagihan non-rutin).

---

## 🛠️ Tech Stack Rekomendasi

* **Database:** PostgreSQL
* **Backend:** Next.js (App Router) atau Laravel
* **Frontend:** React / Tailwind CSS + shadcn/ui
* **ORM:** Prisma Client
* **Cetak Raport & Kuitansi:** React-PDF / Puppeteer / Dompdf
* **Penyimpanan Berkas:** Cloudflare R2 / AWS S3

---

## 🏗️ Alur Arsitektur & Relasi Data

1. **Autentikasi & RBAC (`User`):**
   * Multi-peran: *Superadmin, Yayasan, Kepala Sekolah, Bendahara, Tata Usaha, Guru, Siswa, Orang Tua*.
2. **Akademik & Rombel (`AcademicYear`, `Class`, `Student`, `Employee`):**
   * Penugasan wali kelas dari tabel `Employee`.
   * Pendaftaran siswa ke kelas via tabel perantara `StudentEnrollment`.
   * Guru mengajar mata pelajaran tertentu di kelas lewat `TeachingLoad` (perhitungan jam ajar per minggu).
3. **Penggajian Pegawai (`Payroll`):**
   * Mengakumulasi gaji pokok, tunjangan, potongan kasbon, dan variabel honor jam mengajar dari `TeachingLoad`.
4. **Keuangan & Kasir (`FeeCategory`, `StudentBill`, `Transaction`, `CashLedger`):**
   * **Tagihan Rutin:** SPP bulanan terikat atribut `billMonth` dan `billYear`.
   * **Tagihan Non-Rutin:** Biaya kegiatan insidental (PKL, Seragam, Ujikom) mendukung status cicilan.
   * **Multi-Item Payment:** 1 transaksi loket (`Transaction`) dapat melunasi beberapa tagihan siswa sekaligus via `PaymentItem`.
   * **Otomasi BKU:** Setiap transaksi kasir otomatis menambahkan catatan kas masuk ke Buku Kas Umum (`CashLedger`) dan memperbarui realisasi target pada `BudgetPlan` (RKAS).

---
   
 ##  Alur Kerja (Workflow) Sistem Input Nilai dan Cetak Raport
 1. **Guru Mata Pelajaran Input Nilai**
   *Guru login ke aplikasi -> sistem memfilter jadwal ajar miliknya berdasarkan tabel TeachingLoad.*
   *Guru memilih kelas yang diajar (misal: XII-TKR-A) dan mapelnya (misal: Basis Data).*
   *Sistem menampilkan daftar seluruh siswa di kelas tersebut.*
   *Guru menginput nilai tugas, UTS, UAS, dan deskripsi capaian capaian kompetensi. Data tersimpan ke StudentGrade.*
 2. **Rekapitulasi oleh Wali Kelas**
   *Guru yang berstatus wali kelas (Class.homeroomTeacherId) membuka menu Manajemen Kelas / Rapor.*
   *Wali kelas melihat rekap nilai seluruh mapel yang sudah diisi oleh masing-masing guru mapel. Jika ada guru mapel yang belum selesai input, sistem memberi tanda peringatan.*
   *Wali kelas menginput rekap kehadiran siswa (sakit, izin, alfa), ekstrakurikuler yang diikuti siswa, prestasi siswa (jika ada), kegiatan prakerin siswa (kelas 12) serta catatan/pesan motivasi ke tabel ReportCard.*
 3. **Validasi & Cetak PDF**
   *Wali kelas mengaktifkan status isPublished = true (mengunci nilai agar tidak bisa diedit sepihak lagi oleh guru mapel).*
   *Tombol "Cetak Rapor" aktif. Sistem mengeksekusi template PDF (menggabungkan data identitas sekolah, nilai per mapel dari StudentGrade, serta catatan wali kelas & absensi dari ReportCard) dan mengunduh berkas rapor resmi.*

---

## 📋 Prasyarat Sistem

* Node.js versi 18.x atau lebih baru
* PostgreSQL aktif
* Git

---

## 🚀 Panduan Setup & Instalasi

### 1. Kloning Repositori & Instal Dependensi

```bash
git clone <https://github.com/enkasadja-design/KoSIMS>
cd nama-project
npm install
