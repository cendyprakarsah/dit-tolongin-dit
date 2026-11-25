# Template kode test

Contoh memakai PHPUnit + Laravel. Untuk stack lain (Jest, Pytest, RSpec, dsb.) pakai pola yang sama: fixture,
driver alur lewat kode asli, pemeriksa invarian yang mengumpulkan temuan, assert sekali di akhir.

## Prinsip
- **Pakai ulang helper yang sudah ada** (fixture, driver alur, pemeriksa). Daftarkan helper itu di template project.
- Data masuk lewat **kode aplikasi yang sebenarnya** (form/komponen/service), bukan insert langsung ke tabel.
- Nama test: `test_<id-tahap/temuan>_<kalimat_perilaku>` dalam bahasa user.
- Pesan temuan menyebut tahap, dokumen, data, **angka harapan**, dan **angka aktual**.
- Bekukan waktu (mis. `$this->travelTo(...)`) supaya jatuh tempo/periode stabil.

## Kerangka test alur

```php
<?php

namespace Tests\Feature\<Grup>\Flow;

use Illuminate\Foundation\Testing\RefreshDatabase;
use PHPUnit\Framework\Attributes\DataProvider;
use PHPUnit\Framework\Attributes\Group;
use Tests\TestCase;

/**
 * Rencana: docs/rencana-uji-<grup>-putaran-<n>.md. Temuan: docs/audit-bug-<grup>.md.
 */
#[Group('<grup>-flow')]
class <Grup>FlowScenarioTest extends TestCase
{
    use RefreshDatabase;

    private array $temuan = [];

    private function catat(string $tahap, string $pesan): void
    {
        $this->temuan[] = "[{$tahap}] {$pesan}";
    }

    private function periksaSemua(string $tahap): void
    {
        // panggil semua pemeriksa invarian yang relevan, mis.:
        // $this->periksaJurnalSeimbang($tahap);
        // $this->periksaStokSamaDenganKartu($tahap);
    }

    private function assertTanpaTemuan(): void
    {
        $this->assertSame([], $this->temuan, "Temuan:\n" . implode("\n", $this->temuan));
    }

    public static function variasi(): array
    {
        return ['average' => ['average'], 'fifo' => ['fifo']];
    }

    #[DataProvider('variasi')]
    public function test_t01_<nama_skenario>(string $variasi): void
    {
        // fixture ...

        // Langkah 1 (angka harapan dari rencana, dihitung manual)
        // ... jalankan lewat kode aplikasi asli ...
        $this->periksaSemua('T01 langkah 1');

        // Langkah 2 ...
        $this->periksaSemua('T01 langkah 2');

        $this->assertTanpaTemuan();   // cetak semua selisih sekaligus
    }
}
```

## Test pembukti bug (sebelum diperbaiki)

```php
#[Group('temuan')]
public function test_s12_mutasi_fifo_tidak_mengubah_total_nilai_stok(): void
{
    // langkah persis seperti di dokumen temuan
    // assert perilaku yang SEHARUSNYA (test ini merah sampai bug diperbaiki)
}
```

Saat menjalankan suite (grup lain atau full suite), kecualikan grup `temuan` (mis. `--exclude-group=temuan`) supaya tidak merah selama bug
belum diputuskan. Setelah diperbaiki, hapus grup `temuan`, pindahkan ke grup alur, lalu jalankan ulang.
