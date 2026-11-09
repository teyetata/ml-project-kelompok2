# Klasterisasi Kotamadya di Italia Berdasarkan Profil Pengelolaan Limbah dan Struktur Biaya Menggunakan Pendekatan Unsupervised Learning

Repository: https://github.com/teyetata/ml-project-kelompok2

Repository ini berisi seluruh artefak **UTS Machine Learning (Pembelajaran Mesin) Milestone 1**, Semester Ganjil TA 2026-2027.

## Identitas Kelompok

| Keterangan | Isi |
|---|---|
| Project ID | **[B06]** |
| Nama kelompok | Kelompok 2 |
| Mata kuliah | Pembelajaran Mesin (Machine Learning) |
| Tahap | UTS, Milestone 1 |

### Anggota

| Nama | NIM |
|---|---|
| Alvin | 231113040 |
| Wilmer Tanton | 231112849 |
| Teresia Hana Agatha Siburian | 231112494 |

## Deskripsi Masalah

Data pengelolaan sampah dari 4.341 kota/kabupaten (municipality) di Italia dicatat dengan skala yang sangat berbeda antar fitur, banyak nilai hilang dengan pola tertentu, serta beberapa ketidakkonsistenan pelaporan (misalnya `msw` tidak sama dengan `msw_so + msw_un` pada 1.039 baris). Karena tidak ada label, proyek ini memakai **unsupervised learning (clustering)** untuk mengelompokkan kota/kabupaten berdasarkan profil sampah dan struktur biayanya.

- **Tujuan:** menemukan pola pengelolaan sampah dan membangun alur preprocessing yang aman dari outlier dan data leakage.
- **Target:** cluster wilayah berdasarkan 11 fitur: `tc`, `cres`, `csor` (biaya), `msw_pc` (sampah per kapita), `sor` (tingkat pemilahan), serta `organic`, `paper`, `glass`, `plastic`, `metal`, `other` (komposisi).
- **Pendekatan:** classical machine learning. Baseline **K-Means (k = 3)**, dibandingkan dengan kandidat **GMM**, **Agglomerative Ward**, dan **DBSCAN**.
- **Validasi:** split 80:20 (3.450 train, 863 test, stratified menurut `region_impute`, seed 42) dan 5-fold CV di dalam train. Data test tidak dipakai untuk memilih model.

## Ringkasan Hasil Awal

Hasil validasi 5-fold CV pada data train (k = 3; rata-rata ± simpangan baku antar-fold):

| Model | Silhouette ↑ | % silhouette < 0 ↓ | Davies-Bouldin ↓ | Rasio B/W ↑ |
|---|---|---|---|---|
| Baseline: K-Means | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | 0,371 ± 0,018 |
| GMM (full) | 0,080 ± 0,007 | 40,1 ± 2,5 | 2,768 ± 0,102 | 0,222 ± 0,013 |
| Agglomerative (Ward) | 0,092 ± 0,013 | 32,4 ± 10,1 | 2,581 ± 0,199 | 0,247 ± 0,018 |
| DBSCAN (eps 2,737; min_samples 22) | tidak terdefinisi (1 cluster, 1,7% noise) | - | - | - |

Pada k = 3 tidak ada kandidat yang mengungguli baseline, dan pemisahan cluster tergolong lemah (silhouette di bawah 0,25), yang konsisten dengan temuan EDA bahwa data lebih mirip awan kontinu. Rincian, analisis, dan keterbatasan ada di [`results/hasil_eksperimen_awal.md`](results/hasil_eksperimen_awal.md).

## Struktur Repository

```
.
├── README.md                      # dokumen ini
├── requirements.txt               # daftar library
├── notebooks/
│   └── ML_final.ipynb             # notebook eksperimen lengkap (EDA, preprocessing, baseline, kandidat)
├── data/
│   └── README.md                  # cara memperoleh dataset (berkas CSV tidak disertakan)
├── docs/
│   ├── dataset_source.md          # dokumentasi sumber, lisensi, dan struktur dataset
│   ├── experiment_log.md          # catatan eksperimen (experiment tracking log)
│   ├── model_card.md              # model card baseline K-Means
│   └── rencana_pasca_uts.md       # rencana pekerjaan menuju UAS
└── results/
    ├── hasil_eksperimen_awal.md   # tabel hasil, analisis, keterbatasan
    ├── tabel_hasil_awal.csv       # tabel hasil baseline dan kandidat (k = 3)
    ├── tabel_sapuan_k_awal.csv    # hasil eksplorasi awal sensitivitas k
    └── figures/                   # gambar keluaran notebook
```

## Petunjuk Penggunaan

1. **Ambil dataset.** Unduh dari tautan pada [`data/README.md`](data/README.md) dan simpan sebagai `public_data_waste_fee.csv`.
2. **Siapkan lingkungan.** Python 3.12 dengan library pada `requirements.txt`:
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
   | 7 | Eksplorasi sapuan k dan grid DBSCAN (sensitivitas awal) |

5. **Keterulangan.** `SEED = 42`, `TEST_SIZE = 0,20`, 5-fold CV, `n_init = 10`. Notebook menyimpan beberapa berkas CSV (misalnya `baseline_ref.csv`, `kandidat_perbandingan.csv`, `sapuan_perbandingan.csv`) ke folder kerja saat dijalankan.

> Catatan: angka pada README dan folder `results/` disalin dari keluaran sel notebook ini. Versi library yang berbeda dapat menggeser angka sedikit; jika itu terjadi, angka di laporan harus mengikuti hasil eksekusi terbaru.

## Sumber Dataset

Di Foggia, Giacomo (2022), *Municipal waste management cost and fee schemes*, Bicocca Open Archive Research Data, V1, doi: 10.17632/w5f9kg7743.1. Lisensi CC BY 4.0. Detail pada [`docs/dataset_source.md`](docs/dataset_source.md).

## Rencana Lanjutan

Rencana pekerjaan setelah UTS menuju final project (UAS) ada pada [`docs/rencana_pasca_uts.md`](docs/rencana_pasca_uts.md).

## Integritas Akademik

Seluruh angka evaluasi pada repository ini berasal dari eksekusi notebook. Penggunaan AI sebagai alat bantu: **[ISI PERNYATAAN KELOMPOK]**.
