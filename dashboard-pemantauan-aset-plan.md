# Pelan Pelaksanaan: Dashboard Pemantauan Aset JKM PSAS

**Dokumen Prosedur Asas:** PSAS-PK-PPP-09 Keluaran 05 Pindaan 01  
**Bertajuk:** Pemantauan Peralatan Pengajaran dan Pembelajaran  
**Jabatan:** Jabatan Kejuruteraan Mekanikal (JKM), Politeknik Sultan Azlan Shah (PSAS)  
**Pemilik Projek:** Pegawai Pengurusan Aset Jabatan (PAJ)

---

## Gambaran Keseluruhan

Membina satu aplikasi web fail tunggal (`.html`) yang membolehkan PAJ memantau semua aset bengkel, makmal dan bilik kuliah JKM PSAS mengikut keperluan prosedur kualiti PSAS-PK-PPP-09. Dashboard ini bukan pengganti borang rasmi kerajaan — ia adalah alat pemantauan yang membantu PAJ menguruskan, menjejak dan melaporkan status aset dan pematuhan prosedur.

**Timbunan Teknologi:** HTML5 + Tailwind CSS (CDN) + Vanilla JavaScript ES6+ + Chart.js (CDN) + localStorage  
**Penghantaran:** Fail `.html` tunggal — tiada pelayan, tiada pemasangan, berfungsi dalam mana-mana pelayar moden  
**Penyimpanan Data:** `localStorage` pelayar untuk keterusan data  
**Eksport:** CSV (melalui JavaScript) dan cetakan PDF (melalui `window.print()` + CSS cetak)

---

## Sub-Tugas 1 — Struktur Fail & Rangka Navigasi

**Status:** `[ ] pending`

### Niat
Mewujudkan fail HTML tunggal dengan semua aset CDN, rangka navigasi sisi (sidebar), pengepala atas (top header), dan sistem penghalaan modul berbasis JavaScript. Ini adalah asas yang digunakan oleh semua sub-tugas seterusnya.

### Hasil Dijangka
- Fail `dashboard-jkm-psas.html` wujud dan boleh dibuka dalam pelayar
- Sidebar menunjukkan kesemua 10 item navigasi dalam Bahasa Melayu
- Pengepala atas menunjukkan nama sistem, nama jabatan, tarikh/masa semasa, dan lencana peranan
- Mengklik item navigasi menunjukkan panel modul yang betul dan menyembunyikan yang lain
- Laman landas lalai ialah Modul 1 (Dashboard Ringkasan)
- Reka bentuk responsif berfungsi pada skrin desktop dan tablet
- Warna korporat digunakan dengan konsisten

### Senarai Tugasan
1. Buat fail `dashboard-jkm-psas.html` dengan doctype HTML5 lengkap
2. Sambungkan CDN: Tailwind CSS, Chart.js, Font Awesome (ikon)
3. Bina struktur tata letak CSS Grid: sidebar kiri (lebar tetap) + kawasan kandungan utama
4. Bina komponen sidebar dengan: logo/nama sistem, nama jabatan, kesemua 10 pautan modul berkod warna, label peranan semasa di bahagian bawah
5. Bina komponen pengepala atas dengan: nama sistem "DASHBOARD PEMANTAUAN ASET JKM PSAS", jam masa nyata, pemilih peranan (PAJ / KJ / Penyelia BMB / PPJ), butang simpan manual
6. Bina fungsi JavaScript `showModule(id)` yang menunjukkan/menyembunyikan panel div modul
7. Laksana pemilih peranan — menukar peranan memaparkan/menyembunyikan kawalan yang berkaitan
8. Bina sistem pemberitahuan untuk amaran tertunggak dalam pengepala atas
9. Tambah CSS cetak: sidebar disembunyikan, kandungan dioptimumkan untuk A4
10. Tambah cap air "DATA CONTOH" untuk semua rekod demonstrasi

### Konteks Relevan
- Skema warna: `#1e3a5f` (biru gelap), `#16a34a` (hijau), `#d97706` (amber), `#dc2626` (merah), `#f8fafc` (latar belakang cerah)
- 10 modul: Dashboard Ringkasan, Pendaftaran Aset, Pemeriksaan PdP, Kerosakan & Status, Keperluan Peralatan, Penyelenggaraan Berjadual, Pergerakan Peralatan, Dokumen Kualiti, Kalendar Tahunan, Laporan & Analitik
- Peranan pengguna mempengaruhi butang tindakan yang kelihatan (bukan kawalan akses sebenar — ini hanya paparan UI)

---

