# Template dokumen

## 1. Rencana uji: `docs/rencana-uji-<grup>-putaran-<n>.md`

```markdown
# Rencana Uji <Grup> Putaran <n>

Dibuat <tanggal>. Status: **RENCANA** | **DIEKSEKUSI <tanggal>** (hasil di `docs/audit-bug-<grup>.md` bagian Putaran <n>).

## Tujuan
Satu perintah untuk menjalankan semua test grup ini: `<perintah test> --group=<grup>-flow`.

## Cakupan menu
| Menu | Kode utama | Efek data (stok/saldo/status) | Jurnal/efek keuangan |
|---|---|---|---|
**Tidak termasuk** (alasan/keputusan user): ...

## Perubahan sejak putaran sebelumnya
Commit/fitur baru yang wajib diuji: ...

## Efek keuangan yang seharusnya (kalau relevan)
| Kejadian | Debit | Kredit |
|---|---|---|

## Dugaan dari membaca kode (wajib dibuktikan)
- **D1** ... (file:baris)

## Fixture
Cabang/lokasi, master data, user per peran, pengaturan (toggle), variasi konfigurasi.

## Alur (tahap T0..Tn)
| Tahap | Isi | Angka harapan (hitung manual) |
|---|---|---|

## Pemeriksa setelah setiap langkah
(salin dari invarian template project, coret yang tidak relevan beserta alasannya)

## Kasus tepi
(dari checklist umum + khusus database + khusus grup ini)

## Hak akses
| Peran/izin | Boleh | Tidak boleh |
|---|---|---|

## Laporan/cetakan yang dicek
```

## 2. Temuan: bagian baru di `docs/audit-bug-<grup>.md`

Untuk putaran baru, tambahkan bagian `## Putaran <n> (<tanggal>)` di dokumen grup yang sudah ada. Buat file baru
hanya kalau grup itu belum punya dokumen.

```markdown
## Putaran <n> (<tanggal>)

Rencana: `docs/rencana-uji-<grup>-putaran-<n>.md`. Kode aplikasi tidak diubah selama pengujian.

### Ringkasan
| | Jumlah |
|---|---|
| Bug | <n> (Kritis · Tinggi · Sedang · Rendah) |
| Bug khusus database | |
| Perlu keputusan | |
| Uang/stok/jurnal | **Tidak ditemukan selisih** / <n> bug |

### Cara menjalankan
    <perintah test> --group=<grup>-flow
    <perintah test> --group=temuan        # bug yang masih terbuka

### Bug

#### <ID> · <Tingkat> · <judul satu kalimat: apa yang salah>
- Menu / tahap:
- Langkah: (bisa diulang orang lain, dengan angka)
- Seharusnya:
- Yang terjadi:
- Dampak uang/stok/data:
- Lokasi kode: `path/file:<baris>` (+ sebabnya)
- Saran perbaikan: (kalau menyangkut aturan bisnis → jadikan K-xx)
- Test: `<File>::test_<id>_...`
- Status: **Terbuka** | **Diperbaiki <tanggal>** (ringkas perbaikannya)

### Perlu keputusan

#### K-<prefix><n> · <pertanyaan>
- Kondisi sekarang:
- (a) ... dampaknya ...
- (b) ... dampaknya ...
- Rekomendasi: (a), karena ...
- Keputusan user: **<a/b, tanggal>**

### Yang sudah terbukti benar
(daftar alur/angka yang lolos, supaya tidak diuji ulang tanpa alasan)

### Sisa uji manual (oleh user)
- [ ] ...

### Hasil perbaikan & regresi
Test grup <x/x> lulus. Full suite <lulus>/<total>, gagal hanya bawaan lama: ...
```
