# Hasil Eksperimen Awal dan Analisis (Bab III Laporan)

Isi berkas ini mengikuti Bab III laporan final (`../laporan/UTS_ML_IF-B_Pagi_Kelompok_2.pdf`). Tabel dalam format CSV tersedia di folder ini (`tabel_3_1_...` sampai `tabel_3_7_...`).

## 3.1 Hasil Eksperimen Awal dan Analisis

Seluruh model memakai 11 fitur clustering (tc, cres, csor, msw_pc, sor, organic, paper, glass, plastic, metal, other), pembagian hold-out 80:20 (3.450 baris latih dan 863 baris uji, stratified menurut region_impute, random_state = 42), dan 5-fold cross-validation di dalam data latih dengan fold yang dibekukan. Preprocessing di-fit ulang pada fold latih di setiap iterasi. Sesuai rancangan pada Subbab 2.2, data uji tidak dipakai dalam eksperimen mana pun pada Milestone 1; seluruh skor berasal dari fold validasi atau dari data latih (dalam-sampel), dan data uji disimpan untuk evaluasi akhir pada tahap UAS. Silhouette dibaca dengan acuan bahwa nilai di bawah sekitar 0,25 menandakan struktur yang lemah.

### 3.1.1 Baseline dan Kandidat Model pada k = 3

Pada tahap ini jumlah klaster dikunci pada k = 3 untuk baseline, GMM, dan Agglomerative (Ward) agar selisih skor mencerminkan perbedaan algoritma. DBSCAN tidak memiliki k sehingga memakai min_samples = 22 dan eps dari titik lutut kurva k-distance (2,737 pada data latih penuh; 2,80 sampai 2,86 pada tiap fold).

**Tabel 3.1** Perbandingan Baseline dan Kandidat pada 5-Fold CV Fold Validasi (k = 3; rata-rata ± simpangan baku antar-fold). Berkas: `tabel_3_1_baseline_kandidat_cv.csv`

| Model | Silhouette ↑ | % silhouette < 0 ↓ | Davies-Bouldin ↓ | Rasio B/W ↑ | Jumlah klaster; % noise |
|---|---|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | 0,371 ± 0,018 | 3; 0,0 |
| Kandidat 1: GMM (full) | 0,080 ± 0,007 | 40,1 ± 2,5 | 2,768 ± 0,102 | 0,222 ± 0,013 | 3; 0,0 |
| Kandidat 2: Ward | 0,092 ± 0,013 | 32,4 ± 10,1 | 2,581 ± 0,199 | 0,247 ± 0,018 | 3; 0,0 |
| Kandidat 3: DBSCAN | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | tidak terdefinisi | 1; 1,7 |

**Tabel 3.2** Hasil pada Data Latih Penuh (N = 3.450, dalam-sampel) dan Ukuran Klaster. Berkas: `tabel_3_2_train_dan_ukuran_klaster.csv`

| Model | Silhouette | % silhouette < 0 | Davies-Bouldin | Rasio B/W | Ukuran klaster (baris latih) |
|---|---|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,154 | 10,8 | 2,116 | 0,368 | 1.460 / 997 / 993 |
| Kandidat 1: GMM | 0,082 | 40,0 | 2,783 | 0,218 | 1.224 / 1.201 / 1.025 |
| Kandidat 2: Ward | 0,087 | 26,3 | 2,450 | 0,249 | 1.523 / 1.080 / 847 |
| Kandidat 3: DBSCAN | - | - | - | - | 3.388 (satu klaster) + 62 noise |

**Temuan 1:** baseline K-Means unggul pada semua metrik geometri pada k = 3. Silhouette baseline (0,155) hampir dua kali lipat GMM (0,080) dan Ward (0,092), dengan Davies-Bouldin terendah dan rasio B/W tertinggi. Selisih silhouette terhadap kedua kandidat jauh di atas pedoman derau 0,02 dan arahnya sama di kelima fold. Namun nilai 0,155 sendiri berada di bawah 0,25, sehingga baseline pun hanya menemukan struktur yang lemah.

**Temuan 2:** GMM dan Ward menempatkan banyak kota di perbatasan klaster. Sebanyak 40,1% sampel validasi pada GMM dan 32,4% pada Ward memiliki silhouette negatif, dibandingkan 10,5% pada baseline. Perlu dicatat bahwa silhouette dan Davies-Bouldin berbasis jarak Euclidean sehingga cenderung menguntungkan klaster berbentuk bola ala K-Means; skor rendah GMM tidak otomatis berarti GMM keliru, tetapi pada data ini ia tidak memberi pemisahan yang lebih jelas.