## Sub-Tugas 2 — Storan Data & Skema localStorage

**Status:** `[ ] pending`

### Niat
Mentakrifkan semua struktur data JavaScript (objek/tatasusunan) yang akan disimpan dalam `localStorage`, fungsi utiliti untuk membaca/menulis/mengemas kini rekod, dan data demonstrasi "DATA CONTOH" awal.

### Hasil Dijangka
- 12 stor data yang ditakrifkan dan didokumenkan dalam kod
- Fungsi `loadData()`, `saveData()`, `generateId()` berfungsi dengan betul
- Data demonstrasi dimuatkan secara automatik pada kali pertama laman dibuka (jika `localStorage` kosong)
- Semua rekod demonstrasi mempunyai medan `is_demo: true`
- Tiada kehilangan data apabila pelayar ditutup dan dibuka semula

### Skema Data (12 Stor)

#### `stor_aset` — Daftar Aset (KEW.PA-7)
```
{
  id: string,              // cth. "AST-2025-001"
  no_pendaftaran: string,  // No. KEW.PA-7
  nama_aset: string,
  kategori: string,        // "Peralatan PdP" | "ICT" | "Perabot" | "Lain-lain"
  model: string,
  jenama: string,
  no_siri: string,
  id_bengkel: string,      // FK ke stor_bengkel
  lokasi_rekod: string,
  lokasi_sebenar: string,
  id_pegawai: string,      // FK ke stor_pegawai
  tarikh_perolehan: string, // ISO 8601
  harga_perolehan: number,
  status: string,          // "A" | "B" | "C" | "D" | "E" | "F"
  tarikh_pemeriksaan_akhir: string,
  catatan: string,
  is_demo: boolean,
  tarikh_cipta: string,
  tarikh_kemaskini: string
}
```

#### `stor_bengkel` — Lokasi Bengkel/Makmal
```
{
  id: string,              // cth. "BKL-001"
  kod: string,             // cth. "BM1"
  nama: string,            // cth. "Bengkel Mekanik 1"
  jenis: string,           // "Bengkel" | "Makmal" | "Bilik Kuliah"
  tingkat: string,
  id_penyelia: string,     // FK ke stor_pegawai
  catatan: string
}
```

#### `stor_pegawai` — Rekod Pegawai
```
{
  id: string,
  nama: string,
  no_pekerja: string,
  jawatan: string,
  peranan: string,         // "PAJ" | "KJ" | "PPJ" | "Penyelia BMB" | "PBMB"
  emel: string,
  telefon: string,
  id_bengkel: string       // untuk Penyelia BMB/PBMB
}
```

#### `stor_pemeriksaan` — Rekod Pemeriksaan PdP (PPP-09/FOR/1)
```
{
  id: string,
  no_rujukan: string,      // cth. "PMRKSA-2025-001"
  tahun: number,
  id_bengkel: string,
  tarikh_pemeriksaan: string,
  id_pemeriksa: string,    // FK ke stor_pegawai (PPJ)
  item_pemeriksaan: array, // [{id_aset, dapatan, tindakan, status}]
  status_keseluruhan: string, // "Lengkap" | "Belum Lengkap" | "Dalam Proses"
  pengesahan_paj: boolean,
  tarikh_pengesahan_paj: string,
  catatan: string,
  is_demo: boolean
}
```

#### `stor_kerosakan` — Laporan Kerosakan (KEW.PA-10 + PPP-09/FOR/3)
```
{
  id: string,
  no_rujukan: string,      // cth. "KRS-2025-001"
  id_aset: string,
  tarikh_laporan: string,
  id_pelapor: string,      // FK ke stor_pegawai (Penyelia BMB)
  penerangan: string,
  no_kewpa10: string,
  no_for3: string,
  cadangan: string,        // "Baiki" | "Lupus" | "Simpan"
  anggaran_kos: number,
  kelulusan_kj: string,    // "Lulus" | "Tolak" | "Belum"
  tarikh_kelulusan_kj: string,
  id_penyelia_kj: string,
  tindakan_susulan: string,
  tarikh_selesai: string,
  status: string,          // "Terbuka" | "Dalam Proses" | "Selesai" | "Ditangguh"
  is_demo: boolean
}
```

