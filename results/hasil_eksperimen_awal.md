# Hasil Eksperimen Awal (Milestone 1)

Semua angka berasal dari keluaran `notebooks/ML_final.ipynb` (bagian 4.4 sampai 7.11). Evaluasi memakai data train (3.450 baris) dengan 5-fold CV; data test (863 baris) tidak dipakai untuk memilih atau membandingkan model.

## 1. Tabel hasil: baseline dan kandidat (k = 3)

| Model | Validasi | Silhouette ↑ | % silh. < 0 ↓ | Davies-Bouldin ↓ | Rasio B/W ↑ | SSE per sampel (*) | Jumlah cluster | % noise |
|---|---|---|---|---|---|---|---|---|
| Baseline: K-Means | 5-fold CV | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | 0,371 ± 0,018 | 8,061 ± 0,254 | 3 | 0,0 |
| Kandidat 1: GMM (full) | 5-fold CV | 0,080 ± 0,007 | 40,1 ± 2,5 | 2,768 ± 0,102 | 0,222 ± 0,013 | 9,047 ± 0,341 | 3 | 0,0 |
| Kandidat 2: Agglomerative (Ward) | 5-fold CV | 0,092 ± 0,013 | 32,4 ± 10,1 | 2,581 ± 0,199 | 0,247 ± 0,018 | 8,856 ± 0,381 | 3 | 0,0 |
| Kandidat 3: DBSCAN | 5-fold CV | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | 1 | 1,7 |

(*) SSE hanya ditampilkan dan tidak dipakai menilai model karena K-Means meminimalkannya secara langsung. Metrik DBSCAN tidak terdefinisi karena hanya terbentuk satu cluster. Berkas: [`tabel_hasil_awal.csv`](tabel_hasil_awal.csv).

Silhouette per fold:

| Model | Fold 1 | Fold 2 | Fold 3 | Fold 4 | Fold 5 |
|---|---|---|---|---|---|
| K-Means | 0,166 | 0,153 | 0,157 | 0,155 | 0,145 |
| GMM | 0,092 | 0,072 | 0,078 | 0,079 | 0,080 |
| Ward | 0,091 | 0,090 | 0,108 | 0,099 | 0,074 |

## 2. Hasil pada data train (dalam-sampel) dan ukuran cluster

| Model | Silhouette | % silh. < 0 | Davies-Bouldin | Ukuran cluster (train) |
|---|---|---|---|---|
| K-Means | 0,154 | 10,8 | 2,116 | 1.460 / 997 / 993 |
| GMM | 0,082 | 40,0 | 2,783 | 1.224 / 1.201 / 1.025 |
| Ward | 0,087 | 26,3 | 2,450 | 1.523 / 1.080 / 847 |
| DBSCAN | tidak terdefinisi | - | - | 3.388 (satu cluster) dan 62 noise |

## 3. Kesepakatan antar-partisi (ARI) dan kestabilan baseline

- ARI K-Means vs GMM 0,307; K-Means vs Ward 0,318; GMM vs Ward 0,306.
- Kestabilan baseline: ARI 0,987 (seed berbeda; minimum 0,957) dan 0,934 (subsampel 80%; minimum 0,861).

## 4. Profil cluster baseline (median satuan asli, train)

| Cluster | n (%) | tc | cres | csor | msw_pc | sor (%) | organic (%) | Median penduduk | Utara (%) | PAYT (%) | Kelas besar (%) |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 1.460 (42,3) | 115,54 | 28,99 | 47,09 | 454,22 | 78,65 | 27,55 | 4.528 | 89,2 | 24,6 | 6,8 |
| 1 | 997 (28,9) | 185,32 | 74,44 | 44,53 | 498,34 | 49,37 | 9,67 | 4.577 | 41,4 | 3,1 | 18,6 |
| 2 | 993 (28,8) | 139,79 | 42,09 | 55,87 | 334,89 | 70,08 | 29,15 | 2.275 | 26,4 | 4,1 | 2,9 |

## 5. Eksplorasi awal sensitivitas jumlah cluster (bagian 7)

