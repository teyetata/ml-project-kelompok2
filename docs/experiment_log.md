# Catatan Pelacakan Eksperimen (Tabel 3.8 Laporan)

Setiap baris dapat ditelusuri ke bagian notebook yang disebutkan pada kolom terakhir (`../notebooks/ML_final.ipynb`).

**Tabel 3.8** Log Eksperimen Milestone 1

| ID | Eksperimen | Konfigurasi utama | Hasil utama | Bagian notebook |
|---|---|---|---|---|
| EXP01 | Struktur awal (PCA dan Hopkins) | PCA 3 komponen; Hopkins 10 seed; z-score vs rank; 3.441 baris lengkap | Varians 3 PC 54,9% (z-score) dan 56,8% (rank); Hopkins 0,900 vs 0,600 | 3.1.10 |
| EXP02 | Preprocessing dan diagnostik kebocoran | Imputasi median bertingkat, log1p, winsorizing P1–P99, z-score; fit pada train | Fit train vs fit seluruh data: selisih mean maks. 0,013 SD; rasio SD 0,992–1,006; selisih P99 maks. 0,132 SD (paper) | 4.4–4.6 |
| EXP03 | Baseline K-Means k = 3 | k-means++, n_init = 10, seed 42 | Sil. CV 0,155 ± 0,008; sil. train 0,154; DB CV 2,101; ARI seed 0,987; ARI subsampel 0,934 | 5.2–5.9 |
| EXP04 | Kandidat 1: GMM k = 3 | covariance_type = full, n_init = 10 | Sil. CV 0,080 ± 0,007; % sil. < 0 = 40,1; DB 2,768 | 6.4–6.6 |
| EXP05 | Kandidat 2: Ward k = 3 | linkage = ward, Euclidean; 1-NN untuk titik baru | Sil. CV 0,092 ± 0,013; % sil. < 0 = 32,4; DB 2,581 | 6.4–6.6 |
| EXP06 | Kandidat 3: DBSCAN | min_samples = 22, eps = 2,737 (lutut k-distance) | 1 klaster + 62 noise (1,8%) pada train; metrik tidak terdefinisi | 6.4–6.5 |
| EXP07 | Sapuan k | K-Means, GMM, Ward; k = 2–8; 5 fold | K-Means sil. 0,202 (k = 2) turun ke 0,111 (k = 8); aturan memilih k = 2 untuk ketiga model | 7.2–7.6 |
| EXP08 | Grid DBSCAN | min_samples 5/11/22/44 × eps persentil 20/40/60/80 | 0 dari 16 konfigurasi memenuhi syarat | 7.5 |
| EXP09 | Konfigurasi terpilih vs baseline, ketahanan, stabilitas | k = 2 untuk tiga model; 1-NN seragam; 20 seed dan 20 subsampel | K-Means k = 2: +0,046 (5/5 fold); GMM k = 2: -0,035; Ward k = 2: +0,023 (tidak konsisten, ARI subsampel 0,382) | 7.7–7.10 |
