# Memahami Data

Tahap ini merupakan fondasi utama dalam siklus analisis data karena seluruh keputusan pada tahap berikutnya (data preparation, modeling, dan evaluation) sangat bergantung pada pemahaman yang benar terhadap data.

Pada tahap ini, analis mulai berinteraksi langsung dengan dataset untuk memahami:

- Isi data (content)
- Struktur data (structure)
- Tipe dan format data
- Kualitas data
- Potensi permasalahan dalam data
- Kesesuaian data dengan tujuan analisis

Kesalahan dalam memahami data dapat menyebabkan:

- Pemilihan metode yang tidak tepat
- Interpretasi hasil yang salah
- Model yang tidak akurat
- Kesimpulan yang menyesatkan

Oleh karena itu, Data Understanding menjadi tahap yang sangat krusial dalam proses data mining.

---

# Aktivitas dalam Data Understanding

Tahap Data Understanding umumnya terdiri dari beberapa aktivitas utama berikut:

---

## 1. Collect Initial Data

### Pengertian

Collect Initial Data adalah proses pengumpulan data awal dari berbagai sumber yang relevan dengan tujuan analisis.

Tahap ini tidak hanya sekadar mengambil data, tetapi juga memastikan bahwa data yang dikumpulkan sesuai dengan kebutuhan penelitian atau proyek.

### Kegiatan yang Dilakukan

- Mengambil data dari berbagai sumber (file CSV, Excel, database, API, website)
- Memastikan data relevan dengan permasalahan yang ingin diselesaikan
- Mengetahui jumlah data yang tersedia
- Memastikan data dapat diakses dan dibaca dengan baik
- Membuat laporan ringkas mengenai data awal

### Tujuan Tahap Ini

- Menjamin ketersediaan data
- Menilai kelayakan data untuk dianalisis
- Menghindari penggunaan data yang tidak relevan

### Contoh

Mengambil dataset Iris dari file CSV yang berisi 150 data bunga dengan 5 atribut.

---

## 2. Describe Data

### Pengertian

Describe Data adalah tahap mendeskripsikan karakteristik dataset secara menyeluruh untuk memahami struktur dan isi data.

Tahap ini membantu analis mengetahui bagaimana data tersusun dan apa arti dari setiap atribut.

### Hal yang Dilakukan

- Mengetahui jumlah baris (records)
- Mengetahui jumlah kolom (attributes)
- Mengidentifikasi tipe data (numerik, kategorikal, ordinal, dll.)
- Memahami arti setiap atribut
- Melihat struktur dataset secara umum

### Tujuan Tahap Ini

- Memahami gambaran umum dataset
- Mengidentifikasi potensi masalah seperti inkonsistensi format
- Menentukan pendekatan analisis yang sesuai

### Contoh pada Dataset Iris

- 150 baris data
- 5 kolom
- 4 variabel numerik:
  - sepal_length
  - sepal_width
  - petal_length
  - petal_width
- 1 variabel kategorikal:
  - species

Setiap baris merepresentasikan satu sampel bunga iris dengan label spesiesnya.

---

## 3. Explore Data

### Pengertian

Explore Data adalah tahap eksplorasi awal untuk menemukan pola, hubungan, dan karakteristik penting dalam dataset.

Pada tahap ini analis mulai melakukan visualisasi dan analisis statistik sederhana untuk memahami perilaku data.

### Kegiatan yang Dilakukan

- Membuat visualisasi data
- Melihat distribusi data
- Menganalisis hubungan antar variabel
- Mengidentifikasi outlier
- Menilai keseimbangan distribusi kelas

### Tujuan Tahap Ini

- Menemukan pola awal
- Mengidentifikasi fitur yang dominan
- Menentukan variabel yang berpengaruh
- Mendeteksi anomali dalam data

---

# Tools yang Sering Digunakan dalam Explore Data

## 1. Scatter Plot

Digunakan untuk melihat hubungan antara dua variabel numerik.  
Scatter plot membantu melihat:

- Pola penyebaran data
- Kelompok data (cluster)
- Hubungan linier atau non-linier

---

## 2. Box Plot

Digunakan untuk melihat distribusi data dan mendeteksi outlier.

Box plot menunjukkan:

- Nilai minimum
- Kuartil bawah (Q1)
- Median
- Kuartil atas (Q3)
- Nilai maksimum

---

## 3. Correlation

Digunakan untuk mengukur hubungan antar variabel numerik.

Nilai korelasi berada dalam rentang -1 sampai +1:

- Mendekati +1 → hubungan positif kuat
- Mendekati 0 → hubungan lemah
- Mendekati -1 → hubungan negatif kuat

---

## 4. Rank (Feature Importance)

Digunakan untuk mengetahui tingkat kepentingan masing-masing variabel dalam mempengaruhi target atau kelas.

Fitur dengan nilai rank tinggi biasanya:

- Lebih informatif
- Lebih berpengaruh dalam proses klasifikasi

---

# Kesimpulan

Data Understanding merupakan tahap fundamental dalam data mining yang berfungsi sebagai dasar dalam pengambilan keputusan analitis.

Tahap ini memastikan bahwa:

- Data relevan dengan tujuan
- Struktur data dipahami dengan baik
- Tidak terdapat masalah kualitas data
- Pola awal dalam data dapat dikenali

Semakin baik pemahaman terhadap data, semakin tinggi kemungkinan menghasilkan model yang akurat dan dapat dipercaya.