**Temuan 3:** ketiga algoritma menghasilkan partisi yang berbeda. ARI antar-partisi pada data latih hanya 0,307 (K-Means dan GMM), 0,318 (K-Means dan Ward), dan 0,306 (GMM dan Ward). Artinya tidak ada satu partisi tunggal yang diperoleh konsisten oleh semua algoritma. Temuan ini sejalan dengan hasil EDA (Subbab 1.3.4) bahwa data lebih mirip awan kontinu daripada gugus terpisah.

**Temuan 4:** DBSCAN tidak menghasilkan segmentasi pada konfigurasi ini. Dengan min_samples = 22 dan eps lutut kurva, DBSCAN hanya membentuk satu klaster yang memuat 3.388 baris (98,2%) dan menandai 62 baris (1,8%) sebagai noise pada data latih (1,7% pada fold validasi). Karena hanya ada satu klaster, silhouette dan Davies-Bouldin tidak terdefinisi. Kesimpulan ini hanya berlaku untuk konfigurasi tersebut; grid DBSCAN yang lebih luas dibahas pada Subbab 3.1.2.

**Kestabilan baseline.** Baseline stabil terhadap inisialisasi (ARI rata-rata 0,987 pada 20 seed, minimum 0,957) dan terhadap subsampel 80% data latih (ARI rata-rata 0,934, minimum 0,861; ukuran klaster terkecil pada subsampel 24,6 sampai 28,8%). Stabilitas yang tinggi ini hanya menunjukkan partisi dapat diulang, bukan bukti adanya kelompok alami.

### 3.1.2 Penyapuan Jumlah Klaster dan Grid DBSCAN

Setelah perilaku kandidat pada k tetap diketahui, K-Means, GMM, dan Ward disapu pada k = 2 sampai 8 dengan fold yang sama, dan hanya skor fold validasi yang dipakai. Rentang sapuan ditetapkan sebelum sapuan dijalankan (Subbab 2.2.4).

**Tabel 3.3** Sapuan k pada 5-Fold CV Fold Validasi (silhouette dan Davies-Bouldin: rata-rata ± simpangan baku). Berkas: `tabel_3_3_sapuan_k.csv`

| k | K-Means Sil ↑ | K-Means DB ↓ | K-Means % klaster terkecil | GMM Sil ↑ | GMM DB ↓ | Ward Sil ↑ | Ward DB ↓ |
|---|---|---|---|---|---|---|---|
| 2 | 0,202 ± 0,007 | 1,891 ± 0,044 | 34,3 | 0,121 ± 0,008 | 2,656 ± 0,053 | 0,178 ± 0,031 | 1,848 ± 0,348 |
| 3 | 0,155 ± 0,008 | 2,101 ± 0,063 | 26,8 | 0,080 ± 0,007 | 2,768 ± 0,102 | 0,092 ± 0,013 | 2,581 ± 0,199 |
| 4 | 0,152 ± 0,009 | 1,934 ± 0,045 | 17,3 | 0,088 ± 0,010 | 2,497 ± 0,140 | 0,096 ± 0,009 | 2,394 ± 0,106 |
| 5 | 0,144 ± 0,008 | 1,958 ± 0,034 | 9,7 | 0,080 ± 0,011 | 3,309 ± 0,142 | 0,095 ± 0,014 | 2,399 ± 0,086 |
| 6 | 0,118 ± 0,007 | 1,982 ± 0,051 | 8,6 | 0,033 ± 0,015 | 3,259 ± 0,091 | 0,088 ± 0,021 | 2,278 ± 0,031 |
| 7 | 0,111 ± 0,004 | 1,996 ± 0,061 | 7,6 | 0,029 ± 0,009 | 3,336 ± 0,126 | 0,083 ± 0,018 | 2,283 ± 0,081 |
| 8 | 0,111 ± 0,003 | 1,914 ± 0,043 | 6,8 | 0,025 ± 0,022 | 3,006 ± 0,271 | 0,079 ± 0,018 | 2,340 ± 0,170 |

Hasil penerapan aturan pemilihan tetap (klaster terkecil ≥ 5%, silhouette tertinggi, lalu k terkecil dalam satu simpangan baku) adalah k = 2 untuk ketiga model. Beberapa hal perlu dibaca bersamaan:

