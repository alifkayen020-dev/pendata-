# Normalisasi dan Pengisian Missing Value dalam Penambangan Data

Dalam pengolahan data untuk data mining, seringkali kita dihadapkan pada atribut dengan skala yang berbeda atau bahkan adanya data yang hilang (_missing value_). Bab ini akan menjelaskan secara rinci proses normalisasi data dengan metode Min-Max dan penanganan _missing value_ menggunakan algoritma K-Nearest Neighbors (KNN). Pembagian tahapan disesuaikan dengan struktur perhitungan dataset Anda.

## Blok 1 (Kolom A-F): Dataset Asli (Data Mentah)

Blok ini berisi rancangan data awal sebelum diproses. Format data tabular ini terdiri dari atribut-atribut dasar karyawan:

- **Kolom A:** ID Karyawan
- **Kolom B:** Departemen (Kategorikal)
- **Kolom C:** Tingkat Pendidikan (Kategorikal)
- **Kolom D:** Status Pernikahan (Kategorikal)
- **Kolom E:** Tahun Lahir (Numerik)
- **Kolom F:** Gaji Per Bulan (Numerik)

Data ini memiliki berbagai macam skala, misalnya atribut `Tahun_Lahir` berkisar di angka ribuan (1985 - 2001), dan `Gaji_Per_Bulan` dalam skala jutaan Rupiah (3.800.000 - 15.000.000). Nilai minimum (Min) dan maksimum (Max) dari atribut numerik ini akan menjadi dasar perhitungan Normalisasi pada tahap selanjutnya.

---

## Blok 2 (Kolom G-H): Hasil Normalisasi Min-Max untuk Atribut Numerik

Perbedaan jarak (skala) antara tahun lahir dan gaji per bulan yang sangat jauh dapat mengacaukan perhitungan ketetanggaan (_Euclidean distance_). Oleh karena itu, kita perlu menyamakan rentang skalanya menjadi rentang standar antara 0 hingga 1 menggunakan **Normalisasi Min-Max**.

- **Kolom G:** Normalisasi Tahun Lahir
- **Kolom H:** Normalisasi Gaji

**Rumus Min-Max Normalization:**
$$X_{baru} = \frac{X - X_{min}}{X_{max} - X_{min}}$$

_Keterangan:_

- $X$: Nilai atribut numerik asli saat ini.
- $X_{min}$, $X_{max}$: Nilai minimum dan maksimum seluruh sampel dari kolom atribut tersebut.

**Contoh Perhitungan untuk Karyawan K001:**

- **Normalisasi Tahun Lahir** (Data Asli: Tahun = 1990, Min = 1985, Max = 2001)
  $$X_{baru} = \frac{1990 - 1985}{2001 - 1985} = \frac{5}{16} = 0,3125$$
- **Normalisasi Gaji** (Data Asli: Gaji = 8.500.000, Min = 3.800.000, Max = 15.000.000)
  $$X_{baru} = \frac{8.500.000 - 3.800.000}{15.000.000 - 3.800.000} = \frac{4.700.000}{11.200.000} \approx 0,4196...$$

---

## Blok 3 (Kolom I-K): Perhitungan Jarak Kategorikal

Pada proses pencarian jarak (distansi), atribut berjenis kategorikal tidak bisa diproses menggunakan pengurangan angka biasa. Jarak kategorikal dihitung menggunakan logika perbandingan kesamaan **Simple Matching Coefficient**:

- Jika dua nilai kategorikal **Sama**, maka Jarak = **0**
- Jika dua nilai kategorikal **Berbeda**, maka Jarak = **1**

Ketiga kolom ini mewakili setiap variabel kategorikal:

- **Kolom I:** Jarak Pendidikan (_S1 vs S2, dll_)
- **Kolom J:** Jarak Departemen (_IT vs Keuangan, dll_)
- **Kolom K:** Jarak Status Pernikahan (_Menikah vs Belum Menikah_)

---

## Blok 4 (Kolom L): Jarak Kategorikal Gabungan

Setelah nilai per-kolom kategorikal didapatkan, semua skor jarak kategorikal ini digabungkan dan dirata-rata.

**Rumus Jarak Kategorikal Gabungan:**
$$d_{kategorikal} = \frac{\sum \text{Jarak Atribut Kategorikal}}{\text{Jumlah Atribut Kategorikal}}$$

**Contoh Perhitungan (K001 vs rujukan tertentu):**
Misalkan antara K001 (S1, IT, Menikah) dan Target rujukan (S1, HR, Belum Menikah):