Dilaporkan sebagai sensitivitas, bukan pemilihan model final. Berkas: [`tabel_sapuan_k_awal.csv`](tabel_sapuan_k_awal.csv).

| Model | Silhouette ↑ | % silh. < 0 ↓ | Davies-Bouldin ↓ | Rasio B/W ↑ |
|---|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | 0,371 ± 0,018 |
| K-Means (k = 2) | 0,202 ± 0,007 | 4,1 ± 1,4 | 1,891 ± 0,044 | 0,237 ± 0,015 |
| GMM (k = 2) | 0,121 ± 0,008 | 28,3 ± 0,6 | 2,656 ± 0,053 | 0,126 ± 0,004 |
| Ward (k = 2) | 0,178 ± 0,031 | 13,0 ± 3,4 | 1,848 ± 0,348 | 0,151 ± 0,018 |

Skor k = 2 lebih tinggi sebagian karena sifat silhouette yang menguntungkan k kecil. Rasio B/W naik seiring k, sehingga hanya layak dibandingkan antar-model pada k yang sama. Ward k = 2 tidak stabil (ARI subsampel rata-rata 0,382; minimum 0,055). Dari 16 konfigurasi grid DBSCAN (4 × 4), tidak ada yang memenuhi syarat.

## 6. Temuan penting

1. Baseline K-Means mengungguli semua kandidat pada k = 3, konsisten di kelima fold.
2. Pemisahan cluster lemah pada semua metode (silhouette di bawah 0,25); data lebih mirip awan kontinu.
3. Partisi sangat bergantung pada algoritma (ARI antar-partisi sekitar 0,31).
4. DBSCAN tidak menemukan struktur kepadatan: nilai bawaan menjadikan 99,5% titik noise; dengan eps 2,737 dan min_samples 22 hanya terbentuk satu cluster.
5. Baseline stabil, tetapi stabil belum berarti kelompoknya nyata.
6. Cluster baseline dapat ditafsirkan sebagai gradien biaya-pemilahan. PAYT terkonsentrasi di cluster 0 yang hampir seluruhnya Utara (89,2%), sehingga tidak boleh dibaca sebagai efek PAYT.

## 7. Keterbatasan

- Evaluasi hanya internal; metrik condong ke cluster berbentuk bola.
- Tabel utama hanya pada k = 3 dengan hyperparameter dasar (GMM hanya `covariance_type='full'`; DBSCAN eps dari lutut k-distance dan grid 4 × 4).
- Aturan penugasan titik baru berbeda antar-model (centroid terdekat, posterior GMM, 1-NN untuk Ward, titik inti untuk DBSCAN).
- Lima fold bukan uji statistik; selisih di bawah sekitar 0,02 tidak dibaca sebagai perbedaan nyata.
- 491 nilai `organic` (11,4% dari 4.313 baris) berasal dari imputasi median; batas `size_class` adalah pilihan tim yang belum diuji; ketidakkonsistenan pelaporan `msw` dan komposisi dipertahankan apa adanya.

## 8. Gambar keluaran notebook

| Berkas | Bagian notebook |
|---|---|
| `figures/5_6_centroid_dan_sebaran_fitur_per_cluster.png` | 5.6 Karakteristik cluster |
| `figures/5_7_komposisi_cluster_per_region.png` | 5.7 Komposisi cluster per region |
| `figures/5_8_proyeksi_pca_cluster_kmeans.png` | 5.8 Proyeksi PCA, warna = cluster K-Means |
| `figures/5_8_diagram_silhouette_dan_ukuran_cluster.png` | 5.8 Diagram silhouette dan ukuran cluster |
| `figures/6_7_visualisasi_perbandingan_kandidat.png` | 6.7 Visualisasi perbandingan kandidat |
| `figures/7_3_grafik_sapuan_k_dan_elbow.png` | 7.3 Grafik sapuan k dan elbow |
| `figures/7_10_centroid_konfigurasi_terpilih.png` | 7.10 Centroid konfigurasi terpilih |
| `figures/7_10_proyeksi_pca_konfigurasi_terpilih.png` | 7.10 Proyeksi PCA konfigurasi terpilih |
