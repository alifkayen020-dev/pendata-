# Peramalan Kadar NO₂ Daerah Pati

## Tujuan Proyek
Notebook ini digunakan untuk melakukan pengambilan data NO₂ dari Copernicus Data Space, melakukan preprocessing data, mendeteksi dan menangani outlier, kemudian membangun model prediksi menggunakan KNN Regression.

---

# 1. Pengumpulan Data

## 1.1 Koneksi ke Copernicus Data Space
Pada tahap ini dilakukan autentikasi ke layanan OpenEO untuk mengakses data satelit Sentinel-5P yang menyediakan informasi konsentrasi NO₂.

**Tujuan:**
- Menghubungkan notebook dengan server OpenEO.
- Mendapatkan akses ke data pengamatan atmosfer.
- Menjalankan proses ekstraksi data secara otomatis.

## 1.2 Menentukan Area of Interest (AOI)

Area penelitian ditentukan menggunakan polygon GeoJSON yang mencakup wilayah Kabupaten Pati dan sekitarnya.

### Peta AOI

![AOI Kabupaten Pati](geojson_map.jpeg)

**Penjelasan:**
- Polygon digunakan untuk membatasi area pengambilan data.
- Data yang berada di luar polygon tidak ikut diproses.
- Pendekatan ini mengurangi ukuran data dan mempercepat pemrosesan.

## 1.3 Eksekusi Batch Job

Data Sentinel-5P diproses melalui OpenEO dan disimpan dalam format NetCDF (`.nc`).

**Mengapa NetCDF digunakan?**
- Cocok untuk data spasial dan temporal.
- Mampu menyimpan banyak variabel dalam satu file.
- Umum digunakan pada bidang meteorologi dan penginderaan jauh.

---

# 2. Preprocessing Data

Tahap preprocessing bertujuan menghasilkan data yang bersih dan siap digunakan untuk pemodelan.

## 2.1 Membaca Data NetCDF

Data NO₂ diekstrak dari file NetCDF.

**Aktivitas yang dilakukan:**
- Membuka file NetCDF.
- Membaca variabel waktu.
- Membaca nilai konsentrasi NO₂.
- Mengubah waktu menjadi format datetime.

**Manfaat:**
Data menjadi lebih mudah dianalisis menggunakan Pandas dan Python.

---

## 2.2 Mengatasi Missing Value dengan Interpolasi Linear

Pada data deret waktu sering ditemukan nilai kosong (missing value).

Metode yang digunakan adalah **Linear Interpolation**.

### Cara Kerja
Jika terdapat data:

| Hari | Nilai |
|--------|--------|
| 1 | 10 |
| 2 | NaN |
| 3 | 14 |

Maka nilai hari ke-2 dihitung:

(10 + 14) / 2 = 12

### Alasan Penggunaan
- Sederhana.
- Cepat.
- Cocok untuk data time series yang berubah secara bertahap.

---

## 2.3 Mengubah Format Tanggal dan Menghitung Rata-rata

Data kemudian:

1. Diubah menjadi format tanggal yang konsisten.
2. Dirata-ratakan apabila terdapat lebih dari satu observasi pada tanggal yang sama.
3. Disiapkan menjadi data harian.

**Tujuan:**
- Mengurangi redundansi data.
- Memudahkan analisis tren.

---

## 2.4 Menyimpan Data ke CSV

Data hasil preprocessing disimpan ke file CSV.

**Keuntungan:**
- Mudah dibuka di Excel.
- Mudah digunakan pada proses machine learning.
- Ukuran file relatif kecil.

---

## 2.5 Pengecekan Missing Value

Setelah data disimpan, dilakukan pemeriksaan ulang untuk memastikan:

- Tidak ada tanggal yang hilang.
- Tidak ada nilai kosong yang tersisa.
- Data sudah berurutan secara kronologis.

---

## 2.6 Deteksi Outlier Menggunakan IQR

Outlier adalah data yang memiliki nilai sangat jauh dari mayoritas data lainnya.

Metode yang digunakan adalah **Interquartile Range (IQR)**.

### Rumus

Q1 = Kuartil 1

Q3 = Kuartil 3

