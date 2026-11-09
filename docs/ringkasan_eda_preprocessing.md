# Ringkasan EDA, Preprocessing, dan Feature Engineering

Ringkasan ini mengikuti Bab I laporan dan notebook bagian 3 sampai 4 (`../notebooks/ML_final.ipynb`). Rincian lengkap dan seluruh keluaran sel ada di notebook.

## 1. Temuan EDA

| No | Temuan | Bukti | Implikasi |
|---|---|---|---|
| 1 | Distribusi sangat miring dan skala antar fitur berbeda | Skewness `pop` 33,0, `msw` 36,8, `roads` 33,0; median `pop` 3.535 vs maksimum 2.617.175 | Algoritma berbasis jarak akan didominasi kota besar dan fitur berskala besar bila dipakai apa adanya; volume sampah dibaca per kapita |
| 2 | Kolom ukuran kota saling berkorelasi tinggi | `pop`, `msw`, `msw_so`, `msw_un` berkorelasi 0,83 sampai 0,98 | Satu konsep (ukuran kota) terwakili banyak kolom sehingga bobotnya berlipat dalam jarak |
| 3 | Kategori tidak seimbang | Lombardia 28,3% baris; PAYT 12,8% dan 93% baris PAYT yang punya `geo` berada di `geo = 3` | Cluster mudah didominasi pola Lombardia; perbandingan PAYT vs STANDARD tercampur dengan geografi |
| 4 | Tidak tampak struktur cluster yang jelas | PCA pada 11 variabel (3.441 baris): tiga komponen memuat sekitar 55 sampai 57% varians; Hopkins 0,90 (z-score) vs 0,60 (rank) | Data lebih mirip awan kontinu; hasil clustering perlu divalidasi, Hopkins tidak konklusif |

## 2. Masalah kualitas data dan penanganannya

- Nilai hilang berpola (misalnya `wood` 97% kosong di Molise): `wood` dan `texile` tidak dipakai sebagai fitur karena porsi kosongnya melebihi 23%.
- Ketidakkonsistenan pelaporan: `msw` vs `msw_so + msw_un` (1.039 baris) dan `sor` vs jumlah komposisi dipertahankan apa adanya dan dicatat sebagai keterbatasan, karena penyebabnya belum diketahui dan selisihnya terstruktur per region.
- Setelah pembuangan baris tidak valid tersisa 4.313 baris.

## 3. Langkah preprocessing

1. Perbaikan data dan aturan per baris (koreksi satuan, pembuangan baris tidak valid, `msw_pc`, `gap_flag`, `size_class`), dikerjakan sebelum split karena tidak bergantung pada baris lain.
2. Split 80:20 (`test_size = 0,20`, `random_state = 42`, stratified menurut `region_impute`): 3.450 train dan 863 test.
3. Imputasi median bertingkat (provinsi, lalu `region_impute`, lalu nasional) untuk 10 kolom: `organic`, `metal`, `other`, `paper`, `glass`, `plastic`, `cres`, `csor`, `tc`, `sor`.
4. `log1p` pada lima fitur bersebaran miring, winsorizing P1 sampai P99, lalu standardisasi z-score pada 11 fitur.

## 4. Pencegahan data leakage

- Split dilakukan sebelum langkah yang menghitung statistik; imputasi, winsorizing, dan z-score dikerjakan sesudah split.
- Seluruh parameter preprocessing di-fit hanya pada train; test hanya ditransformasi.
- Pada 5-fold CV, preprocessing di-fit ulang pada empat fold latih di setiap iterasi; fold dibuat sekali, disimpan, dan dibekukan sebelum ada hasil model.
- Data test tidak dipakai untuk memilih fitur, model, atau hyperparameter.
- Diagnostik: preprocessing yang sama di-fit pada seluruh data hanya sebagai pembanding (selisih mean terbesar 0,013 simpangan baku; selisih batas P99 terbesar 0,132 pada `paper`), dan tidak dipakai untuk model.

## 5. Feature engineering awal

| No | Fitur | Rumus | Peran |
|---|---|---|---|
| 1 | `msw_pc` | `msw / pop` (kg per jiwa) | Masuk 11 fitur clustering; skewness pada train turun dari 3,08 menjadi 0,21 (setelah log1p) dan 0,09 (setelah winsorizing) |
| 2 | `region_impute` | `region` dirapikan; Valle d'Aosta digabung ke Piemonte | Kelompok imputasi dan dasar stratifikasi; selisih proporsi region train vs test terbesar 0,0006 |
| 3 | `urb_rev` | `4 - urb` | Hanya untuk profiling (3 = paling urban) |
| 4 | `size_class` | `pop` dibagi pada batas 5.000 dan 20.000 (kecil 2.634, sedang 1.292, besar 387) | Hanya untuk profiling; batas adalah pilihan tim |
| 5 | `gap_flag` | 1 jika `msw - msw_so - msw_un` > 1 kg | Pemeriksa artefak pelaporan; 1.026 dari 4.313 baris (23,8%) |
| 6 | `csor_share` | `csor / (cres + csor)` | Profiling struktur biaya cluster; median per cluster baseline 0,62; 0,38; 0,59 |

Fitur clustering (11): `tc`, `cres`, `csor`, `msw_pc`, `sor`, `organic`, `paper`, `glass`, `plastic`, `metal`, `other`.
