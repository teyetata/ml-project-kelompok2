# Klasterisasi Kotamadya di Italia Berdasarkan Profil Pengelolaan Limbah dan Struktur Biaya Menggunakan Pendekatan Unsupervised Learning

Repository: https://github.com/teyetata/ml-project-kelompok2

Repository ini berisi seluruh artefak **UTS Machine Learning (Pembelajaran Mesin) Milestone 1**, Semester Ganjil TA 2026-2027.

## Identitas Kelompok

| Keterangan | Isi |
|---|---|
| Project ID | **[ISI PROJECT ID KELOMPOK]** |
| Nama kelompok | Kelompok 2 |
| Kelas | IF-B Pagi |
| Mata kuliah | Pembelajaran Mesin |
| Tahap | UTS, Milestone 1 |

### Anggota

| Nama | NIM |
|---|---|
| Alvin | 231113040 |
| Wilmer Tanton | 231112849 |
| Teresia Hana Agatha Siburian | 231112494 |

## Deskripsi Masalah

Data pengelolaan sampah dari 4.341 wilayah di Italia memiliki banyak masalah kualitas: distribusi yang sangat ekstrem dan timpang (volume sampah dan populasi didominasi kota besar), data hilang dengan pola tertentu (misalnya data sampah kayu yang 97% kosong di region Molise), dan ketidakkonsistenan pelaporan (total sampah kadang lebih besar dari gabungan sampah terpilah dan tak terpilah pada 1.039 baris). Jika data mentah langsung dimasukkan ke model, algoritma akan mengelompokkan kota berdasarkan besar wilayah atau populasinya, bukan berdasarkan perilaku pengelolaan sampahnya.

- **Tujuan:** mengelompokkan (clustering) kota/kabupaten di Italia berdasarkan profil limbah dan struktur biayanya untuk menemukan pola pengelolaan yang tersembunyi dari data tabular, serta membangun alur pra-pemrosesan yang aman dari outlier (per kapita, imputasi berjenjang, winsorizing P1-P99, log1p, standardisasi).
- **Target:** klaster wilayah dari 11 fitur: `tc`, `cres`, `csor` (biaya), `msw_pc` (sampah per kapita), `sor` (tingkat pemilahan), serta `organic`, `paper`, `glass`, `plastic`, `metal`, `other` (komposisi). Klaster kemudian divalidasi dan diprofilkan dengan variabel yang tidak ikut jarak (`geo`, `urb_rev`, `size_class`, `d_fee`).
- **Pendekatan:** unsupervised learning (clustering) dengan classical machine learning. Baseline **K-Means (k = 3)**, dibandingkan dengan **GMM**, **Agglomerative Ward**, dan **DBSCAN**.
- **Validasi:** hold-out 80:20 (3.450 train, 863 test, stratified menurut `region_impute`, seed 42) dan 5-fold CV di dalam train. Data test tidak dipakai pada Milestone 1.

## Ringkasan Hasil Awal

Validasi 5-fold CV pada fold validasi (k = 3; rata-rata ± simpangan baku antar-fold), sesuai Tabel 3.1 laporan:

| Model | Silhouette ↑ | % silhouette < 0 ↓ | Davies-Bouldin ↓ | Rasio B/W ↑ | Jumlah klaster; % noise |
|---|---|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | 0,371 ± 0,018 | 3; 0,0 |
| Kandidat 1: GMM (full) | 0,080 ± 0,007 | 40,1 ± 2,5 | 2,768 ± 0,102 | 0,222 ± 0,013 | 3; 0,0 |
| Kandidat 2: Ward | 0,092 ± 0,013 | 32,4 ± 10,1 | 2,581 ± 0,199 | 0,247 ± 0,018 | 3; 0,0 |
| Kandidat 3: DBSCAN | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | 1; 1,7 |

Pada k = 3 baseline unggul, tetapi silhouette 0,155 masih di bawah 0,25 (struktur lemah). Penyapuan k = 2 sampai 8 dengan aturan pemilihan tetap memilih k = 2 untuk ketiga model (K-Means k = 2: silhouette 0,202), namun kriteria lain menunjuk k berbeda sehingga penentuan k final menjadi agenda UAS. Seluruh tabel, temuan, dan keterbatasan ada di [`results/hasil_eksperimen_awal.md`](results/hasil_eksperimen_awal.md).

## Struktur Repository

