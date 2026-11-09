# Reproduksibilitas (Subbab 3.2.2 Laporan)

- Notebook `ML_final.ipynb` berisi seluruh tahap secara berurutan (data understanding, preprocessing, baseline, kandidat, sapuan) dan sudah dijalankan dari awal sampai akhir tanpa error.
- Keacakan dikunci dengan SEED = 42 untuk pembagian data, fold, K-Means, GMM, dan statistik Hopkins. Pembagian train–test dan fold disimpan di `baseline_split_istat.csv` dan `baseline_folds_istat.csv` agar semua model dinilai pada partisi yang sama.
- Notebook memuat pemeriksaan otomatis (assert) bahwa fungsi evaluasi kandidat mereproduksi angka baseline, sehingga selisih skor tidak berasal dari perbedaan kode.
- Dataset: Di Foggia, G. (2022), *Municipal waste management cost and fee schemes*, Bicocca Open Archive Research Data, doi 10.17632/w5f9kg7743.1 (CC BY 4.0). Berkas `public_data_waste_fee.csv` diletakkan sesuai path pada sel pemuatan data di notebook.
- Pustaka yang dipakai adalah pandas, numpy, scipy, matplotlib, dan scikit-learn (notebook juga memakai seaborn dan statsmodels pada bagian EDA). Versi pustaka dicatat pada `../requirements.txt`.

Cara menjalankan ulang ada pada `../README.md`.