IQR = Q3 - Q1

Batas bawah = Q1 - 1.5 × IQR

Batas atas = Q3 + 1.5 × IQR

### Visualisasi Outlier

![Deteksi Outlier NO2](outlier_detection.png)

### Interpretasi Grafik

Titik merah menunjukkan data yang terdeteksi sebagai outlier.

Garis:
- Oranye = batas atas IQR
- Ungu = batas bawah IQR

Data yang berada di luar batas tersebut dianggap tidak normal dan perlu ditangani.

### Mengapa Outlier Dihapus?

Karena dapat:
- Menurunkan performa model.
- Menyebabkan prediksi menjadi bias.
- Mengganggu pola sebenarnya pada data time series.

---

## 2.7 Interpolasi Setelah Penghapusan Outlier

Setelah outlier diubah menjadi NaN, dilakukan interpolasi ulang.

### Hasil Setelah Pembersihan Data

![Data Setelah Outlier Removal](interpolated_data.png)

### Penjelasan

Pada grafik ini:

- Titik outlier sudah dihilangkan.
- Nilai kosong hasil penghapusan outlier telah diisi kembali.
- Kurva menjadi lebih halus.
- Pola data lebih representatif terhadap kondisi sebenarnya.

---

# 3. Modeling Menggunakan KNN Regression

## 3.1 Uji Korelasi Data

Sebelum membangun model dilakukan analisis hubungan antar data historis.

**Tujuan:**
- Mengetahui pengaruh data masa lalu terhadap data masa depan.
- Menentukan jumlah lag yang relevan.

---

## 3.2 Normalisasi Data

Metode yang digunakan:

MinMaxScaler

### Rumus

X_norm = (X - Xmin) / (Xmax - Xmin)

### Tujuan

- Menyamakan skala data.
- Mempercepat proses pembelajaran model.
- Menghindari dominasi nilai besar.

---

## 3.3 Transformasi Menjadi Data Supervised

Data time series diubah menjadi format supervised learning.

Contoh lag 4:

| t-4 | t-3 | t-2 | t-1 | t |
|------|------|------|------|------|
| 10 | 12 | 11 | 13 | 14 |

Model akan mempelajari hubungan empat hari sebelumnya terhadap hari berikutnya.

Notebook melakukan eksperimen:
- Lag 4 hari
- Lag 10 hari
- Lag 30 hari

---

## 3.4 Training dan Evaluasi Model

Model yang digunakan:

KNeighborsRegressor

### Cara Kerja KNN Regression

1. Mencari sejumlah tetangga terdekat.
2. Menghitung rata-rata nilai target tetangga tersebut.
3. Menggunakan hasil rata-rata sebagai prediksi.

### Metrik Evaluasi

#### Mean Squared Error (MSE)

Mengukur rata-rata kesalahan kuadrat.

Semakin kecil nilainya semakin baik.

#### R² Score

Mengukur kemampuan model menjelaskan variasi data.

Interpretasi:

- Mendekati 1 → sangat baik.
- Mendekati 0 → kurang baik.
- Negatif → performa buruk.

---

## 3.5 Visualisasi Hasil Prediksi

Grafik evaluasi membandingkan:

- Data aktual.
- Data hasil prediksi KNN.

### Tujuan Visualisasi

- Mengetahui apakah model mengikuti pola data.
- Melihat besarnya selisih prediksi.
- Mengevaluasi kualitas model.

---

# Kesimpulan

Tahapan penelitian terdiri dari:

1. Mengambil data NO₂ Sentinel-5P menggunakan OpenEO.
2. Menentukan AOI wilayah Kabupaten Pati.
3. Membersihkan data melalui interpolasi.
4. Mendeteksi outlier menggunakan IQR.
5. Menghapus dan menginterpolasi ulang outlier.
6. Melakukan normalisasi data.
7. Mengubah data menjadi format supervised learning.
8. Membangun model KNN Regression.
9. Mengevaluasi performa model menggunakan MSE dan R².

Pipeline ini menghasilkan data yang lebih bersih dan siap digunakan untuk peramalan konsentrasi NO₂ di wilayah Kabupaten Pati.