#### `stor_penyelenggaraan` — Jadual Penyelenggaraan (PPP-09/FOR/5)
```
{
  id: string,
  no_rujukan: string,      // cth. "SLNGG-2025-001"
  id_aset: string,
  tahun: number,
  no_for5: string,
  no_kewpa15: string,
  no_kewpa16: string,
  tarikh_dirancang: string,
  tarikh_sebenar: string,
  jenis: string,           // "Pencegahan" | "Pembaikan" | "Kalibrasi"
  id_pegawai: string,
  kontraktor: string,
  kos_sebenar: number,
  status: string,          // "Belum" | "Sedang" | "Selesai" | "Tertunggak"
  catatan: string,
  is_demo: boolean
}
```

#### `stor_keperluan` — Keperluan Peralatan (PPP-09/FOR/2)
```
{
  id: string,
  no_rujukan: string,      // cth. "KPRL-2025-001"
  tahun: number,
  id_bengkel: string,
  id_pengkemuka: string,   // FK ke stor_pegawai
  tarikh_kemukakan: string,
  nama_peralatan: string,
  kuantiti: number,
  anggaran_harga_seunit: number,
  jumlah_anggaran: number, // dikira auto
  justifikasi: string,
  semakan_paj: string,     // "Disokong" | "Tidak Disokong" | "Belum"
  semakan_kj: string,      // "Diluluskan" | "Tidak Diluluskan" | "Belum"
  status: string,
  catatan: string,
  is_demo: boolean
}
```

#### `stor_pergerakan` — Pergerakan Peralatan (PPP-09/FOR/4)
```
{
  id: string,
  no_rujukan: string,      // cth. "PRGKN-2025-001"
  id_aset: string,
  no_for4: string,
  tarikh_keluar: string,
  id_pelulus: string,      // FK ke stor_pegawai
  nama_pembekal: string,
  telefon_pembekal: string,
  alamat_pembekal: string,
  tujuan: string,          // "Baik Pulih" | "Penyelenggaraan" | "Kalibrasi"
  tarikh_jangka_pulang: string,
  tarikh_sebenar_pulang: string,
  id_pengesah_terima: string,
  keadaan_semasa_pulang: string,
  status: string,          // "Keluar" | "Pulang" | "Tertunggak"
  catatan: string,
  is_demo: boolean
}
```

#### `stor_pelupusan` — Pemantauan Pelupusan (KEW.PA-11, KEW.PA-12)
```
{
  id: string,
  no_rujukan: string,
  id_aset: string,
  tarikh_cadangan: string,
  id_pencadang: string,
  sebab_pelupusan: string,
  no_kewpa11: string,
  no_kewpa12: string,
  status_lembaga: string,  // "Belum Dilantik" | "Dalam Proses" | "Selesai"
  tarikh_keputusan: string,
  kaedah_pelupusan: string,
  catatan: string,
  is_demo: boolean
}
```

#### `stor_borang` — Pematuhan Dokumen Kualiti
```
{
  id: string,
  kod_borang: string,      // cth. "PPP-09/FOR/1"
  tahun: number,
  id_bengkel: string,      // null jika peringkat jabatan
  tarikh_wajib: string,    // tarikh/bulan wajib diselesaikan
  tarikh_serah: string,
  diserahkan_oleh: string,
  status: string,          // "Lengkap" | "Belum Lengkap" | "Tidak Berkaitan"
  catatan: string,
  is_demo: boolean
}
```

#### `stor_audit` — Rekod Audit & Semakan Pengurusan
```
{
  id: string,
  jenis: string,           // "Audit Dalaman" | "Semakan Pengurusan" | "Laporan Suku Tahun"
  tarikh: string,
  suku: string,            // "S1" | "S2" | "S3" | "S4"
  tahun: number,
  dapatan: string,
  tindakan_pembetulan: string,
  status: string,
  catatan: string,
  is_demo: boolean
}
```

#### `stor_tetapan` — Tetapan Sistem
```
{
  nama_institusi: string,  // "Politeknik Sultan Azlan Shah"
  nama_jabatan: string,    // "Jabatan Kejuruteraan Mekanikal"
  nama_paj: string,
  nama_kj: string,
  tahun_semasa: number,
  tarikh_muat_naik_terakhir: string,
  versi: string
}
```

### Senarai Tugasan
1. Bina objek `DB` sebagai pengurus penyimpanan pusat dengan kaedah `load()`, `save()`, `getAll()`, `getById()`, `add()`, `update()`, `remove()`
2. Bina fungsi `generateId(prefix)` yang menghasilkan ID unik dengan awalan dan cap masa
3. Bina fungsi `initDemoData()` yang mengisi data demonstrasi untuk semua 12 stor sekiranya `localStorage` kosong
4. Semua rekod demonstrasi mesti mempunyai `is_demo: true` dan nama aset/lokasi yang jelas berbentuk "DATA CONTOH"
5. Bina fungsi `exportCSV(storName)` yang memuat turun sebarang stor sebagai fail `.csv`
6. Bina fungsi `clearDemoData()` untuk mengalih keluar semua rekod `is_demo: true`
7. Tambah pengesahan asas untuk medan wajib sebelum simpan

