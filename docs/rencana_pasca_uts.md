# Rencana Pekerjaan Pasca-UTS Menuju Final Project (UAS)

Rencana berikut disusun langsung dari temuan dan keterbatasan pada Subbab 3.1 laporan dan dikerjakan pada Minggu 9–14.

1. **Menetapkan jumlah klaster dan menyetel kandidat.** Membandingkan k = 2, 3, dan 4 dengan kriteria yang sama: silhouette, Davies-Bouldin, BIC/siku, stabilitas ARI, AMI dengan geo, dan keterbacaan profil. Menyetel GMM (covariance_type full, tied, diag, spherical dan reg_covar) serta Ward (linkage average dan complete) dengan aturan pemilihan yang ditetapkan sebelum eksekusi. Keluaran: tabel perbandingan dan satu konfigurasi terpilih beserta alasannya.

2. **Menelaah kota di perbatasan klaster dan pendekatan berbasis kepadatan.** Karena 10,5% kota pada baseline berada lebih dekat ke klaster lain dan DBSCAN pada grid awal tidak memberi segmentasi, akan dianalisis profil kota dengan silhouette negatif dan probabilitas keanggotaan GMM, serta dicoba HDBSCAN. Keluaran: profil kota perbatasan dan keputusan apakah pendekatan kepadatan dipertahankan.

3. **Sensitivitas fitur dan imputasi.** Menjalankan ablasi satu fitur (leave-one-feature-out), menguji apakah enam fitur komposisi mendominasi jarak dibanding tiga fitur biaya, membandingkan model dengan dan tanpa msw_pc, serta melatih ulang model tanpa baris yang organiknya diimputasi, lalu menghitung ARI terhadap model utama. Keluaran: tabel sensitivitas dan kesimpulan tentang kekokohan klaster.

4. **Analisis PAYT yang mengontrol wilayah.** Karena PAYT terkonsentrasi di Utara, perbandingan PAYT dan STANDARD akan dilakukan di dalam sub-populasi geo = 3 dan pada kelas ukuran kota yang sama, untuk tc, cres, csor_share, dan sor. Keluaran: perbandingan terkontrol dengan jumlah observasi per kelompok.

5. **Evaluasi akhir dan dokumentasi final.** Menjalankan model terpilih dan baseline satu kali pada data uji (N = 863) untuk memeriksa konsistensi skor dan profil, menyusun analisis kesalahan, serta menyiapkan model card dan rekomendasi beserta batasannya.