- Silhouette K-Means turun hampir monoton dari 0,202 (k = 2) ke 0,111 (k = 8) dan tidak ada nilai yang melewati 0,25. Silhouette memang cenderung memihak k kecil (Subbab 2.2.6), sehingga puncak pada k = 2 tidak otomatis berarti k = 2 paling bermakna.
- Kriteria pemeriksa silang tidak sepakat: Davies-Bouldin K-Means terendah pada k = 2 (1,891) tetapi k = 4 (1,934) dan k = 8 (1,914) hampir setara; pada GMM, Davies-Bouldin terendah ada di k = 4 dan BIC terus turun hingga k = 6 sampai 7 (25,817); siku SSE K-Means mengarah ke k = 4. Pemilihan gabungan yang bersifat eksploratif pada notebook (bagian 7.6) juga menunjuk k = 4 untuk K-Means dan GMM.
- Rasio B/W naik mekanis seiring k (K-Means 0,237 pada k = 2 menjadi 0,795 pada k = 8) sehingga tidak dipakai untuk memilih k.

Dengan demikian, kurva metrik tidak menunjukkan siku atau puncak yang tegas selain preferensi silhouette terhadap k = 2. Penentuan k final belum dilakukan pada Milestone 1 dan menjadi agenda UAS.

**Grid DBSCAN (16 konfigurasi).** Tidak ada satu pun dari 16 konfigurasi (min_samples 5, 11, 22, 44 × eps persentil ke-20/40/60/80) yang memenuhi semua syarat (≥ 2 klaster di tiap fold, klaster terkecil ≥ 5%, noise ≤ 20%). Hanya 4 konfigurasi yang menghasilkan ≥ 2 klaster di setiap fold, dan keempatnya gagal karena klaster terkecil kurang dari 5% (0,2 sampai 1,2%) serta noise 27 sampai 73%. Konfigurasi dengan noise kecil hanya membentuk satu klaster. Nilai silhouette tertinggi (0,279 pada min_samples = 44, persentil ke-80) diperoleh jika noise (2,2%) dihitung sebagai kelompok tersendiri dan hanya memisahkan titik ekstrem dari satu massa data, bukan membentuk segmentasi. DBSCAN karena itu tidak ikut dibandingkan lebih lanjut. Kesimpulan ini terbatas pada grid kecil tersebut dan jarak Euclidean pada 11 fitur.

### 3.1.3 Konfigurasi Terpilih dibandingkan dengan Baseline

Konfigurasi terpilih (K-Means, GMM, dan Ward pada k = 2) dibandingkan dengan baseline k = 3 pada fold yang sama. Karena aturan penugasan titik baru berbeda antar-model, silhouette juga dihitung ulang dengan aturan seragam 1-NN ke titik latih sebagai uji ketahanan.

**Tabel 3.4** Konfigurasi Terpilih vs Baseline (5-Fold CV, fold validasi). Berkas: `tabel_3_4_konfigurasi_terpilih_vs_baseline.csv`

| Konfigurasi | Silhouette ↑ | % sil < 0 ↓ | Davies-Bouldin ↓ | Δ silhouette vs baseline (per fold) | Silhouette (1-NN) | Δ vs baseline (1-NN) |
|---|---|---|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,155 ± 0,008 | 10,5 ± 1,3 | 2,101 ± 0,063 | - | 0,142 ± 0,008 | - |
| K-Means (k = 2) | 0,202 ± 0,007 | 4,1 ± 1,4 | 1,891 ± 0,044 | +0,046 (positif 5 dari 5 fold) | 0,193 ± 0,008 | +0,050 |
| GMM (k = 2) | 0,121 ± 0,008 | 28,3 ± 0,6 | 2,656 ± 0,053 | -0,035 (negatif 5 dari 5 fold) | 0,128 ± 0,004 | -0,014 |
| Ward (k = 2) | 0,178 ± 0,031 | 13,0 ± 3,4 | 1,848 ± 0,348 | +0,023 ± 0,030 (positif 4 dari 5 fold) | 0,178 ± 0,031 | +0,036 |

