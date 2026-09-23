# Mini Project: Analisis Data Cryptocurrency dengan CoinMarketCap API

Mini project ini mengambil, membersihkan, dan menyimpan data cryptocurrency dari CoinMarketCap API menggunakan Python.

## Daftar Isi
- [Gambaran Umum](#gambaran-umum)
- [API yang Digunakan](#api-yang-digunakan)
- [Struktur Project](#struktur-project)
- [Struktur Program (OOP)](#struktur-program-oop)
- [Alur Kerja](#alur-kerja)
- [Data Cleaning](#data-cleaning)
- [Hasil Akhir](#hasil-akhir)
- [Ringkasan](#ringkasan)
- [Menyesuaikan untuk API Lain](#menyesuaikan-untuk-api-lain)

## Gambaran Umum

Project ini terdiri dari beberapa tahap:
1. Mendapatkan API key dari CoinMarketCap
2. Mencoba memanggil API secara langsung
3. Membungkus logika pengambilan data ke dalam sebuah class
4. Membersihkan dan mengubah tipe data
5. Menyimpan hasil ke file CSV

## API yang Digunakan

- **Nama API:** CoinMarketCap API
- **Endpoint:** `https://pro-api.coinmarketcap.com/v3/cryptocurrency/listings/latest`
- **Autentikasi:** API key dikirim lewat header `X-CMC_PRO_API_KEY`
- **Alasan pemilihan:** _(belum ditulis di notebook — isi alasan lo di sini, misalnya: data lengkap & real-time, mendukung banyak mata uang sekaligus, dokumentasi jelas, cocok untuk skala mini project, dll.)_

API key disimpan di file `.env` dan dimuat dengan `python-dotenv`, tidak ditulis langsung di kode.

## Struktur Project

```
.
├── bismillah_mp_denn.ipynb   # Notebook utama
├── .env                      # Berisi COIN_API_KEY (tidak di-commit)
├── dataset_kripto.csv        # Output data yang sudah dibersihkan
└── README.md
```

## Struktur Program (OOP)

Logika pengambilan data dibungkus dalam satu class agar mudah dipakai ulang untuk mata uang yang berbeda.

**Class `KoinKripto`**

Atribut:
- `api_key` — API key CoinMarketCap
- `alamat_api` — endpoint API yang dipanggil
- `header` — header HTTP (`Accepts`, `X-CMC_PRO_API_KEY`)

Method:
- `ambil_koin(jumlah=15, mata_uang="IDR")` — mengambil data koin sebanyak `jumlah`, dikonversi ke `mata_uang` tertentu, lalu mengembalikan hasilnya sebagai `pandas.DataFrame`. Method ini juga punya penanganan error sederhana: jika koneksi gagal, dicoba ulang sekali setelah jeda 3 detik.

## Alur Kerja

1. **Ambil data** — memanggil `ambil_koin()` untuk tiga mata uang: `USD`, `IDR`, `EUR`, masing-masing 80 koin, lalu digabung jadi satu `DataFrame` (`df_koin`).
2. **Cek kualitas data** — memeriksa jumlah sel kosong, baris duplikat (berdasarkan `Nama` + `Mata Uang`), dan tipe data tiap kolom.
3. **Perbaiki tipe data** — kolom `Update Terakhir` yang awalnya berupa string diubah menjadi `datetime`, lalu dikonversi dari UTC ke UTC+7 (WIB).
4. **Simpan data** — hasil akhir disimpan ke `dataset_kripto.csv`, lalu dibaca ulang untuk verifikasi.

## Data Cleaning

Pemeriksaan yang dilakukan:
- Jumlah sel kosong per kolom
- Jumlah baris kembar (berdasarkan `Nama` dan `Mata Uang`)
- Tipe data tiap kolom

Hasil pemeriksaan pada data yang dikumpulkan: **0 sel kosong** dan **0 baris duplikat**.

**Keputusan yang diambil:** _(bagian ini asumsi berdasarkan angka di atas — cek dan sesuaikan)_ karena tidak ditemukan data kosong maupun duplikat, seluruh baris dipertahankan tanpa proses penghapusan atau pengisian nilai. Satu-satunya perubahan yang dilakukan adalah pada tipe data kolom `Update Terakhir`, dari string menjadi `datetime` (UTC → WIB), karena format aslinya tidak bisa langsung dipakai untuk analisis waktu.

## Hasil Akhir

- Data dari 3 mata uang (USD, IDR, EUR) × 80 koin per mata uang
- Disimpan sebagai `dataset_kripto.csv`
- Kolom yang tersedia: `Nama`, `Simbol`, `Peringkat`, `Mata Uang`, `Harga`, `Market Cap`, `Perubahan 24j`, `Update Terakhir`

_(Sisipkan screenshot `df_koin.head()` atau cuplikan isi `dataset_kripto.csv` di sini sebagai bukti visual hasil akhir.)_

## Ringkasan

| Aspek | Ada di bagian |
|---|---|
| Pengambilan data lewat API | Bagian 2 & 3 |
| Struktur OOP (class `KoinKripto`) | Bagian 3 |
| Function & modularitas (`ambil_koin`) | Bagian 3 |
| Pengambilan data multi-mata uang (USD, IDR, EUR) | Bagian 3 |
| Data cleaning & pengecekan null/duplikat | Bagian 4 |
| Perubahan tipe data tanggal & konversi UTC ke WIB | Bagian 4 |
| Penyimpanan data (`dataset_kripto.csv`) | Bagian 5 |

## Menyesuaikan untuk API Lain

Kalau mau memakai project ini sebagai template untuk API lain, tiga hal yang perlu disesuaikan:

1. **Alamat API dan parameter** di dalam `ambil_koin` — ganti dengan endpoint API pilihan dan parameter sesuai dokumentasinya.
2. **Nama dan struktur data** yang diambil dari respons API — sesuaikan dengan skema data API tersebut.
3. **Metode autentikasi** — sesuaikan bagian `header` atau parameter jika API menggunakan token/API key dengan cara berbeda.

Bagian lain (pembuatan class, penggabungan data, data cleaning, perubahan tipe data, dan penyimpanan file) polanya bisa dipakai ulang untuk API apa pun.
