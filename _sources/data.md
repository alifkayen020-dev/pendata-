# HIMPUNAN — Data

---

## Data Campuran dari Dataset Iris

Dataset Iris asli memiliki 4 atribut numerik dan 1 atribut kategorikal.  
Untuk membentuk data campuran (numerik, ordinal, kategorikal, dan biner), dilakukan proses transformasi pada beberapa atribut tanpa mengambil data luar.

### Struktur Data Campuran

| No | Sepal Length (Numerik) | Sepal Width (Numerik) | Petal Length (Ordinal) | Species (Kategorikal) | Petal Width (Biner) |
|----|------------------------|----------------------|-----------------------|----------------------|--------------------|
| 1  | 5.1                    | 3.5                  | Pendek (1)            | setosa               | 0                  |
| 2  | 4.9                    | 3.0                  | Pendek (1)            | setosa               | 0                  |
| 3  | 4.7                    | 3.2                  | Pendek (1)            | setosa               | 0                  |
| 4  | 4.6                    | 3.1                  | Pendek (1)            | setosa               | 0                  |
| 5  | 5.0                    | 3.6                  | Pendek (1)            | setosa               | 0                  |
| 6  | 5.4                    | 3.9                  | Pendek (1)            | setosa               | 0                  |
| 7  | 4.6                    | 3.4                  | Pendek (1)            | setosa               | 0                  |
| 8  | 5.0                    | 3.4                  | Pendek (1)            | setosa               | 0                  |
| 9  | 4.4                    | 2.9                  | Pendek (1)            | setosa               | 0                  |
| 10 | 4.9                    | 3.1                  | Pendek (1)            | setosa               | 0                  |

### Penjelasan Pembentukan Tipe Data

- **Numerik:** Sepal Length dan Sepal Width digunakan langsung dari data asli karena berbentuk angka kontinu.  
- **Ordinal:** Petal Length dikategorikan menjadi:  
  - < 3 cm → Pendek (1)  
  - 3 – 5 cm → Sedang (2)  
  - > 5 cm → Panjang (3)  
- **Kategorikal:** Species (setosa, versicolor, virginica) merupakan label tanpa tingkatan.  
- **Biner:** Petal Width dikonversi menjadi:  
  - ≤ 1.5 cm → 0  
  - > 1.5 cm → 1  

### Kesimpulan

Dataset Iris secara asli hanya memiliki data numerik dan kategorikal.  
Namun melalui proses transformasi (feature engineering), berhasil dibentuk data campuran yang terdiri dari numerik, ordinal, kategorikal, dan biner.  
Data campuran ini dapat digunakan untuk analisis lanjutan seperti pengukuran jarak menggunakan metode **Gower Distance** atau clustering data campuran.

---

## Perhitungan Jarak (Euclidean Distance)

Pada tahap **Data Understanding**, dilakukan perhitungan jarak antar data menggunakan metode **Euclidean Distance**.  
Metode ini digunakan untuk mengukur tingkat kemiripan antar dua sampel data berdasarkan atribut numerik.

**Rumus Euclidean Distance:**

\[
d(x,y) = \sqrt{(x_1 - y_1)^2 + (x_2 - y_2)^2 + (x_3 - y_3)^2 + (x_4 - y_4)^2}
\]

Variabel numerik yang digunakan:

- sepal_length  
- sepal_width  
- petal_length  
- petal_width  

### Contoh Perhitungan Manual

Data ke-1: (5.1, 3.5, 1.4, 0.2)  
Data ke-2: (4.9, 3.0, 1.4, 0.2)

\[
\begin{align*}
d &= \sqrt{(5.1-4.9)^2 + (3.5-3.0)^2 + (1.4-1.4)^2 + (0.2-0.2)^2} \\
  &= \sqrt{0.2^2 + 0.5^2 + 0 + 0} \\
  &= \sqrt{0.04 + 0.25} \\
  &= \sqrt{0.29} \\
  &= 0.538516
\end{align*}
\]

### Hasil Perhitungan Jarak dari Data ke-1

| Index Data | Jarak    |
|------------|----------|
| 0          | 0.000000 |
| 1          | 0.538516 |
| 2          | 0.509902 |
| 3          | 0.648074 |
| 4          | 0.141421 |

### Analisis

Data ke-4 memiliki jarak paling kecil terhadap data ke-1: **0.141421**, menunjukkan karakteristik paling mirip.  
Semakin kecil jarak → semakin mirip data.  
Semakin besar jarak → semakin berbeda karakteristik antar sampel.

### Kesimpulan

Perhitungan jarak menggunakan Euclidean Distance membantu memahami pola kemiripan antar data.  
Ini penting untuk algoritma klasifikasi seperti **K-Nearest Neighbor (KNN)**, karena menentukan kelas berdasarkan data terdekat.

---

## Perhitungan Jarak Data Campuran (Numerik, Ordinal, Kategorikal, Biner)

### 1. Pengertian

Untuk data campuran, Euclidean Distance tidak cukup karena tipe variabel berbeda.  
Gunakan **Gower Distance**, mampu menghitung jarak untuk data dengan tipe campuran.

### 2. Pembentukan Data Campuran dari Dataset Iris

- **Numerik:** sepal_length, petal_length  
- **Ordinal:** ukuran petal (Pendek=1, Sedang=2, Panjang=3)  
- **Kategorikal:** species  
- **Biner:** petal_width > 1.0 (Ya=1, Tidak=0)  

Variabel ordinal dibuat dengan mengelompokkan panjang petal menjadi 3 kategori:

- Petal Length < 2.5 → Pendek (1)  
- 2.5 – 5.0 → Sedang (2)  
- > 5.0 → Panjang (3)  

### 3. Rumus Gower Distance

\[
D(i,j) = \frac{1}{p} \sum d_{ij}
\]

- **Numerik:** selisih dinormalisasi  
- **Ordinal:** dikonversi ke skala 0–1  
- **Kategorikal:** 0 jika sama, 1 jika berbeda  
- **Biner:** 0 jika sama, 1 jika berbeda  

### 4. Contoh Perhitungan

| Variabel | Data 1 | Data 2 | Perhitungan |
|----------|--------|--------|------------|
| Sepal Length (Numerik) | 5.1 | 4.9 | Selisih dinormalisasi |
| Petal Category (Ordinal) | 1 | 1 | 0 (sama) |
| Species (Kategorikal) | Setosa | Setosa | 0 (sama) |
| Petal Width Binary | 0 | 0 | 0 (sama) |

### 5. Analisis

- Data dari spesies sama → nilai jarak lebih kecil  
- Variabel kategorikal & biner mempertegas perbedaan antar kelas  
- Variabel numerik & ordinal memberikan informasi variasi ukuran

### Kesimpulan

- Gower Distance lebih sesuai untuk data campuran  
- Menangani berbagai tipe variabel tanpa kehilangan informasi  
- Analisis kemiripan antar data menjadi lebih akurat dan representatif