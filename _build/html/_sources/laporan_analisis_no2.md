# Laporan Analisis Data NO2 dan Area Studi

## Deskripsi Proyek

Dokumen ini merangkum hasil analisis data kadar NO2 (Nitrogen Dioksida) yang diperoleh melalui proses *data crawling*, meliputi deteksi *outlier*, interpolasi data, serta visualisasi area studi menggunakan GeoJSON.

---

## 1. Deteksi Outlier Data NO2 (Metode IQR)

Proses pertama adalah mendeteksi nilai-nilai ekstrem (*outlier*) pada data NO2 menggunakan metode **Interquartile Range (IQR)**. Titik-titik yang berada di luar batas atas (*upper bound*) dan batas bawah (*lower bound*) ditandai sebagai *outlier*.

![Deteksi Outlier Data NO2](outlier_detection.png)

*Gambar 1: Visualisasi deteksi outlier pada data NO2 menggunakan metode IQR. Titik merah menunjukkan data yang teridentifikasi sebagai outlier, garis oranye putus-putus adalah upper bound, dan garis biru putus-putus adalah lower bound.*

---

## 2. Data NO2 Setelah Outlier Removal & Interpolasi

Setelah *outlier* dihapus, nilai yang hilang (*missing values*) diisi menggunakan metode **interpolasi** sehingga data menjadi kontinu dan siap digunakan untuk analisis lebih lanjut.

![Data NO2 Setelah Interpolasi](interpolated_data.png)

*Gambar 2: Plot data NO2 setelah proses outlier removal dan interpolasi. Data tampak lebih bersih dan kontinu tanpa lonjakan ekstrem.*

---

## 3. Area Studi — Wilayah Pati, Jawa Tengah (GeoJSON)

Area studi yang digunakan dalam penelitian ini mencakup wilayah **Kabupaten Pati** dan sekitarnya di Jawa Tengah. Batas wilayah digambarkan menggunakan poligon dalam format **GeoJSON** dan divisualisasikan melalui platform geojson.io.

![Peta Area Studi GeoJSON](geojson_map.jpeg)

*Gambar 3: Visualisasi area studi dalam bentuk poligon GeoJSON di atas peta wilayah Jawa Tengah, mencakup Kabupaten Pati dan sekitarnya.*

---

## Ringkasan Alur Analisis

1. **Pengumpulan Data** — Data NO2 diperoleh melalui proses *data crawling*
2. **Deteksi Outlier** — Identifikasi nilai ekstrem menggunakan metode IQR
3. **Pembersihan Data** — Penghapusan *outlier* dari dataset
4. **Interpolasi** — Pengisian nilai yang hilang agar data kontinu
5. **Visualisasi Area Studi** — Penentuan batas wilayah menggunakan GeoJSON

---

*Dokumen ini dibuat sebagai bagian dari analisis data NO2 untuk wilayah studi Kabupaten Pati, Jawa Tengah.*