### Konteks Relevan
- Semua penyimpanan dilakukan melalui `localStorage.setItem(key, JSON.stringify(data))`
- Setiap stor disimpan sebagai kunci `localStorage` yang berasingan (bukan satu objek besar)
- `generateId` mesti menghasilkan ID unik walaupun rekod ditambah dengan pantas
- Semua tarikh disimpan dalam format ISO 8601 (`YYYY-MM-DD`) untuk pengurutan yang betul

---

## Sub-Tugas 3 — Modul 1: Dashboard Ringkasan Aset

**Status:** `[ ] pending`

### Niat
Bina halaman utama dashboard dengan kad ringkasan dikira automatik, carta agihan status, amaran tertunggak dan jalur aktiviti kalendar. Ini adalah paparan pertama yang dilihat oleh PAJ.

### Hasil Dijangka
- 10 kad ringkasan dipaparkan dan dikemas kini secara dinamik apabila data berubah
- Carta donat menunjukkan agihan status A–F
- Carta bar menunjukkan bilangan aset mengikut bengkel/makmal
- Panel amaran menunjukkan penyelenggaraan tertunggak, laporan kerosakan belum selesai, dan borang tidak lengkap
- Jalur aktiviti bulan semasa disorot pada kalendar ringkasan
- Semua kiraan adalah tepat

### Senarai Tugasan
1. Bina komponen `renderSummaryCards()` yang mengira dan memaparkan 10 kad
2. Bina carta donat Chart.js untuk agihan status (menggunakan `stor_aset`)
3. Bina carta bar Chart.js untuk bilangan aset mengikut bengkel (menggunakan `stor_aset` + `stor_bengkel`)
4. Bina panel amaran: tapis `stor_penyelenggaraan` untuk tertunggak, `stor_kerosakan` untuk belum selesai, `stor_borang` untuk tidak lengkap
5. Bina jalur aktiviti mini: 12 blok bulan, sorotkan bulan semasa, tanda bulan dengan obligasi
6. Bina fungsi `refreshDashboard()` yang dipanggil selepas sebarang perubahan data
7. Tambah pemilih tahun (lalai = tahun semasa) pada bahagian atas Dashboard

### Kiraan Kad
| Kad | Logik |
|---|---|
| Jumlah Aset | `stor_aset.length` |
| Status A | `stor_aset.filter(a => a.status === 'A').length` |
| Status B | `stor_aset.filter(a => a.status === 'B').length` |
| Status C | `stor_aset.filter(a => a.status === 'C').length` |
| Status D | `stor_aset.filter(a => a.status === 'D').length` |
| Status E | `stor_aset.filter(a => a.status === 'E').length` |
| Status F | `stor_aset.filter(a => a.status === 'F').length` |
| Penyelenggaraan Tertunggak | `stor_penyelenggaraan.filter(m => m.tarikh_dirancang < today && m.status !== 'Selesai').length` |
| Laporan Kerosakan Belum Selesai | `stor_kerosakan.filter(k => k.status !== 'Selesai').length` |
| Borang Kualiti Tidak Lengkap | `stor_borang.filter(b => b.status === 'Belum Lengkap').length` |

---

## Sub-Tugas 4 — Modul 2: Pendaftaran Aset

**Status:** `[ ] pending`

### Niat
Bina modul CRUD penuh untuk mendaftar, memapar, mengedit dan memadamkan rekod aset. Ini adalah modul paling penting kerana semua modul lain menggunakan `id_aset` sebagai kunci asing.

### Hasil Dijangka
- Jadual senarai aset dengan carian, penapis bengkel, dan penapis status
- Borang tambah/edit aset dengan pengesahan medan wajib
- Kemaskini status aset menukar nilai dalam `stor_aset` dan menyegarkan semula Dashboard
- Butang eksport CSV mengeksport rekod yang ditapis
- Setiap baris jadual mempunyai butang Lihat / Edit / Hapus

