---
name: dit-tolongin-dit
description: Audit & testing aplikasi per grup menu/modul secara bertahap (rencana uji, test alur ujung ke ujung, invarian, kasus tepi, hak akses, cetakan, laporan silang, dokumen temuan, keputusan bisnis K-xx). Pakai saat user minta "testing grup X", "audit menu X", "uji modul X putaran N", "cari bug di X", atau "dit-tolongin-dit". Use for structured QA/audit rounds of an app module, not for quick checks of a small change.
---

# Dit, Tolongin Dit — audit & testing per grup menu

Cara kerja audit bertahap untuk aplikasi bisnis (ERP, kasir, koperasi, inventori, dsb.) dan aplikasi web lain.
Tujuannya menemukan bug dengan bukti test, melaporkannya rapi, lalu memperbaiki hanya yang diputuskan user.

## 0. Mulai dari sini

1. Cari template khusus project: `docs/TEMPLATE_TESTING.md` (atau nama serupa yang disebut `CLAUDE.md`/`AGENTS.md`).
   - **Ada** → baca seluruhnya dan ikuti. Template project **mengalahkan** skill ini bila berbeda
     (database test, perintah test, invarian, peta grup menu, prefix temuan, helper test).
   - **Belum ada** → kenali project dulu (framework, database produksi, cara menjalankan test, daftar menu/route,
     modul yang menyentuh uang/stok/data sensitif, helper test yang sudah ada), lalu buat
     `docs/TEMPLATE_TESTING.md` dari `references/template-project.md`. **Tunjukkan ke user dan tunggu persetujuan**
     sebelum mulai menguji.
2. Baca dokumen audit sebelumnya untuk grup itu (mis. `docs/audit-bug-<grup>.md`) dan backlog kalau ada.
   Putaran baru melanjutkan nomor temuan (S12, bukan mulai dari S1).
3. Ikuti bahasa user dan pakai istilah menu sesuai yang tampil di aplikasi.

## 1. Aturan kerja

1. **Langkah 0–10 = uji, catat, lapor, lalu BERHENTI.** Jangan mengubah kode aplikasi selama pengujian.
   Perbaikan (langkah 11+) hanya setelah user memerintahkan atau memilih opsi.
2. **Aturan bisnis diputuskan user.** Tulis sebagai keputusan `K-<prefix><n>` dengan opsi (a)/(b)/(c), dampak
   tiap opsi, dan rekomendasi. Jangan memilih sendiri.
3. **Setiap bug punya bukti test** yang bisa dijalankan ulang. Setiap bug yang diperbaiki punya test regresi yang
   namanya memuat ID temuan, mis. `test_s12_mutasi_fifo_tidak_mengubah_nilai_stok`.
4. **Uji di database yang sama jenisnya dengan produksi** (mis. PostgreSQL/MySQL), bukan cukup SQLite in-memory.
   Hasil yang berbeda antara keduanya hampir selalu berarti bug khusus database.
5. **Database test harus unik dan bukan database aplikasi.** Test yang me-refresh database menghapus isinya.
   Bisa ada sesi lain yang berjalan bersamaan.
6. **Repo bisa dipakai beberapa sesi/agen sekaligus.** Jangan ikut meng-commit perubahan orang lain. Kalau perlu,
   uji di git worktree terpisah yang hanya berisi perubahan sendiri. Commit/push hanya kalau user meminta.
7. **Kesalahan yang menyangkut uang, stok, jurnal, atau kebocoran data wajib dilaporkan** walau kecil
   (selisih pembulatan, akun salah, tanggal salah, data cabang lain terlihat).
8. Ikuti aturan coding project (mis. `.agents/rules/`, `CONTRIBUTING.md`) saat memperbaiki.

## 2. Alur satu putaran (per grup menu)

