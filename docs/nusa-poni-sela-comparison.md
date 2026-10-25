# Perbandingan Nusa, Poni, dan integrasi Sela

Tanggal pemeriksaan: 25 Oktober 2026, Asia/Jakarta.

**Pilihan basis fitur: Poni. Status penggabungan source Sela: belum dapat
dilaksanakan karena source aplikasi Poni tidak tersedia dalam repo maupun
installer yang diberikan.** Dokumen ini tidak menyatakan integrasi kode selesai.

## Bukti yang diperiksa

| Bahan | Identitas | Batas bukti |
| --- | --- | --- |
| Repo pengguna `tetukon9-cmd/Poni` | Commit `9546382be70ce40bd2e20d7970e393457cf92e2b`, default `master` | Hanya README dan dua gambar |
| Seluruh ref/riwayat repo | Satu remote branch, tanpa tag; 36 commit terjangkau | Seluruh path historis: README, CHANGELOG, dua gambar |
| Installer Poni lokal | `Poni_1.1.8_x64-setup.exe`, 3.980.730 byte | Dibaca dan dibuka sebagai arsip, tidak dieksekusi |
| Poni portable resmi | v1.1.8, 9.378.816 byte | Hash dan struktur PE diperiksa; tidak dieksekusi |
| Nusa lokal | Installer 2.809.698 byte, payload 909.312 byte | Hasil inspeksi statis dalam pekerjaan sebelumnya |
| Source Sela | `cb279cef26e6eb1f46eb9607ddeb1d615c580b48` | C# WPF/.NET 10; source tersedia |