### Senarai Tugasan
1. Bina jadual aset dengan pengurutan lajur dan penomoran halaman (10 rekod setiap halaman)
2. Bina bar carian teks bebas (cari dalam nama, no. pendaftaran, model)
3. Bina penapis: Bengkel (semua/senarai bengkel), Status (semua/A/B/C/D/E/F), Kategori
4. Bina modal tambah/edit aset dengan semua 15 medan dari skema `stor_aset`
5. Bina fungsi pengesahan: medan wajib (nama, no. pendaftaran, bengkel, status) mesti diisi sebelum simpan
6. Bina paparan butiran (modal lihat) yang menunjukkan semua maklumat aset beserta sejarah ringkas
7. Bina amaran pengesahan sebelum padam — kemaskini status ke "F" berbanding memadamkan
8. Sambungkan eksport CSV yang mengeluarkan rekod yang ditapis sahaja

### Konteks Relevan
- Padam tidak boleh memadamkan aset sebenar — cadangkan kemaskini status ke `F` (Hilang) atau `E` (Cadangan Pelupusan)
- Semua dropdown bengkel dan pegawai diisi dari `stor_bengkel` dan `stor_pegawai`

---

## Sub-Tugas 5 — Modul 3: Pemeriksaan Peralatan PdP

**Status:** `[ ] pending`

### Niat
Bina modul untuk menjejak pemeriksaan tahunan Ogos menggunakan PPP-09/FOR/1. Papar status siap pemeriksaan mengikut bengkel/makmal.

### Hasil Dijangka
- Jadual ringkasan menunjukkan semua bengkel dengan status pemeriksaan tahun semasa (Lengkap / Belum Lengkap / Belum Dimulakan)
- Peratusan siap keseluruhan dipaparkan
- Borang tambah rekod pemeriksaan untuk setiap bengkel
- Senarai item pemeriksaan individu (satu baris per aset yang diperiksa)
- Amaran muncul apabila bulan Ogos tiba dan pemeriksaan belum dimulakan

### Senarai Tugasan
1. Bina paparan ringkasan bengkel: jadual dengan lajur Bengkel, PPJ, Tarikh, Peratusan Siap, Status
2. Bina modal tambah rekod pemeriksaan (borang pengepala): pilih bengkel, pilih PPJ, tarikh
3. Bina sub-borang item pemeriksaan: senarai aset dalam bengkel pilihan, dengan medan dapatan dan tindakan bagi setiap aset
4. Bina kalkulator peratusan siap: `item_selesai / jumlah_item * 100`
5. Bina butang "Sahkan PAJ" yang menukar `pengesahan_paj` kepada `true` dan merekod tarikh
6. Tambah penapis tahun (lalai = tahun semasa)
7. Bina amaran: tanda merah jika bulan Ogos dan tiada rekod pemeriksaan bagi tahun semasa

---

## Sub-Tugas 6 — Modul 4: Kerosakan & Status Peralatan

**Status:** `[ ] pending`

### Niat
Bina modul untuk merekod dan menjejak laporan kerosakan, cadangan pembaikan/pelupusan, kelulusan KJ, dan tindakan susulan. Mengaitkan KEW.PA-10 dan PPP-09/FOR/3.

### Hasil Dijangka
- Jadual laporan kerosakan dengan penapis status dan bengkel
- Borang tambah laporan kerosakan dengan semua medan dari skema `stor_kerosakan`
- Kemaskini kelulusan KJ boleh dilakukan dari jadual (butang Lulus/Tolak)
- Merekod laporan kerosakan secara automatik menukar `status` aset berkaitan kepada `C` atau `D`
- Carta mini menunjukkan bilangan kerosakan mengikut bulan (tahun semasa)

### Senarai Tugasan
1. Bina jadual kerosakan dengan lajur: No. Rujukan, Aset, Bengkel, Tarikh, Cadangan, Status, Tindakan
2. Bina modal tambah/edit laporan kerosakan dengan semua medan skema
3. Bina logik auto-kemaskini status aset: jika laporan ditambah dengan cadangan "Baiki", tukar status aset ke `C`; jika "Lupus", tukar ke `E`
4. Bina panel kelulusan KJ: tunjukkan laporan tertangguh, butang Lulus/Tolak dengan medan catatan
5. Bina jadual tindakan susulan: senarai laporan dengan tindakan belum selesai
6. Bina carta bar mini kerosakan mengikut bulan

---

## Sub-Tugas 7 — Modul 5: Keperluan Peralatan

**Status:** `[ ] pending`

### Niat
Bina modul untuk menjejak penyerahan PPP-09/FOR/2 setiap September, semakan PAJ, dan kelulusan KJ.

### Hasil Dijangka
- Jadual keperluan dengan penapis tahun dan bengkel
- Borang tambah keperluan dengan kalkulasi jumlah automatik
- Paparan status semakan PAJ dan KJ
- Amaran jika bulan September dan tiada penyerahan untuk tahun semasa
- Eksport CSV senarai keperluan untuk pembentangan

