# DEBUG_LOG: Katalog Karya (latihan-p10)

Nama: Nabila Putri  
NRP: 5125500044  
Repositori: https://github.com/abilwashere/ppv-latihan-p10-5125500044

Catatan: Dokumentasi ini mencatat proses penelusuran dan perbaikan bug G1–G6. Tangkapan layar DevTools/Network dilampirkan pada PDF laporan P10, bukan ditempelkan di file Markdown ini.

---

## Bug 1 — Logo navbar tidak tampil (G1)

- **Cara mengulang:** Buka `index.html` melalui browser. Perhatikan logo pada navbar, lalu buka DevTools → Network dan muat ulang halaman.
- **Gejala:** Logo tidak tampil dan browser menampilkan ikon gambar rusak.
- **Hipotesis 1 (gugur):** File `logo.svg` tidak tersedia. Hipotesis ini gugur setelah memeriksa struktur folder karena file tersedia di `assets/logo.svg`.
- **Hipotesis 2 (terbukti):** Path pada atribut `src` mengarah ke lokasi yang salah. Kode awal memanggil `img/logo.svg`, sedangkan lokasi file sebenarnya adalah `assets/logo.svg`.
- **Bukti DevTools:** Pada Network, permintaan ke `img/logo.svg` menghasilkan status 404 (Not Found). Pemeriksaan struktur folder menunjukkan file berada di folder `assets`.
- **Perbaikan:** `index.html` baris 13 — ubah path gambar menjadi `assets/logo.svg`.
- **Uji ulang:** Muat ulang halaman dan periksa Network. Logo seharusnya tampil dan permintaan `assets/logo.svg` berhasil tanpa status 404.
- **Commit perbaikan:** `47882f3` — `chore(assets): perbaiki jalur logo ke assets/logo.svg agar tidak 404`

## Bug 2 — Kartu karya tersusun satu kolom (G2)

- **Cara mengulang:** Buka halaman pada laptop atau lebarkan viewport hingga lebih dari 992 px. Perhatikan susunan kartu pada galeri.
- **Gejala:** Kartu tersusun memanjang satu kolom meskipun lebar layar cukup untuk menampilkan beberapa kolom.
- **Hipotesis 1 (gugur):** Class kolom pada setiap kartu salah. Hipotesis ini gugur setelah memeriksa fungsi `kartu()` karena elemen kartu sudah memiliki class responsif `col-12 col-sm-6 col-lg-4`.
- **Hipotesis 2 (terbukti):** Elemen induk galeri belum menggunakan class Bootstrap `row`, sehingga sistem grid tidak tersusun sebagaimana mestinya.
- **Bukti DevTools:** Pada Elements, elemen `#galeri` hanya memiliki class `g-3` pada kode awal, sedangkan elemen anak menggunakan class `col-*`. Struktur grid Bootstrap membutuhkan elemen induk dengan class `row`.
- **Perbaikan:** `index.html` baris 34 — tambahkan class `row` sehingga menjadi `class="row g-3"`.
- **Uji ulang:** Lebarkan viewport. Kartu seharusnya tersusun beberapa kolom sesuai breakpoint Bootstrap dan kembali menjadi satu kolom pada layar kecil.
- **Commit perbaikan:** `a4fb314` — `fix(grid): tambah class row pada #galeri agar kolom Bootstrap berjajar`

## Bug 3 — Filter kategori tidak berfungsi (G3)

- **Cara mengulang:** Buka halaman, lalu pilih kategori **Video** pada dropdown filter.
- **Gejala:** Daftar karya tidak berubah dan Console menampilkan TypeError.
- **Hipotesis 1 (gugur):** Nilai kategori pada data tidak cocok dengan pilihan dropdown. Hipotesis ini gugur setelah memeriksa `data.js` dan pilihan pada HTML; keduanya menggunakan nilai kategori yang sama, yaitu `Animasi`, `Video`, dan `Desain`.
- **Hipotesis 2 (terbukti):** Selector yang digunakan untuk mengambil elemen filter tidak sesuai dengan ID pada HTML. Kode awal mencari `#filter-kategori`, sedangkan elemen HTML memiliki ID `filterKategori`.
- **Bukti DevTools:** Console menunjukkan TypeError ketika kode mencoba memasang event listener pada hasil selector yang bernilai `null`. Pemeriksaan Elements menunjukkan ID elemen yang benar adalah `filterKategori`.
- **Perbaikan:** `main.js` baris 7 dan 54 — ambil elemen dengan `document.querySelector('#filterKategori')`, lalu pasang event listener `change` melalui variabel `filterKategori`.
- **Uji ulang:** Pilih kategori Video, Animasi, dan Desain secara bergantian. Daftar karya seharusnya berubah mengikuti kategori yang dipilih dan Console tidak lagi menampilkan TypeError tersebut.
- **Commit perbaikan:** `6de986b` — `fix(filter): pakai variabel filterKategori karena selektor #filter-kategori tidak ada (null)`

## Bug 4 — Jumlah karya tidak mengikuti hasil pencarian/filter (G4)

