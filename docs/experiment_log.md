# Catatan Eksperimen (Experiment Tracking Log)

Dataset dan rumusan masalah tidak diganti meskipun hasil awal pemisahan cluster lemah, sesuai ketentuan UTS. Data test tidak dipakai pada eksperimen E0 sampai E9; hanya diperiksa secara deskriptif.

| ID | Eksperimen (bagian notebook) | Konfigurasi | Hasil ringkas | Keputusan |
|---|---|---|---|---|
| E0 | Split dan fold (4.4) | 80:20 stratified menurut `region_impute`, seed 42; 5-fold di dalam train | 3.450 train dan 863 test; selisih proporsi region terbesar 0,0006 | Split dan fold dibekukan dan dipakai semua tahap |
| E1 | Preprocessing fit pada train (4.5 dan 4.6) | Imputasi median bertingkat, log1p, winsorizing P1 sampai P99, z-score | Selisih parameter terhadap fit seluruh data kecil (selisih mean maksimum 0,013 std; selisih P99 maksimum 0,132 std pada `paper`) | Tetap fit pada train |
| E2 | Baseline K-Means k = 3 (5.2 sampai 5.9) | Default scikit-learn, `n_init` 10, seed 42 | Silhouette CV 0,155 ± 0,008; DB 2,101; ARI seed 0,987 | Dibekukan sebagai acuan |
| E3 | GMM k = 3 (6.4 dan 6.5) | `covariance_type='full'`, `n_init` 10; konvergen | Silhouette CV 0,080 ± 0,007 | Tidak lebih baik dari baseline |
| E4 | Agglomerative Ward k = 3 (6.4 dan 6.5) | Jarak Euclidean | Silhouette CV 0,092 ± 0,013 | Tidak lebih baik dari baseline |
| E5 | DBSCAN nilai bawaan (6.2) | eps 0,5; min_samples 5 | 99,5% titik train menjadi noise | Tidak dipakai |
| E6 | DBSCAN eps dari k-distance (6.4) | eps 2,737; min_samples 22 | Satu cluster dan 1,7% noise; metrik tidak terdefinisi | Tidak dapat dibandingkan angka demi angka |
| E7 | Sapuan k untuk K-Means, GMM, Ward (7.2 sampai 7.7) | Aturan utama dan syarat ukuran cluster ditetapkan sebelum menjalankan | Aturan utama memilih k = 2 untuk ketiga model | Hanya sensitivitas; keputusan final ditunda ke UAS |
| E8 | Grid DBSCAN 4 × 4 (7.5) | Persentil eps 20 sampai 80; min_samples 5, 11, 22, 44 | 0 dari 16 konfigurasi memenuhi syarat | DBSCAN dilaporkan tanpa konfigurasi terpilih |
| E9 | Stabilitas dan variabel penjelas (5.5, 5.7, 7.9) | ARI antar-seed dan subsampel; AMI | Baseline stabil; Ward k = 2 tidak stabil (ARI subsampel 0,382) | Dipertimbangkan saat memilih model final |