### Senarai Tugasan
1. Bina jadual keperluan peralatan dengan lajur: No. Rujukan, Bengkel, Peralatan, Kuantiti, Jumlah Anggaran, Semakan PAJ, Semakan KJ, Status
2. Bina modal tambah/edit dengan kalkulasi `jumlah_anggaran = kuantiti * anggaran_harga_seunit` secara automatik
3. Bina panel semakan: PAJ dan KJ boleh kemaskini status semakan masing-masing
4. Tambah ringkasan: jumlah nilai keperluan mengikut bengkel, tahun
5. Bina amaran September: tanda jika September tiba dan tiada rekod untuk tahun semasa

---

## Sub-Tugas 8 — Modul 6: Penyelenggaraan Berjadual

**Status:** `[ ] pending`

### Niat
Bina modul untuk menjejak jadual penyelenggaraan tahunan (PPP-09/FOR/5) yang disediakan setiap Januari, termasuk rekod KEW.PA-15 dan KEW.PA-16.

### Hasil Dijangka
- Jadual penyelenggaraan dengan penapis status, bengkel dan bulan
- Baris tertunggak disorot dalam warna merah
- Kemaskini tarikh sebenar dan status boleh dilakukan terus dari jadual
- Carta garis menunjukkan rancangan vs. sebenar mengikut bulan
- Amaran Januari: tanda jika Januari tiba dan FOR/5 belum disediakan

### Senarai Tugasan
1. Bina jadual penyelenggaraan dengan penapis dan pengurutan tarikh
2. Sorot baris tertunggak (merah), akan datang dalam 14 hari (amber), selesai (hijau)
3. Bina modal tambah/edit dengan semua medan skema `stor_penyelenggaraan`
4. Bina kemaskini cepat (quick update): butang "Tandakan Selesai" terus dari baris jadual
5. Bina carta garis: penyelenggaraan dirancang vs. diselesaikan mengikut bulan
6. Bina amaran Januari: notifikasi jika tiada rekod FOR/5 untuk tahun semasa

---

## Sub-Tugas 9 — Modul 7: Pergerakan Peralatan

**Status:** `[ ] pending`

### Niat
Bina modul untuk menjejak peralatan yang dihantar keluar PSAS menggunakan PPP-09/FOR/4, dari tarikh keluar hingga pengesahan penerimaan balik.

### Hasil Dijangka
- Jadual pergerakan dengan penapis status (Keluar / Pulang / Tertunggak)
- Peralatan bertanda "Keluar" melebihi tarikh jangka pulang disorot merah
- Kemaskini penerimaan balik boleh dilakukan dengan satu butang
- Merekod penerimaan balik secara automatik menukar status aset kepada nilai sesuai

### Senarai Tugasan
1. Bina jadual pergerakan dengan lajur: No. Rujukan, Aset, Pembekal, Tarikh Keluar, Jangka Pulang, Status
2. Sorot baris tertunggak (tarikh jangka pulang < hari ini dan status "Keluar") dengan warna merah
3. Bina modal tambah/edit pergerakan
4. Bina borang penerimaan balik: tarikh sebenar pulang, keadaan semasa pulang, pengesah
5. Kemaskini status aset secara automatik apabila peralatan diterima balik

---

## Sub-Tugas 10 — Modul 8: Pemantauan Dokumen Kualiti

**Status:** `[ ] pending`

### Niat
Bina matriks pematuhan yang memaparkan status semua 11 borang wajib mengikut bengkel dan tahun, membolehkan PAJ memantau kelengkapan dokumen untuk tujuan audit.

### Hasil Dijangka
- Matriks jadual: baris = borang, lajur = bengkel/makmal, sel = status (✓ / ✗ / N/A)
- Peratusan pematuhan keseluruhan dipaparkan
- Rekod boleh ditambah/dikemas kini terus dari matriks
- Penapis tahun tersedia
- Amaran muncul untuk borang dengan tarikh wajib yang hampir

### Senarai Tugasan
1. Bina jadual matriks: 11 borang × n bengkel dengan sel berkod warna
2. Bina logik pengisian matriks dari `stor_borang`
3. Klik pada sel membuka modal kemaskini status borang tersebut
4. Bina bar kemajuan pematuhan keseluruhan (peratusan sel "Lengkap")
5. Bina amaran deadline: tandakan borang yang tarikh wajibnya dalam tempoh 30 hari
6. Tambah ringkasan borang wajib mengikut bulan dengan pautan ke aktiviti berkaitan