Sumber fitur Poni adalah [README pada commit yang diperiksa](https://github.com/tetukon9-cmd/Poni/blob/9546382be70ce40bd2e20d7970e393457cf92e2b/README.md)
dan [rilis resmi v1.1.8](https://github.com/liimalim/Poni/releases/tag/v1.1.8).
Repo asal `liimalim/Poni` juga hanya menampilkan dokumentasi dan aset.
Panduan dan catatan rilis di situs penerbit mengembalikan HTTP 403 selama
pemeriksaan; isinya tidak dianggap sudah diverifikasi.

## Perbandingan fitur

`Ada` pada Poni berarti disebut dalam dokumentasi penerbit; pada Nusa berarti
ada bukti label/import statis pada paket yang diperiksa. Ini bukan hasil UAT.
`Belum terbukti` tidak berarti fitur tersebut pasti tidak ada.

| Kelompok fitur | Nusa 1.0.0 | Poni 1.1.8 |
| --- | --- | --- |
| Musik dan lirik | Ada | Ada |
| Pomodoro/fokus | Ada | Ada |
| Pengingat minum | Ada | Ada, ditambah jeda/breathing/istirahat mata |
| To-do | Ada | Ada, ditambah catatan |
| Shelf file | Ada | Ada |
| Volume/brightness HUD | Ada | Ada |
| Status baterai/konektivitas | Ada | Ada, ditambah pengaturan radio/perangkat |
| Cuaca | Belum terbukti | Ada |
| Kalender dan pengingat meeting | Belum terbukti | Ada |
| Jadwal sholat | Belum terbukti | Ada |
| Launcher dan quick toggles | Belum terbukti | Ada |
| Screen time dan statistik sistem | Belum terbukti | Ada |
| Aksi file/konversi/arsip | Belum terbukti di luar shelf | Ada |
| Tiling Snap Zones | Belum terbukti | Ada |
| Penampil notifikasi Windows | Ada nama API; alur belum terbukti | Ada |
| AI, terjemahan, aksi asisten | Belum terbukti | Ada |
| Daftar review PR GitHub | Belum terbukti | Ada |
| Cat Buddy/cursor/touchscreen | Maskot/minimal notch terbukti; lainnya belum | Ada |

Poni memiliki cakupan fitur terdokumentasi yang lebih luas. Karena itu Poni
dipilih sebagai basis produk gabungan yang diminta. Keputusan ini bukan
benchmark performa, audit keamanan menyeluruh, atau pernyataan bahwa semua
fitur Poni telah diuji. Ukuran binary dan pilihan framework tidak membuktikan
efisiensi CPU/RAM saat berjalan.

## Arsitektur dan hasil installer

| Aspek | Nusa | Poni | Sela |
| --- | --- | --- | --- |
| Penanda teknologi | Rust/windows-rs, API grafis Windows | Rust/Tauri/Wry/WebView2 | C# WPF/.NET 10 |
| Aplikasi Windows x64 | PE32+ native | PE32+ native | Source managed tersedia |
| Kemasan installer | Inno Setup 6.7.0 | NSIS-3 Unicode, solid LZMA | Skrip Inno tersedia |
| Proyek yang bisa dibuild dari repo | Tidak tersedia pada input | Tidak tersedia | Tersedia |

Hash SHA-256 installer Poni lokal:
`2733f7e8fc7c3bbe44b9bd8397521607ecaafb2128fa1c86fa6c811b4d1f5219`.
Hash ini cocok dengan digest aset rilis resmi yang dibaca dari GitHub.

Daftar NSIS terdiri dari `poni.exe`, `uninstall.exe`, dan tujuh komponen
installer: `System.dll`, `modern-wizard.bmp`, `nsDialogs.dll`,
`nsis_tauri_utils.dll`, `StartMenu.dll`, `NSISdl.dll`, `nsExec.dll`.
Tidak ada proyek Rust/frontend atau manifest build dalam daftar tersebut.
Installer lolos pemeriksaan integritas arsip dengan 7-Zip 26.05. Hanya
`poni.exe` diekstrak untuk inspeksi; tidak ada program Poni yang dijalankan.

Hash payload installer `poni.exe`:
`0fb81bc927796e6a131003bac8ca6b6d49ab9b72d5337cf85346ea04f4ab5cd1`.
Hash portable resmi:
`ad0553431bd787e1b5d3f96732580744157540293157df99bc01c0c3fcd19cab`.
Keduanya berukuran sama tetapi hash berbeda. Perbandingan byte menunjukkan
tiga byte berbeda pada offset 8593846–8593848, di penanda jenis bundle Tauri
`UNK` versus `NSS`; bagian `.text` identik. Ini bukan bukti kesetaraan perilaku
runtime. Kedua PE aplikasi mempunyai CLR directory kosong; Authenticode
portable dan installer melaporkan `NotSigned`. Tanda tangan updater yang
disebut README adalah mekanisme berbeda dari Authenticode dan belum diuji.

Penanda versi library yang ditemukan: `tauri-2.12.4`, `wry-0.57.0`,
`webview2-com-0.39.1`. Penanda ini tidak menggantikan Cargo.lock atau manifest
proyek. String internal Tauri tentang plugin/protocol juga tidak membuktikan
tersedianya API plugin publik untuk memasang Sela.

Lihat [metadata bukti](poni-source-audit.json) untuk ukuran, hash, offset,
import DLL, ref repo, serta provenance alat pemeriksaan. Binary, aset
ekstraksi, dan dump string mentah tidak dimasukkan ke repo.

## Penambahan Sela yang ditargetkan

Permintaan pengguna adalah **satu aplikasi gabungan**, sehingga menjalankan
dua executable atau menambah shortcut Sela tidak dianggap memenuhi tujuan.

| Fungsi Sela | Bukti source Sela | Integrasi yang dituju setelah source Poni tersedia |
| --- | --- | --- |
| Adzan, mute/volume, pilihan per sholat dan audio Subuh | `Services/AdhanPlayer.cs`, `SelaSettings` | Gunakan jadwal Poni yang ada untuk mencegah pengingat ganda |
| Hadits harian Arab dan terjemahan Indonesia/Inggris | `Sela.Core/HadithService.cs` | Tambahkan konten dan cache pada UI/backend Poni |
| Check-in dzikir pagi/petang dan sholawat | `Sela.Core/Habits.cs` | Tambahkan state harian dan reminder dalam antrean Poni |
| Kalender Outlook klasik lokal | `Services/OutlookCalendarProvider.cs` | Audit provider Poni dahulu; pertahankan akses kalender read-only |
| Jadwal/cache dan timer fokus | Source Core dan test | Audit overlap dan pakai satu pemilik state untuk setiap fungsi |

Fitur Poni yang sudah ada harus dipertahankan. Tidak diasumsikan Poni pasti
belum memiliki setiap fungsi di atas; source diperlukan untuk audit overlap.
Source Sela memakai lisensi MIT dengan copyright yang harus dipertahankan
jika kodenya digunakan. Source aplikasi Poni belum tersedia, sehingga belum
ada bagian aplikasi Poni yang diubah atau diberi lisensi baru.

## Status dan kebutuhan berikutnya

- **PASS:** perbandingan cakupan fitur, inventory seluruh riwayat repo,
  verifikasi digest installer, pemeriksaan integritas dan metadata binary.
- **BLOCKED:** penggabungan kode menjadi satu aplikasi; source aplikasi Poni
  belum ada pada bahan yang diberikan.
- **NOT RUN:** build/lint/test aplikasi gabungan, karena tidak ada proyek
  aplikasi Poni yang bisa dibuild. Hasil test Sela sebelumnya tidak digunakan
  sebagai bukti keberhasilan integrasi Poni.

Bahan yang diperlukan adalah proyek aplikasi asli, misalnya `Cargo.toml`,
`Cargo.lock`, `src-tauri`/source backend, source frontend, manifest dependensi,
serta resource yang dirujuk build (susunan persisnya mengikuti proyek asli).
Installer adalah hasil kompilasi; mengekstraknya tidak memulihkan proyek
source lengkap. Alternatif berupa implementasi ulang fitur Poni dari awal
merupakan pekerjaan berbeda dan belum dipilih pengguna.
