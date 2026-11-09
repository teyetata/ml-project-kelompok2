# Dokumentasi Sumber Dataset

| Informasi | Keterangan |
|---|---|
| Nama dataset | Municipal waste management cost and fee schemes |
| Sumber | Bicocca Open Archive Research Data (kontributor: Giacomo Di Foggia) |
| Tautan sumber | https://board.unimib.it/datasets/w5f9kg7743/1 |
| DOI | 10.17632/w5f9kg7743.1 |
| Sitasi | Di Foggia, Giacomo (2022), "Municipal waste management cost and fee schemes", Bicocca Open Archive Research Data, V1 |
| Lisensi | CC BY 4.0 (Creative Commons Attribution 4.0 International): boleh dibagikan, disalin, dan dimodifikasi dengan atribusi yang sesuai, tautan lisensi, dan keterangan jika ada perubahan |
| Unit observasi | Kotamadya (municipality) di Italia |
| Jumlah baris | 4.341 |
| Jumlah kolom | 39 (1 ID, 3 atribut identitas wilayah, 35 fitur) |
| Target | Tidak ada (unsupervised learning / clustering) |
| Jenis data | Numerik dan kategorikal |
| Berkas | `public_data_waste_fee.csv` dan `read_me.txt` (kamus variabel dari penyedia) |

## Catatan penggunaan dalam proyek

- Kamus variabel di notebook (Bagian 0) disalin dari `read_me.txt` dengan ejaan asli dipertahankan, termasuk salah ketik seperti *caputa* dan *texile*.
- Dataset tidak disertakan di repository; cara memperolehnya ada di [`../data/README.md`](../data/README.md).
- Kolom `wood` dan `texile` tidak dipakai sebagai fitur clustering karena porsi data kosongnya melebihi 23%.
- Fitur clustering yang dipakai (11): `tc`, `cres`, `csor`, `msw_pc`, `sor`, `organic`, `paper`, `glass`, `plastic`, `metal`, `other`. Variabel lain (misalnya `geo`, `d_fee`, `size_class`, `gap_flag`) dipakai hanya untuk memeriksa makna cluster dan tidak ikut perhitungan jarak.
- Setelah pembuangan baris tidak valid tersisa 4.313 kota: 3.450 train dan 863 test.