---

## Sub-Tugas 11 — Modul 9: Kalendar Pemantauan Tahunan

**Status:** `[ ] pending`

### Niat
Bina paparan kalendar 12 bulan visual yang memaparkan semua aktiviti pemantauan wajib, tarikh akhir, dan tugas berkaitan prosedur.

### Hasil Dijangka
- Grid 12 bulan menunjukkan aktiviti wajib berkod warna
- Bulan semasa disorot
- Mengklik aktiviti menavigasi ke modul berkaitan
- Aktiviti tertunggak ditanda dengan ikon amaran
- Cetak kalendar menghasilkan output mesra cetak

### Senarai Tugasan
1. Bina grid 12 kotak (Jan–Dis) dengan senarai aktiviti dalam setiap kotak
2. Kodkan warna mengikut jenis: biru (perancangan), hijau (pemeriksaan), amber (laporan), merah (semakan)
3. Tambah ikon amaran pada bulan dengan aktiviti tertunggak
4. Klik pada nama aktiviti menavigasi ke modul berkaitan
5. Bina paparan senarai alternatif (jadual) untuk mudah dicetak

### Aktiviti Wajib
| Bulan | Aktiviti | Modul Berkaitan |
|---|---|---|
| Januari | Sediakan PPP-09/FOR/5, kemaskini KEW.PA-15/16 | Modul 6 |
| Januari, April, Julai, Oktober | Laporan Prestasi Suku Tahun | Modul 8 |
| Ogos | Pemeriksaan Peralatan PdP (PPP-09/FOR/1) | Modul 3 |
| September | Kemukakan PPP-09/FOR/2, Laporan Status Pemeriksaan | Modul 5 |
| Oktober | Semakan Pengurusan Status Peralatan | Modul 8 |
| Sepanjang Tahun | Laporan Kerosakan, Kemaskini Aset, Baik Pulih, Pelupusan | Modul 4, 2, 6 |

---

## Sub-Tugas 12 — Modul 10: Laporan & Analitik

**Status:** `[ ] pending`

### Niat
Bina modul pelaporan komprehensif dengan penapis, carta, jadual boleh eksport, dan ringkasan eksekutif untuk PAJ dan KJ.

### Hasil Dijangka
- 8 jenis laporan tersedia dengan penapis tarikh dan bengkel
- Setiap laporan boleh dieksport sebagai CSV
- Butang cetak menghasilkan output mesra cetak A4
- Carta analitik visual untuk setiap laporan
- Ringkasan eksekutif merangkumi semua modul dalam satu paparan

### Laporan Yang Dirancang
| No | Nama Laporan | Sumber Data | Penapis |
|---|---|---|---|
| L1 | Laporan Status Aset | `stor_aset` | Bengkel, Status, Kategori |
| L2 | Laporan Pemeriksaan Peralatan | `stor_pemeriksaan` | Tahun, Bengkel, Status |
| L3 | Laporan Peralatan Rosak | `stor_kerosakan` | Tarikh, Bengkel, Cadangan |
| L4 | Laporan Penyelenggaraan | `stor_penyelenggaraan` | Tahun, Bengkel, Status |
| L5 | Laporan Keperluan Peralatan | `stor_keperluan` | Tahun, Bengkel |
| L6 | Laporan Pelupusan | `stor_pelupusan` | Status, Tarikh |
| L7 | Laporan Pematuhan Borang Kualiti | `stor_borang` | Tahun, Bengkel |
| L8 | Ringkasan Eksekutif | Semua stor | Tahun |

### Senarai Tugasan
1. Bina pengendali pilihan laporan: radio button atau dropdown pilih jenis laporan
2. Untuk setiap laporan: bina fungsi penapis, fungsi render jadual, fungsi eksport CSV
3. Bina carta Chart.js untuk laporan L1 (pai status), L3 (bar kerosakan bulanan), L4 (bar penyelenggaraan)
4. Bina Ringkasan Eksekutif (L8): paparan satu halaman merangkumi semua KPI utama
5. Tambah fungsi cetak laporan dengan `window.print()` — lajur tidak diperlukan disembunyikan dalam CSS cetak

---

## Sub-Tugas 13 — Ujian & Pengesahan

**Status:** `[ ] pending`

### Niat
Mengesahkan bahawa semua fungsi utama berfungsi dengan betul sebelum penghantaran. Ujian dilakukan secara manual dalam pelayar.

### Senarai Kes Ujian

