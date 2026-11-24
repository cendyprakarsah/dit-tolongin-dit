# Kerangka `docs/TEMPLATE_TESTING.md` untuk project baru

Isi berdasarkan hasil mengenali project (langkah 0.1 di SKILL.md). Bagian yang tidak diketahui ditandai `TODO`
dan ditanyakan ke user, jangan ditebak. Tunjukkan ke user sebelum dipakai.

```markdown
# Template Testing <Nama Aplikasi>

Dibuat <tanggal> dengan skill dit-tolongin-dit. Dokumen ini berisi aturan khusus project; alur umum ada di skill.

## 1. Aturan khusus project
- Bahasa & istilah menu: ...
- Database produksi: <PostgreSQL/MySQL/...> → semua test wajib dijalankan di sini.
- Nama database test: `<app>_test_<nama>` (unik per sesi). **Jangan pernah** memakai database aplikasi `<db_aplikasi>`.
- Aturan coding yang wajib diikuti saat memperbaiki: `<path>`.
- Kegagalan test bawaan lama (bukan regresi): <daftar atau "belum ada">.

## 2. Menjalankan test
    <perintah membuat database test>
    <perintah menjalankan test dengan database produksi, mis. override env DB_CONNECTION/DB_DATABASE>
    <perintah menjalankan satu grup>

Catatan: <cara memastikan env benar-benar terpakai, mis. force="true" di salinan phpunit.xml>.

## 3. Invarian yang dicek setelah setiap langkah
(pilih dari references/checklist.md skill + aturan bisnis khusus aplikasi ini)

## 4. Kasus tepi khusus aplikasi ini
(tambahan di luar checklist umum: toggle pengaturan, aturan persetujuan, multi cabang, dsb.)

## 5. Konsistensi antar laporan
| Angka | Harus sama di |
|---|---|

## 6. Peta grup menu & status
| # | Grup | Menu utama | Test group | Dokumen | Prefix temuan | Status |
|---|---|---|---|---|---|---|

Urutan yang disarankan: ...

Log putaran (isi setiap selesai):
| Tanggal | Grup | Putaran | Bug (K/T/S/R) | Bug uang/stok/jurnal | Keputusan | Test grup | Full suite | Commit |
|---|---|---|---|---|---|---|---|---|

## 7. Helper test yang sudah ada
| Helper | Isi |
|---|---|
```