| Langkah | Isi | Hasil |
|---|---|---|
| 0. Persiapan | Baca template project, audit grup sebelumnya, backlog, `git log` grup itu. Siapkan database test unik. Jalankan test grup yang ada sebagai **baseline**. | Baseline lulus/gagal |
| 1. Baca kode | Controller/komponen, form, service/repository, model, job, dan semua pemanggilnya. Tulis **dugaan** D1, D2, ... (dengan `file:baris`) yang wajib dibuktikan. | Daftar dugaan |
| 2. Rencana uji | Tulis `docs/rencana-uji-<grup>-putaran-<n>.md` dari `references/template-dokumen.md`. Tunjukkan ke user kalau cakupannya besar atau ada yang perlu ditanyakan. | Rencana |
| 3. Hitung manual | Hitung angka harapan **sebelum** menjalankan aplikasi (total, diskon, pajak, HPP, saldo, sisa piutang). | Angka harapan |
| 4. Test alur | Jalankan alur lewat kode asli aplikasi (form/komponen/service), **bukan** insert langsung ke tabel. Setelah **setiap langkah** jalankan pemeriksa invarian. | Test alur |
| 5. Kasus tepi | Validasi, batal di tiap status, tanggal mundur/periode tertutup, dobel submit, batas angka, data kosong (`references/checklist.md`). | Test kasus tepi |
| 6. Hak akses | Tiap peran: menu sesuai izin, URL langsung ditolak (bukan error 500), aksi tanpa izin ditolak, data cabang/pemilik lain tidak bocor. | Test akses |
| 7. Halaman & cetakan | Semua halaman grup terbuka tanpa error; cetak/PDF/Excel jalan dan **angkanya sama dengan layar**. | Smoke test |
| 8. Laporan silang | Angka yang sama muncul identik di semua laporan terkait. | Test konsistensi |
| 9. Catat temuan | Tulis di `docs/audit-bug-<grup>.md`: bug + bukti test, keputusan K-xx, yang sudah terbukti benar, sisa uji manual. Test pembukti bug diberi grup `temuan` supaya full suite tidak merah. | Dokumen temuan |
| 10. Lapor | Ringkas: jumlah bug per tingkat, ada/tidak bug uang/stok/jurnal, daftar keputusan beserta opsinya. **Berhenti, tunggu perintah.** | |
| 11. Perbaiki | Sesuai keputusan. Pindahkan test pembukti dari grup `temuan` ke test biasa yang mengunci perilaku baru. | |
| 12. Regresi | Test grup + full suite di database produksi. Bandingkan dengan kegagalan bawaan lama supaya tidak dikira regresi. Cek grup lain yang memakai kode yang diubah. | Angka lulus/gagal |
| 13. Putaran lanjutan | Kalau perbaikan menyentuh banyak tempat, ulangi langkah 4–8 untuk bug turunan. | |
| 14. Tutup | Perbarui dokumen temuan, backlog, dan tabel status/log putaran di template project. Commit kalau diminta. | |

**Tingkat bug:** **Kritis** (uang/stok/jurnal salah tanpa disadari, data bocor, celah keamanan) ·
**Tinggi** (alur penting salah atau bisa dilewati, mis. persetujuan dilompati) ·
**Sedang** (salah di kasus tertentu, ada jalan keluar) · **Rendah** (tampilan, pesan, kenyamanan).

## 3. Lapisan pengujian

1. **Alur ujung ke ujung** lewat menu sebenarnya (mis. stok masuk lewat Penerimaan Barang, bukan insert tabel stok).
   Pakai ulang helper/fixture test yang sudah ada.
2. **Skenario hitungan manual** — angka harapan ditulis sendiri, bukan disalin dari output aplikasi.
3. **Pemeriksa invarian setelah setiap langkah.** Kumpulkan temuan ke array lalu assert sekali di akhir, supaya
   satu test menghasilkan daftar selisih yang lengkap.
4. **Variasi konfigurasi penting** lewat data provider (mis. metode stok average/FIFO, pajak inklusif/eksklusif).
5. **Kasus tepi & validasi** (`references/checklist.md`).
6. **Hak akses per peran.**
7. **Khusus database produksi** (`references/checklist.md` bagian database).
8. **Smoke semua halaman + cetak/export.**
9. **Konsistensi antar laporan.** Kalau ada fitur "hitung/jurnal ulang", hasilnya harus identik dengan semula.
10. **Data besar & performa** — ribuan baris; datatable, laporan, export tidak timeout dan tidak N+1 (hitung query).
11. **Uji manual oleh user** untuk yang tidak bisa diotomatisasi (printer fisik, scanner/kamera, tablet/HP, tampilan).
    Tulis sebagai checklist "Sisa uji manual".

## 4. Bentuk laporan ke user (langkah 10)

- Satu kalimat hasil utama dulu (mis. "Grup Stok: 3 bug, 0 bug jurnal, 1 keputusan.").
- Tabel bug: ID · tingkat · apa yang salah · dampak · status.
- Keputusan K-xx: pertanyaan, opsi (a)/(b) beserta dampaknya, rekomendasi.
- Angka test: baseline, test baru (berapa merah sebelum perbaikan), full suite dibanding kegagalan bawaan.
- Sisa uji manual.
- Lalu berhenti.

## 5. Contoh perintah user

- "Testing grup Stok putaran 2" → langkah 0–10, lalu berhenti dan lapor.
- "Perbaiki S12–S14, K-S1 pilih a" → langkah 11–12, lalu perbarui dokumen.
- "Testing semua grup" → satu grup per giliran sesuai urutan di template project; lapor per grup.
- "Buatkan template testing untuk project ini" → langkah 0.1 (buat `docs/TEMPLATE_TESTING.md`), lalu tunggu persetujuan.

## Berkas pendukung

- `references/template-project.md` — kerangka `docs/TEMPLATE_TESTING.md` untuk project baru.
- `references/template-dokumen.md` — template rencana uji & dokumen temuan.
- `references/checklist.md` — contoh invarian per domain, kasus tepi umum, jebakan PostgreSQL/MySQL, laporan silang.
- `references/template-test.md` — kerangka test alur & test pembukti bug (contoh PHPUnit/Laravel, mudah diadaptasi).