- K-Means k = 2 lebih baik dari baseline dengan selisih +0,046 yang konsisten di kelima fold (di atas pedoman derau 0,02) dan bertahan pada aturan 1-NN (+0,050).
- GMM k = 2 tetap lebih buruk dari baseline pada aturan bawaan (-0,035, konsisten di kelima fold), tetapi selisihnya mengecil menjadi -0,014 pada aturan 1-NN. Sebagian kekalahan GMM karenanya berkaitan dengan cara penugasan titik, bukan semata algoritmanya.
- Ward k = 2 rata-rata lebih tinggi (+0,023) tetapi tidak konsisten (negatif pada satu fold, simpangan baku selisih 0,030) sehingga tidak dianggap bermakna. Ward juga tidak stabil terhadap subsampel (lihat Tabel 3.5).

**Tabel 3.5** Stabilitas Partisi (ARI terhadap partisi acuan pada data latih). Berkas: `tabel_3_5_stabilitas_ari.csv`

| Konfigurasi | ARI seed (rata-rata; minimum) | ARI subsampel 80% (rata-rata; minimum) | % klaster terkecil pada subsampel |
|---|---|---|---|
| Baseline: K-Means (k = 3) | 0,987; 0,957 | 0,934; 0,861 | 24,6 – 28,8 |
| K-Means (k = 2) | 0,988; 0,974 | 0,975; 0,925 | 33,2 – 36,7 |
| GMM (k = 2) | 1,000; 0,999 | 0,951; 0,747 | 44,7 – 50,0 |
| Ward (k = 2) | tidak relevan (deterministik) | 0,382; 0,055 | 11,7 – 44,8 |

Ward sangat sensitif terhadap perubahan sampel (ARI rata-rata hanya 0,382 dan minimum 0,055), sedangkan K-Means stabil pada k = 2 maupun k = 3. Dari sudut pandang geometri dan kestabilan, K-Means k = 2 adalah konfigurasi terkuat pada Milestone 1, namun kuatnya tetap relatif: silhouette 0,202 masih di bawah 0,25.

### 3.1.4 Profil Klaster dan Pemeriksaan Eksternal

Profil klaster dihitung dalam satuan asli (sebelum log1p, winsorizing, dan z-score, setelah imputasi) pada data latih untuk baseline K-Means k = 3. Nilai tabel adalah median per klaster; baris geografi, PAYT, dan ukuran kota adalah persentase baris dalam klaster. Nama klaster adalah label deskriptif sementara dari profil ini, bukan hasil yang sudah divalidasi.

**Tabel 3.6** Profil Klaster Baseline K-Means (k = 3, Data Latih N = 3.450). Berkas: `tabel_3_6_profil_klaster_baseline.csv`

| Atribut | Klaster 0 | Klaster 1 | Klaster 2 |
|---|---|---|---|
| Jumlah kota (proporsi) | 1.460 (42,3%) | 997 (28,9%) | 993 (28,8%) |
| Total biaya per kapita, tc (EUR) | 115,5 | 185,3 | 139,8 |
| Biaya residu per kapita, cres (EUR) | 29,0 | 74,4 | 42,1 |
| Biaya terpilah per kapita, csor (EUR) | 47,1 | 44,5 | 55,9 |
| Porsi biaya pemilahan (csor_share) | 0,62 | 0,38 | 0,59 |
| Sampah per kapita, msw_pc (kg/jiwa) | 454,2 | 498,3 | 334,9 |
| Tingkat pemilahan, sor (%) | 78,7 | 49,4 | 70,1 |
| Organik (%) | 27,6 | 9,7 | 29,2 |
| Kertas / kaca (%) | 12,2 / 9,1 | 8,7 / 7,0 | 10,8 / 11,4 |
| Plastik / logam (%) | 5,9 / 2,2 | 4,2 / 0,9 | 7,4 / 1,2 |
| Lainnya (%) | 11,1 | 5,1 | 4,4 |
| Median penduduk (jiwa) | 4.528 | 4.577 | 2.275 |
| Geo: Selatan / Tengah / Utara (%) | 4,8 / 6,0 / 89,2 | 39,0 / 19,6 / 41,4 | 57,0 / 16,5 / 26,4 |
| Skema PAYT (d_fee = 1, %) | 24,6 | 3,1 | 4,1 |
| Ukuran kota kecil (%) | 54,5 | 51,6 | 78,8 |
| Label deskriptif sementara | Pemilahan tinggi, biaya residu rendah, dominan Utara | Pemilahan rendah, biaya residu tinggi, organik rendah | Pemilahan menengah-tinggi, sampah per kapita rendah, kota kecil, dominan Selatan |

