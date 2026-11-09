# Penelusuran Laporan ke Notebook

Tabel ini membantu memeriksa bahwa angka di laporan final dapat ditelusuri ke notebook dan berkas hasil.

## Bagian laporan dan bagian notebook

| Bagian laporan | Isi | Bagian notebook / berkas |
|---|---|---|
| 1.1 Problem formulation | Masalah, tujuan, target, pendekatan, batasan | README |
| 1.2 Dokumentasi dataset dan kualitas data | Identitas, deskripsi fitur, masalah kualitas | 0 sampai 3 |
| 1.3 EDA dan temuan awal | Distribusi, korelasi, PCA, Hopkins | 3 (EXP01: 3.1.10) |
| 1.4 Preprocessing | Langkah pengolahan dan protokol anti-leakage | 4.4 sampai 4.6 |
| 1.5 Feature engineering | Enam fitur rekayasa | 4 |
| 2.1 Alasan baseline dan kandidat | K-Means, GMM, Ward, DBSCAN | 5 dan 6 |
| 2.2 Evaluation design | Split, 5-fold CV, metrik, tahapan evaluasi | 4.4, 5, 6, 7 |
| 3.1.1 Baseline dan kandidat (k = 3) | Tabel 3.1 dan 3.2 | 5.2 sampai 5.9, 6.4 sampai 6.6 |
| 3.1.2 Sapuan k dan grid DBSCAN | Tabel 3.3 | 7.2 sampai 7.6, 7.5 |
| 3.1.3 Konfigurasi terpilih vs baseline | Tabel 3.4 dan 3.5 | 7.7 sampai 7.10 |
| 3.1.4 Profil klaster dan pemeriksaan eksternal | Tabel 3.6 dan 3.7 | 5.6, 5.7, 7.10 |
| 3.2 Dokumentasi dan rencana | Log eksperimen, reproduksibilitas, rencana UAS | `docs/experiment_log.md`, `docs/reproduksibilitas.md`, `docs/rencana_pasca_uts.md` |

## Tabel laporan dan berkas CSV

| Tabel di laporan | Berkas CSV |
|---|---|
| 3.1 | `results/tabel_3_1_baseline_kandidat_cv.csv` |
| 3.2 | `results/tabel_3_2_train_dan_ukuran_klaster.csv` |
| 3.3 | `results/tabel_3_3_sapuan_k.csv` |
| 3.4 | `results/tabel_3_4_konfigurasi_terpilih_vs_baseline.csv` |
| 3.5 | `results/tabel_3_5_stabilitas_ari.csv` |
| 3.6 | `results/tabel_3_6_profil_klaster_baseline.csv` |
| 3.7 | `results/tabel_3_7_ami_variabel_penjelas.csv` |
| 3.8 | `docs/experiment_log.md` |

Saat notebook dijalankan, ia juga menyimpan CSV sendiri (misalnya `baseline_ref.csv`, `kandidat_perbandingan.csv`, `kandidat_perfold.csv`, `sapuan_perbandingan.csv`). Jika file itu ingin disertakan sebagai bukti, simpan di `results/`.