- Jarak Pendidikan saling sama (S1 vs S1) = 0
- Jarak Departemen berbeda (IT vs HR) = 1
- Jarak Status Pernikahan berbeda (Menikah vs Belum Menikah) = 1
- **Jarak Gabungan Kategorikal** = $\frac{0 + 1 + 1}{3} = \frac{2}{3} \approx 0,6666...$

---

## Blok 5 (Kolom M): Perhitungan Jarak Numerik (Euclidean)

Atribut numerik yang telah dinormalisasi (Kolom G dan Kolom H) lalu dihitung selisih jaraknya menggunakan **Euclidean Distance**.

**Rumus Jarak Numerik Euclidean:**
$$d_{numerik} = \sqrt{(X_{norm_1} - Y_{norm_1})^2 + (X_{norm_2} - Y_{norm_2})^2}$$

---

## Blok 6 (Kolom N): Jarak Gabungan (Final Distance)

Langkah terakhir dari pengukuran kemiripan antar baris data (_instance_) adalah dengan menggabungkan komputasi Jarak Kategorikal (Kolom L) dan Jarak Numerik (Kolom M).

**Rumus Jarak Final:**
$$Distance_{total} = \sqrt{(d_{kategorikal})^2 + (d_{numerik})^2}$$

Semakin kecil nilai di kolom ini, maka semakin **"mirip / dekat"** sifat antar-karyawan tersebut secara holistik.

---

## Blok 7 (Kolom P-V): Penanganan Missing Value (Metode KNN)

Kolom P hingga V digunakan untuk mempraktikkan pengisian **Missing Value** menggunakan algoritma **K-Nearest Neighbors (KNN)** dengan $K=2$ (2 Tetangga Terdekat).

Pada dataset ini, Gaji karyawan **K013** (HR, D3, Menikah, Tahun 1993) sengaja dihilangkan. Nilai asli sebelum dihapus adalah **5.500.000**. Semua jarak pada Blok 7 dihitung terhadap **K013** sebagai target.

| Kolom | Isi                                       |
| :---- | :---------------------------------------- |
| **P** | Jarak Departemen (ke K013)                |
| **Q** | Jarak Pendidikan (ke K013)                |
| **R** | Jarak Status Pernikahan (ke K013)         |
| **S** | Jarak Kategorikal Gabungan                |
| **T** | Jarak Numerik Khusus (Hanya Tahun Lahir)  |
| **U** | Jarak Final ke K013                       |
| **V** | Hasil Missing Value (2 Tetangga Terdekat) |

---

### Langkah 1: Hitung Jarak Kategorikal Setiap Karyawan ke K013

Data K013: **HR, D3, Menikah, Tahun 1993**

**Contoh K001 (IT, S1, Menikah) vs K013:**

- Departemen: IT vs HR (Beda) = 1
- Pendidikan: S1 vs D3 (Beda) = 1
- Status: Menikah vs Menikah (Sama) = 0
- **Jarak Kategorikal (S):** $\frac{1 + 1 + 0}{3} = 0,6667$

---

### Langkah 2: Hitung Jarak Numerik Khusus (Kolom T)

Karena Gaji K013 hilang, jarak numerik hanya dihitung berdasarkan **Tahun Lahir**.
Normalisasi Tahun K013 = $\frac{1993 - 1985}{2001 - 1985} = 0,5$

**Rumus:** $T = | \text{Norm\_Tahun\_Karyawan} - 0,5 |$

---

### Langkah 3: Hitung Jarak Final ke K013 (Kolom U)

**Rumus:** $U = \sqrt{S^2 + T^2}$

- **K016:** $U \approx 0,3391$
- **K018:** $U \approx 0,3391$
- **K022:** $U \approx 0,3560$

---

### Langkah 4: Urutkan dan Pilih 2 Tetangga Terdekat

Urutan jarak terkecil ke K013:

1. **K016:** Jarak 0,3391 (Gaji: 5.000.000)
2. **K018:** Jarak 0,3391 (Gaji: 6.500.000)

---

### Langkah 5: Estimasi Missing Value (Rata-rata Gaji Tetangga)

$$Gaji_{imputasi} = \frac{5.000.000 + 6.500.000}{2} = 5.750.000$$

### Kesimpulan

Nilai Gaji K013 diestimasi menjadi **Rp 5.750.000**. Dibandingkan nilai asli (5.500.000), selisihnya hanya **250.000**, menunjukkan metode KNN cukup akurat.