Klaster 0 dan 2 sama-sama berpemilahan tinggi, tetapi dibedakan oleh sampah per kapita (454 vs 335 kg/jiwa), ukuran kota, dan wilayah. Klaster 1 menonjol dengan tc 185 EUR yang didorong biaya residu (74 EUR), pemilahan rendah (49%), dan organik hanya 9,7%. Kota PAYT terkonsentrasi di Klaster 0 (24,6%), tetapi klaster yang sama juga 89,2% berada di Utara.

**Tabel 3.7** AMI antara Klaster dan Variabel Penjelas (data latih; bukan ground truth). Berkas: `tabel_3_7_ami_variabel_penjelas.csv`

| Variabel (tidak ikut jarak) | K-Means k = 3 (baseline) | GMM k = 3 | Ward k = 3 | K-Means k = 2 |
|---|---|---|---|---|
| geo | 0,185 | 0,130 | 0,121 | 0,047 |
| region_impute | 0,170 | 0,126 | 0,137 | 0,063 |
| d_fee (PAYT) | 0,070 | 0,028 | 0,016 | 0,053 |
| sea | 0,065 | 0,030 | 0,066 | 0,064 |
| urb_rev | 0,052 | 0,059 | 0,054 | 0,005 |
| size_class | 0,044 | 0,092 | 0,065 | 0,021 |
| flag_imputasi (artefak) | 0,026 | 0,054 | 0,022 | 0,031 |
| gap_flag (artefak) | 0,010 | -0,000 | 0,024 | -0,000 |
| isle | 0,005 | 0,003 | 0,005 | 0,008 |

- Keterkaitan klaster dengan variabel eksternal tergolong rendah (AMI tertinggi 0,185 untuk geo pada baseline). Klaster sebagian mengikuti pola wilayah, tetapi tidak didominasi oleh wilayah, PAYT, urbanisasi, atau ukuran kota.
- AMI dengan gap_flag (0,010 pada baseline) dan flag_imputasi (0,026) sangat rendah, sehingga tidak ada indikasi kuat bahwa klaster hanya mencerminkan perbedaan praktik pelaporan atau baris yang banyak diimputasi. Ini hanya pemeriksaan awal; uji dengan melatih ulang model tanpa baris imputasi belum dijalankan (Subbab 3.1.5).
- Keterkaitan geo turun dari 0,185 (k = 3) menjadi 0,047 (k = 2). Ini menunjukkan bahwa klaster tambahan pada k = 3 memisahkan pola yang berkaitan dengan wilayah dan sampah per kapita, sehingga k = 2 yang unggul secara silhouette belum tentu paling informatif untuk tujuan profil pengelolaan.
- Hubungan PAYT dengan Klaster 0 tidak boleh dibaca sebagai dampak kebijakan karena PAYT dan wilayah Utara saling bercampur (Subbab 1.3.3).

### 3.1.5 Keterbatasan Hasil Awal

- **Struktur klaster lemah.** Silhouette terbaik hanya 0,202 (K-Means k = 2) dan 0,155 pada baseline, dengan 10,5% kota berada lebih dekat ke klaster lain. Hasil clustering perlu dibaca sebagai segmentasi kontinu, bukan kelompok alami.
- **Pemilihan k belum final.** Aturan tetap memilih k = 2, tetapi kriteria lain (Davies-Bouldin GMM, BIC, siku SSE, AMI geo) menunjuk k yang berbeda. Baseline k = 3 dipertahankan sebagai acuan, bukan sebagai klaim bahwa k = 3 terbaik.
- **Data uji belum dipakai.** Belum ada bukti generalisasi pada 863 baris uji; hal ini sengaja ditunda ke evaluasi akhir.
- **Uji sensitivitas imputasi belum dijalankan.** Sebanyak 11,4% nilai organik berasal dari imputasi. Pemeriksaan yang ada baru AMI dengan flag_imputasi; pelatihan ulang tanpa baris imputasi dijadwalkan pada UAS.
- **Ambiguitas organik rendah.** Nilai organik di bawah 2% (8,9% baris, terutama Puglia dan Toscana) belum dapat dibedakan antara pemilahan yang memang rendah dan fraksi yang tidak tercatat (Subbab 1.2.2).
- **Hyperparameter masih default dan grid DBSCAN kecil.** Perbandingan antar-algoritma pada Milestone 1 belum memperhitungkan tuning (misalnya covariance_type GMM atau linkage selain Ward).
- **Evaluasi internal.** Lima fold bukan uji statistik, fold latih saling tumpang tindih, dan AMI bukan ground truth. Selisih dipakai sebagai pedoman, bukan bukti pasti.