```
.
├── README.md                          # dokumen ini
├── requirements.txt                   # daftar library
├── notebooks/
│   └── ML_final.ipynb                 # notebook eksperimen lengkap (EDA, preprocessing, baseline, kandidat, sapuan)
├── laporan/
│   ├── UTS_ML_IF-B_Pagi_Kelompok_2.pdf          # laporan Milestone 1 (PDF)
│   └── UTS_ML_IF-B_Pagi_Kelompok_2_final.docx   # berkas sumber laporan
├── data/
│   └── README.md                      # cara memperoleh dataset
├── docs/
│   ├── dataset_source.md              # dokumentasi sumber, lisensi, dan struktur dataset
│   ├── ringkasan_eda_preprocessing.md # ringkasan EDA, preprocessing, anti-leakage, feature engineering
│   ├── experiment_log.md              # catatan pelacakan eksperimen (Tabel 3.8)
│   ├── reproduksibilitas.md           # reproduksibilitas (Subbab 3.2.2)
│   ├── rencana_pasca_uts.md           # rencana menuju UAS (Subbab 3.2.3)
│   ├── model_card.md                  # model card baseline K-Means (pelengkap)
│   └── penelusuran_laporan_notebook.md# pemetaan laporan ke notebook dan berkas hasil
└── results/
    ├── hasil_eksperimen_awal.md       # hasil, temuan, dan keterbatasan (Bab III)
    ├── tabel_3_1_...csv sampai tabel_3_7_...csv  # tabel hasil dalam format CSV
    └── figures/                       # gambar keluaran notebook
```

### Pemetaan terhadap ketentuan pengumpulan

| Ketentuan | Lokasi |
|---|---|
| README (Project ID, kelompok, anggota, masalah, petunjuk) | `README.md` |
| Notebook atau kode eksperimen | `notebooks/ML_final.ipynb` |
| Dokumentasi sumber dataset | `docs/dataset_source.md`, `data/README.md` |
| Hasil eksperimen awal (baseline, kandidat, validasi, metrik, hasil) | `results/` |
| Rencana pengembangan pasca-UTS | `docs/rencana_pasca_uts.md` |
| Laporan Milestone 1 (PDF) | `laporan/UTS_ML_IF-B_Pagi_Kelompok_2.pdf` |
| File pendukung lain | `docs/`, `requirements.txt` |

## Petunjuk Penggunaan

1. **Ambil dataset.** Unduh dari tautan pada [`data/README.md`](data/README.md) dan simpan sebagai `public_data_waste_fee.csv`.
2. **Siapkan lingkungan.** Python 3 dengan library pada `requirements.txt`:
   ```bash
   pip install -r requirements.txt
   ```
3. **Atur lokasi data.** Notebook membaca dari `DATA_PATH = "/content/public_data_waste_fee.csv"` (jalur Google Colab) pada sel Bagian 0. Jika dijalankan di komputer sendiri, ubah variabel `DATA_PATH` ke lokasi berkas Anda.
4. **Jalankan notebook** `notebooks/ML_final.ipynb` dari atas ke bawah (Restart & Run All). Urutan bagian notebook:

   | Bagian | Isi |
   |---|---|
   | 0 sampai 2 | Persiapan, struktur data, statistik dasar |
   | 3 | EDA dan pengujian hipotesis |
   | 4 | Preprocessing, feature engineering, split, dan fold |
   | 5 | Baseline K-Means |
   | 6 | Model kandidat (GMM, Ward, DBSCAN) |
   | 7 | Sapuan k, grid DBSCAN, dan konfigurasi terpilih |

5. **Keterulangan.** `SEED = 42`, `TEST_SIZE = 0,20`, 5-fold CV, `n_init = 10`. Notebook menyimpan beberapa berkas CSV (misalnya `baseline_split_istat.csv`, `baseline_folds_istat.csv`, `baseline_ref.csv`) ke folder kerja saat dijalankan.

> Catatan: angka pada README dan folder `results/` mengikuti laporan final dan keluaran notebook ini. Versi library yang berbeda dapat menggeser angka sedikit; jika itu terjadi, angka di laporan harus mengikuti hasil eksekusi terbaru.

## Sumber Dataset

Di Foggia, Giacomo (2022), *Municipal waste management cost and fee schemes*, Bicocca Open Archive Research Data, V1, doi: 10.17632/w5f9kg7743.1. Lisensi CC BY 4.0. Tautan: https://board.unimib.it/datasets/w5f9kg7743/1. Detail pada [`docs/dataset_source.md`](docs/dataset_source.md).

## Rencana Lanjutan

Rencana pekerjaan setelah UTS menuju final project (UAS) ada pada [`docs/rencana_pasca_uts.md`](docs/rencana_pasca_uts.md).

## Integritas Akademik

Seluruh angka evaluasi pada repository ini berasal dari eksekusi notebook. Penggunaan AI sebagai alat bantu: **[ISI PERNYATAAN KELOMPOK]**.