#### Ujian Data & Kiraan
- [ ] T01: Jumlah kad "Jumlah Aset" sepadan dengan bilangan rekod dalam `stor_aset`
- [ ] T02: Kiraan setiap status (A–F) adalah tepat selepas menambah rekod berstatus yang berbeza
- [ ] T03: Menambah rekod penyelenggaraan tertunggak menambah kiraan kad "Penyelenggaraan Tertunggak"
- [ ] T04: Menambah laporan kerosakan dengan status "Terbuka" menambah kiraan "Laporan Kerosakan Belum Selesai"

#### Ujian CRUD Aset
- [ ] T05: Menambah aset baru muncul dalam jadual Modul 2 dan dikira dalam Modul 1
- [ ] T06: Mengedit status aset mengemas kini kad ringkasan dalam Modul 1
- [ ] T07: Pengesahan borang menghalang penyimpanan aset tanpa medan wajib
- [ ] T08: Carian teks menemui aset mengikut nama dan nombor pendaftaran

#### Ujian Modul Spesifik
- [ ] T09: Peratusan siap pemeriksaan (Modul 3) dikira dengan betul
- [ ] T10: Merekod laporan kerosakan dengan cadangan "Baiki" menukar status aset ke `C`
- [ ] T11: Kalkulasi `jumlah_anggaran = kuantiti × harga_seunit` adalah automatik (Modul 5)
- [ ] T12: Baris penyelenggaraan tertunggak disorot merah (Modul 6)
- [ ] T13: Baris pergerakan tertunggak melebihi tarikh jangka pulang disorot merah (Modul 7)
- [ ] T14: Matriks pematuhan borang menunjukkan ✓ selepas status dikemas kini kepada "Lengkap"

#### Ujian Sistem
- [ ] T15: Menapis mengikut bengkel menunjukkan aset bengkel tersebut sahaja
- [ ] T16: Eksport CSV menghasilkan fail `.csv` yang boleh dibuka dalam Excel
- [ ] T17: Menutup dan membuka semula pelayar mengekalkan semua data (keterusan localStorage)
- [ ] T18: Dashboard dipaparkan dengan betul pada skrin 1366×768 (desktop) dan 768×1024 (tablet)
- [ ] T19: Butang cetak menghasilkan output mesra cetak tanpa sidebar
- [ ] T20: "Padam" rekod demo berfungsi melalui fungsi `clearDemoData()`

### Kriteria Penerimaan
- Semua 20 kes ujian lulus tanpa ralat konsol JavaScript
- Semua label dalam Bahasa Melayu
- Tiada label "DATA CONTOH" yang hilang pada rekod demonstrasi
- Carta dipaparkan dengan betul pada Modul 1 dan 10
- Fail HTML boleh disalin ke komputer lain dan berfungsi tanpa sambungan internet (kecuali CDN)
- Fail HTML saiznya tidak melebihi 500KB

---

## Ringkasan Pelan Pelaksanaan

```
Sub-Tugas  Perkara                           Keutamaan
---------  --------------------------------  ----------
ST-1       Struktur Fail & Navigasi          Kritikal
ST-2       Storan Data & Skema               Kritikal
ST-3       Modul 1: Dashboard Ringkasan      Tinggi
ST-4       Modul 2: Pendaftaran Aset         Tinggi
ST-5       Modul 3: Pemeriksaan PdP          Tinggi
ST-6       Modul 4: Kerosakan & Status       Tinggi
ST-7       Modul 5: Keperluan Peralatan      Sederhana
ST-8       Modul 6: Penyelenggaraan          Sederhana
ST-9       Modul 7: Pergerakan Peralatan     Sederhana
ST-10      Modul 8: Dokumen Kualiti          Sederhana
ST-11      Modul 9: Kalendar Tahunan         Rendah
ST-12      Modul 10: Laporan & Analitik      Sederhana
ST-13      Ujian & Pengesahan                Kritikal
```

---

## Nota Penting

1. **Dashboard bukan borang rasmi:** Rekod dalam dashboard adalah alat pemantauan sahaja. Borang kerajaan rasmi (KEW.PA-7, KEW.PA-10, dll.) mesti disediakan secara berasingan mengikut Pekeliling Perbendaharaan.
2. **Data Contoh:** Semua rekod demonstrasi mempunyai medan `is_demo: true` dan label yang jelas.
3. **Batasan localStorage:** Data hanya disimpan dalam pelayar semasa. Untuk sandaran, gunakan fungsi eksport CSV secara berkala.
4. **Pengesahan item belum disahkan:** Lihat seksyen "Item Perlu Pengesahan" dalam analisis sebelumnya.
