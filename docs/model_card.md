# Model Card: Baseline K-Means (k = 3)

| Aspek | Keterangan |
|---|---|
| Nama dan status | Baseline K-Means (k = 3). Dibekukan sebagai acuan pembanding dan bukan model final; model final ditetapkan pada tahap UAS |
| Jenis model | Clustering (unsupervised), classical machine learning |
| Implementasi | `sklearn.cluster.KMeans` dengan `n_clusters=3`, `init='k-means++'`, `max_iter=300`, `tol=1e-4`, `algorithm='lloyd'`, `n_init=10`, `random_state=42` |
| Masukan | 11 fitur terstandarisasi: tc, cres, csor, msw_pc, sor, organic, paper, glass, plastic, metal, other |
| Keluaran | Label cluster 0, 1, 2 (dinomori ulang menurut ukuran pada train; 0 = terbesar) |
| Data | Municipal waste management cost and fee schemes (Di Foggia, 2022; CC BY 4.0). 4.313 kota setelah pembuangan baris tidak valid: 3.450 train dan 863 test (stratified menurut `region_impute`, seed 42). Data test belum dipakai untuk memilih atau mengevaluasi model |
| Preprocessing | Imputasi median bertingkat, log1p pada tc, cres, csor, msw_pc, dan metal, winsorizing P1 sampai P99, lalu z-score; parameter di-fit hanya pada train (dan di-fit ulang pada tiap fold CV) |
| Validasi | 5-fold CV di dalam train (stratified menurut `region_impute`, 690 baris per fold) |
| Metrik dan hasil (CV) | Silhouette 0,155 ± 0,008; % silhouette < 0 sebesar 10,5 ± 1,3; Davies-Bouldin 2,101 ± 0,063; rasio B/W 0,371 ± 0,018 |
| Kestabilan | ARI terhadap baseline 0,987 (seed berbeda; minimum 0,957) dan 0,934 (subsampel 80%; minimum 0,861) |
| Ukuran cluster (train) | 1.460 (42,3%), 997 (28,9%), 993 (28,8%) |
| Kegunaan yang dimaksud | Eksplorasi dan pemrofilan pola pengelolaan sampah kota/kabupaten di Italia berdasarkan struktur biaya, tingkat pemilahan, dan komposisi sampah |
| Di luar kegunaan | Menilai atau meranking kinerja suatu kota, menyimpulkan efek skema PAYT, atau dasar keputusan kebijakan otomatis tanpa validasi lanjutan |
| Keterbatasan | Pemisahan cluster lemah (silhouette di bawah 0,25) dan data tampak berupa kontinum; evaluasi hanya internal; k dipilih a priori; K-Means mengasumsikan cluster berbentuk bola dengan ukuran mirip; `csor` hampir tidak membedakan cluster; 491 nilai `organic` (11,4%) hasil imputasi |
| Pertimbangan penggunaan | Cluster tidak boleh dibaca sebagai efek PAYT karena PAYT terkonsentrasi di Utara (cluster 0: 24,6% PAYT dan 89,2% Utara). Lombardia mendominasi (28,3% baris), sehingga profil perlu dibaca per region. Data berupa agregat tingkat kota dari sumber publik |
| Reproduksibilitas | Python 3.12, scikit-learn, `SEED = 42` |
