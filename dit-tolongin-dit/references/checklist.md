# Checklist

Pilih yang relevan untuk project & grup. Yang tidak relevan dicoret di rencana beserta alasannya, jangan dilewati
diam-diam.

## 1. Contoh invarian (cek setelah setiap langkah)

### Aplikasi dengan akuntansi/jurnal
- [ ] Setiap dokumen berefek keuangan punya jurnal. Yang belum disetujui, dibatalkan, atau ditolak tidak punya jurnal aktif.
- [ ] Setiap jurnal seimbang (debit = kredit) per dokumen dan per cabang.
- [ ] Akun, sisi, nominal, **tanggal**, dan **cabang** jurnal benar (bandingkan dengan tabel efek keuangan di rencana).
- [ ] Batal/hapus dokumen menghapus atau membalik jurnalnya sepenuhnya.
- [ ] Fitur "jurnal ulang" menghasilkan jurnal identik.
- [ ] Neraca saldo seimbang; Aset = Kewajiban + Ekuitas (termasuk laba berjalan).
- [ ] Saldo buku besar = data operasional: persediaan = nilai stok, piutang = sisa nota, hutang = sisa tagihan,
      kas/bank = pembayaran berhasil, pajak = rincian pajak dokumen.

### Aplikasi dengan stok
- [ ] Qty per produk/gudang/satuan benar (termasuk konversi satuan).
- [ ] Kartu stok urut tanggal, saldo berjalan benar; transaksi bertanggal mundur menghitung ulang sesudahnya.
- [ ] HPP sesuai metode (average/FIFO/dll.) = hitungan manual.
- [ ] Stok tidak minus kecuali diizinkan pengaturan.
- [ ] Tutup periode: snapshot sesuai, periode tertutup menolak transaksi baru/ubah/batal.

### Dokumen transaksi (umum)
- [ ] Status sesuai isi (draft/menunggu/disetujui/lunas/sebagian/batal).
- [ ] Jumlah terbayar, retur, dan sisa sesuai rinciannya.
- [ ] Nomor dokumen unik, berurutan, tidak bentrok saat dua orang menyimpan bersamaan.
- [ ] Riwayat/audit trail tercatat.

### Aplikasi non-keuangan
- [ ] Data turunan (counter, agregat, cache, denormalisasi) selalu sama dengan sumbernya.
- [ ] Hapus/batal membersihkan semua data turunan.
- [ ] Notifikasi/job/email terkirim sekali, tidak dobel, tidak terkirim untuk aksi yang gagal.

## 2. Kasus tepi umum per menu
- [ ] Validasi wajib isi, format, angka 0/negatif, angka sangat besar, desimal, teks di isian angka.
- [ ] Nama/kode dobel, termasuk beda huruf besar/kecil dan spasi di ujung.
- [ ] Dobel klik simpan tidak membuat dua dokumen.
- [ ] Ubah dokumen setelah disetujui/dibayar/diproses ditolak atau mengikuti aturan.
- [ ] Batal di setiap status (draft, menunggu, disetujui, sebagian dibayar, lunas, sesudah retur).
- [ ] Persetujuan tidak bisa dilewati lewat jalur lain (form lain, impor, API, aksi cepat). Penolakan mengembalikan stok/saldo.
- [ ] Tanggal mundur, tanggal di masa depan, tanggal di periode tertutup.
- [ ] Multi cabang/tenant/pemilik: data pihak lain tidak tampil dan tidak bisa diubah lewat id di URL/request.
- [ ] Master yang sudah dipakai transaksi tidak bisa dihapus atau diganti sifatnya.
- [ ] Master nonaktif tidak muncul di pilihan transaksi baru, tapi transaksi lama tetap tampil.
- [ ] Pencarian/filter/urut benar, termasuk karakter `%`, `_`, dan `'`.
- [ ] XSS: teks berisi `<script>` tampil sebagai teks di tabel, detail, dan cetakan.
- [ ] Upload file: tipe berbahaya ditolak (mis. `.php`, `.html`, svg berisi script), ukuran dibatasi.
- [ ] Impor Excel/CSV: file salah format, baris error ditampilkan, impor ulang tidak dobel; ekspor lalu impor kembali hasilnya sama.
- [ ] Pembulatan uang konsisten antara layar, cetakan, dan database/jurnal.
- [ ] Sesi/izin dicabut di tengah jalan: tab yang masih terbuka tidak bisa lagi menyimpan.

## 3. Jebakan khusus database

### PostgreSQL
- [ ] Query tanpa `ORDER BY` — urutan bisa acak setelah update.
- [ ] `LIKE` peka huruf besar/kecil; pencarian pakai `ILIKE`/`LOWER()`.
- [ ] `GROUP BY` ketat: kolom yang di-select wajib di group by atau di dalam agregat.
- [ ] Perbandingan tipe: string vs integer (`'1' = 1` bisa error), boolean (`true`, bukan `1`), `numeric` kembali sebagai string.
- [ ] Pembagian integer (`5/2 = 2`); `SUM` kosong = `NULL` (pakai `COALESCE`).
- [ ] Fungsi khusus MySQL (`DATE_FORMAT`, `MONTH()`, `IFNULL`, `GROUP_CONCAT`) tidak ada.
- [ ] Sequence id setelah insert dengan id manual (seeder/impor) — insert berikutnya bentrok.
- [ ] Error di tengah transaksi membuat transaksi `aborted`; query berikutnya ikut gagal.
- [ ] Lock/advisory lock bekerja saat dua proses bersamaan.

### MySQL/MariaDB
- [ ] `ONLY_FULL_GROUP_BY`, perbandingan string case-insensitive (collation), `0000-00-00`, pembulatan `DECIMAL`.
- [ ] Batas panjang index/varchar dengan utf8mb4.

## 4. Konsistensi antar laporan (contoh)

Satu set transaksi, lalu cek angka yang sama muncul identik di semua tempat:

| Angka | Harus sama di |
|---|---|
| Penjualan bersih | Laporan penjualan, dashboard, laba rugi (pendapatan), laporan kasir/shift |
| HPP | Laporan penjualan (laba kotor), laba rugi, kartu stok |
| Pembayaran | Laporan pembayaran, kas/bank di buku besar, rekap shift |
| Pembelian & hutang | Laporan pembelian, laporan pembayaran pembelian, hutang di neraca |
| Stok | Halaman stok, laporan stok, laporan mutasi, persediaan di neraca |

Cek juga: batas tanggal awal/akhir inklusif, filter cabang, transaksi batal tidak ikut dihitung, layar = cetak/PDF/Excel.
