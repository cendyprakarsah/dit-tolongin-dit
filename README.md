# dit-tolongin-dit

Skill [Claude Code](https://claude.com/claude-code) untuk **audit & testing aplikasi per grup menu** secara
bertahap: rencana uji, test alur ujung ke ujung, pemeriksa invarian, kasus tepi, hak akses, cetakan, konsistensi
laporan, dokumen temuan, dan keputusan bisnis yang diajukan ke user sebagai opsi `K-xx`.

Cocok untuk aplikasi bisnis (ERP, kasir/POS, koperasi, inventori) maupun aplikasi web lain. Bahasa kerja: Indonesia.

## Cara kerja singkat

1. Claude mencari `docs/TEMPLATE_TESTING.md` di project.
   - Ada → diikuti (aturan khusus project mengalahkan skill).
   - Belum ada → Claude mengenali project lalu membuatkan template itu, dan menunggu persetujuan Anda.
2. Per grup menu: baca kode → rencana uji → hitung manual → test alur → kasus tepi → hak akses → cetakan →
   laporan silang → catat temuan → **lapor lalu berhenti**.
3. Perbaikan hanya dikerjakan setelah Anda memerintahkan atau memilih opsi keputusan.

## Pasang

Untuk semua project (tingkat pengguna):

```bash
git clone https://github.com/cendyprakarsah/dit-tolongin-dit.git
mkdir -p ~/.claude/skills
ln -s "$PWD/dit-tolongin-dit/dit-tolongin-dit" ~/.claude/skills/dit-tolongin-dit
```

Hanya untuk satu project: salin folder `dit-tolongin-dit/` ke `.claude/skills/` di project itu.

Perbarui dengan `git pull` di folder hasil clone.

## Pakai

Di Claude Code:

- `/dit-tolongin-dit testing grup Penjualan putaran 1`
- "Audit menu Pembelian pakai dit-tolongin-dit"
- "Buatkan template testing untuk project ini"
- "Perbaiki S12–S14, K-S1 pilih a"

## Isi

| File | Isi |
|---|---|
| `dit-tolongin-dit/SKILL.md` | Aturan kerja & alur satu putaran |
| `dit-tolongin-dit/references/template-project.md` | Kerangka `docs/TEMPLATE_TESTING.md` untuk project baru |
| `dit-tolongin-dit/references/template-dokumen.md` | Template rencana uji & dokumen temuan |
| `dit-tolongin-dit/references/checklist.md` | Invarian, kasus tepi, jebakan PostgreSQL/MySQL, laporan silang |
| `dit-tolongin-dit/references/template-test.md` | Kerangka test alur & test pembukti bug |

## Lisensi

MIT, lihat `LICENSE`.