- **Cara mengulang:** Ketik `Video` pada kotak pencarian atau pilih salah satu kategori sehingga jumlah hasil lebih sedikit dari jumlah seluruh data.
- **Gejala:** Kartu yang tampil berkurang, tetapi teks jumlah karya tetap menunjukkan jumlah seluruh data.
- **Hipotesis 1 (gugur):** Fungsi pencarian/filter tidak menghasilkan data yang tersaring. Hipotesis ini gugur apabila kartu yang tampil sudah berubah sesuai kata pencarian atau kategori yang dipilih.
- **Hipotesis 2 (terbukti):** Teks jumlah menggunakan `KARYA.length`, yaitu jumlah seluruh data, bukan `hasil.length`, yaitu jumlah data setelah disaring.
- **Bukti DevTools:** Pasang breakpoint pada fungsi `tampil()` dan periksa variabel `hasil`. Nilai `hasil.length` berubah mengikuti pencarian/filter, sedangkan `KARYA.length` tetap 6.
- **Perbaikan:** `main.js` baris 38 — gunakan `hasil.length` untuk jumlah karya yang sedang ditampilkan, sementara `KARYA.length` tetap digunakan sebagai jumlah seluruh karya.
- **Uji ulang:** Ketik `Video`. Jumlah hasil yang ditampilkan seharusnya menjadi 2 dari 6 karya. Hapus kata pencarian; jumlah kembali menjadi 6 dari 6 karya.
- **Commit perbaikan:** `9be7abe` — `fix(jumlah): pakai hasil.length untuk jumlah tampil, bukan KARYA.length`

## Bug 5 — Tombol Detail tidak berfungsi setelah pencarian (G5)

- **Cara mengulang:** Buka halaman, ketik `Animasi` pada kotak pencarian, kemudian klik tombol **Detail** pada salah satu kartu hasil.
- **Gejala:** Tombol Detail berfungsi pada tampilan awal, tetapi tidak merespons setelah daftar kartu diperbarui melalui pencarian.
- **Hipotesis 1 (gugur):** Data karya yang dicari tidak memiliki informasi detail. Hipotesis ini gugur karena objek karya pada `data.js` tetap memiliki judul, kategori, pembuat, dan tahun.
- **Hipotesis 2 (terbukti):** Event listener hanya dipasang satu kali saat halaman pertama kali dimuat. Ketika `tampil()` menjalankan `galeri.replaceChildren(...)`, tombol lama dihapus dan tombol baru dibuat tanpa event listener.
- **Bukti DevTools:** Periksa elemen tombol pada Elements setelah pencarian. Tombol yang tampil merupakan elemen baru hasil render ulang. Pemeriksaan event listener pada tombol hasil render ulang dapat digunakan untuk memastikan listener belum terpasang.
- **Perbaikan:** `main.js` baris 39 — panggil `pasangDetail()` setelah elemen kartu diperbarui di dalam fungsi `tampil()`, sehingga listener dipasang pada tombol yang baru dibuat.
- **Uji ulang:** Ketik `Animasi`, lalu klik Detail pada kartu hasil. Informasi judul, kategori, pembuat, dan tahun seharusnya muncul pada bagian detail.
- **Commit perbaikan:** `025af1d` — `fix(detail): pasang ulang listener Detail tiap tampil() karena tombol dibuat ulang`

## Bug 6 — Pencarian tidak menemukan karya jika huruf kecil (G6)

- **Cara mengulang:** Ketik `animasi` (huruf kecil) pada kotak pencarian.
- **Gejala:** Pencarian tidak menemukan karya yang judulnya mengandung kata Animasi dengan huruf kapital.
- **Hipotesis 1 (gugur):** Data judul karya tidak mengandung kata yang dicari. Hipotesis ini gugur setelah memeriksa `data.js`; judul seperti `Animasi Logo Himpunan` dan `Animasi Explainer Beasiswa` memang mengandung kata tersebut.
- **Hipotesis 2 (terbukti):** Pencarian membandingkan teks dengan memperhatikan perbedaan huruf besar dan kecil karena teks input dan judul belum diseragamkan kapitalisasinya.
- **Bukti DevTools:** Periksa nilai kata pencarian dan judul pada fungsi `saring()`. Pada kode awal, `includes()` membandingkan teks secara langsung tanpa normalisasi huruf.
- **Perbaikan:** `main.js` baris 10 dan 14 — gunakan `toLowerCase()` pada kata pencarian dan judul sebelum menjalankan `includes()`.
- **Uji ulang:** Cari `animasi` dan `Animasi`. Kedua penulisan seharusnya menghasilkan karya yang sama.
- **Commit perbaikan:** `2037230` — `fix(cari): ubah kata cari dan judul ke huruf kecil agar pencarian tidak peka huruf besar`

---

## Ringkasan hasil

Enam bug latihan G1–G6 telah diperbaiki dengan commit terpisah. Perbaikan dilakukan pada path aset, struktur grid Bootstrap, selector filter, perhitungan jumlah hasil, pemasangan event listener tombol Detail, dan normalisasi huruf pada pencarian.
