# Rencana Pekerjaan Pasca-UTS Menuju Final Project (UAS)

Disusun dari temuan dan keterbatasan pada Milestone 1 (lihat `../results/hasil_eksperimen_awal.md`). Jadwal dan penanggung jawab: **[ISI]**.

| Tahap | Pekerjaan | Dasar (temuan awal) | Keluaran |
|---|---|---|---|
| 1. Menetapkan k dan model final | Membandingkan k = 2, 3, dan 4 dengan mempertimbangkan kestabilan, tafsiran cluster, dan skor, tidak hanya silhouette | Aturan utama memilih k = 2 tetapi diuntungkan metrik; k = 3 punya dasar a priori | Keputusan model final beserta alasan |
| 2. Tuning terbatas | GMM dengan `covariance_type` lain (diag, tied, spherical); DBSCAN dengan grid lebih luas; fold dan metrik yang sama | GMM hanya diuji dengan `full`; grid DBSCAN 4 × 4 tanpa konfigurasi layak | Tabel perbandingan versi final |
| 3. Uji fitur | Menjalankan ulang clustering tanpa `csor` dan menilai kontribusi tiap fitur | `csor` hampir tidak membedakan cluster (selisih centroid sekitar 0,3 z-score) | Tabel dengan dan tanpa fitur |
| 4. Uji sensitivitas imputasi | Membandingkan cluster dengan dan tanpa baris yang `organic`-nya diimputasi (penanda `flag_imputasi` sudah disiapkan di notebook) | 491 nilai `organic` (11,4%) berasal dari imputasi | Perbandingan ARI dan profil cluster |
| 5. Validasi substantif | Membandingkan PAYT dan STANDARD dengan kontrol wilayah, serta pemeriksaan terhadap `geo`, `region_impute`, dan `size_class` | PAYT terkonsentrasi di Utara; AMI semua variabel penjelas rendah | Profil dan interpretasi cluster |
| 6. Evaluasi akhir | Evaluasi satu kali pada data test untuk model terpilih dan baseline | Data test belum dipakai | Tabel hasil akhir |
| 7. Dokumentasi dan laporan akhir | README dan versi library, notebook yang dapat dijalankan dari awal, laporan final, bahan presentasi | Rubrik menilai keterlacakan laporan dan notebook | Repositori dan laporan final |

## Risiko dan mitigasi

- **Data berupa kontinum, cluster mungkin bukan kelompok alami.** Dilaporkan sebagai segmentasi yang dapat ditafsirkan dan diperiksa lewat validasi substantif (tahap 5).
- **Hasil bergantung pada k dan metrik.** k dipilih dengan beberapa kriteria dan kestabilan, bukan satu skor.
- **Pelaporan antar wilayah berbeda dan Lombardia mendominasi (28,3% baris).** `gap_flag` dipertahankan dan cluster diprofilkan per region.
- **Selisih antar-model kecil dibanding derau lima fold.** Selisih di bawah sekitar 0,02 tidak dianggap perbaikan nyata.
