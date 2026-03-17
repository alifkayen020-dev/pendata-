# Tahap Preproscessing

## Penanganan Missing Value dan Normalisasi Data

Penanganan missing value (data yang hilang) menggunakan Weighted K-Nearest Neighbors (WKNN) adalah metode imputasi data (pengisian data kosong) yang lebih akurat dibandingkan KNN standar atau metode rata-rata (mean/median). Metode ini bekerja dengan mencari tetangga terdekat dari data yang hilang dan memberikan bobot (kepentingan) lebih tinggi kepada tetangga yang letaknya lebih dekat.
Berikut adalah penjelasan rinci mengenai penggunaan WKNN untuk missing value:

### A. Konsep Dasar WKNN Imputation

- \*\*KNN Imputation: Mengisi data kosong dengan rata-rata (jika numerik) atau modus (jika kategorikal) dari tetangga terdekat.
- \*\*Weighted KNN (WKNN) Imputation: Memberikan bobot pada tetangga-tetangga tersebut. Tetangga yang jaraknya lebih dekat ke data yang missing akan memiliki pengaruh (bobot) lebih besar dalam menentukan nilai pengisi dibandingkan tetangga yang lebih jauh.

### B. Mengapa Menggunakan WKNN?

- \*\*Ketepatan Lebih Tinggi: WKNN terbukti menghasilkan Root Mean Squared Error (RMSE) dan Mean Absolute Percentage Error (MAPE) yang lebih rendah, terutama saat missing data di atas 20%.
- \*\*Mengurangi Bias: Karena tetangga terdekat diberi bobot lebih besar, nilai imputasi cenderung lebih akurat merepresentasikan karakteristik data yang sesungguhnya.

### Dataset

Berikut adalah dataset yang digunakan:

| No  | IPK | PO      | JML |
| --- | --- | ------- | --- |
| 1   | 2   | 2000000 | 2   |
| 2   | 3   | 3000000 | 3   |
| 3   | 4   | 2000000 | 2   |
| 4   | 2   | 2000000 | 3   |
| 5   | 3   | 3000000 | 2   |
| 6   | 4   | 4000000 | 3   |
| 7   | 2   | 3000000 | ?   |

Data ke-7 memiliki missing value pada kolom **JML**.

---
## 🔹 1. Missing Value dengan Weighted KNN (WKNN)

Normalisasi Data (Min-Max Scaling)*
Karena variabel *IPK* (skala 2-4) dan *PO* (skala ratusan ribu) memiliki perbandingan numerik yang terlalu timpang, kita harus mengubahnya ke rentang [0, 1] menggunakan formula:
* *Rumus Asli:* $X_{new} = \frac{X - X_{min}}{X_{max} - X_{min}}$
* *Rumus Excel:* =(B2-MIN($B$2:$B$7))/(MAX($B$2:$B$7)-MIN($B$2:$B$7)) (Contoh formula untuk sel Normalisasi IPK - ID 1)

Diketahui dari data referensi (ID 1-6):
* Min IPK = 2, Max IPK = 4
* Min PO = 200,000, Max PO = 400,000

*Tabel Hasil Normalisasi:*
| ID | Normalisasi IPK | Normalisasi PO | Nilai JML Asli |
|---|---|---|---|
| 1 | (2-2) / (4-2) = 0 | (200k-200k) / (400k-200k) = 0 | 2 |
| 2 | (3-2) / (4-2) = 0.5 | (300k-200k) / (400k-200k) = 0.5 | 3 |
| 3 | (4-2) / (4-2) = 1 | (200k-200k) / (400k-200k) = 0 | 2 |
| 4 | (2-2) / (4-2) = 0 | (200k-200k) / (400k-200k) = 0 | 3 |
| 5 | (3-2) / (4-2) = 0.5 | (300k-200k) / (400k-200k) = 0.5 | 2 |
| 6 | (4-2) / (4-2) = 1 | (400k-200k) / (400k-200k) = 1 | 3 |
| *7* | (2-2) / (4-2) = 0 | (300k-200k) / (400k-200k) = 0.5 | *?* |

### Konsep WKNN

Semakin dekat jarak → semakin besar pengaruhnya.

### Rumus Prediksi

$$
X = \frac{\sum (w_i \cdot x_i)}{\sum w_i}
$$

---

## Langkah Penyelesaian Manual

### 1. Hitung Jarak (Euclidean Distance)

*Rumus Asli:* $d = \sqrt{(IPK_i - IPK_{target})^2 + (PO_i - PO_{target})^2}$
*Rumus Excel:* =SQRT((F2-$F$8)^2 + (G2-$G$8)^2) (Dimana F dan G adalah kolom nilai normalisasi, dan baris 8 adalah rujukan baris target ID 7)

Gunakan atribut:

- IPK
- PO

Contoh jarak data ke-7 dengan data ke-1:

$$
d = \sqrt{(2-2)^2 + (3000000-2000000)^2}
$$

$$
d = \sqrt{0 + (1000000)^2} = 1000000
$$

Lakukan ke semua data.


*Tabel Hasil Kalkulasi Jarak Per-ID:*
(Terhadap ID 7 yang memiliki **Norm IPK = 0* dan *Norm PO = 0.5)

| ID | Kalkulasi Selisih Kuadrat Jarak (d) | Jarak (Euclidean) Akhir |
|---|---|---|
| *1* | SQRT((0 - 0)^2 + (0 - 0.5)^2) = SQRT(0 + 0.25) | *0.5* |
| *2* | SQRT((0.5 - 0)^2 + (0.5 - 0.5)^2) = SQRT(0.25 + 0) | *0.5* |
| *3* | SQRT((1 - 0)^2 + (0 - 0.5)^2) = SQRT(1 + 0.25) | *1.118034* |
| *4* | SQRT((0 - 0)^2 + (0 - 0.5)^2) = SQRT(0 + 0.25) | *0.5* |
| *5* | SQRT((0.5 - 0)^2 + (0.5 - 0.5)^2) = SQRT(0.25 + 0) | *0.5* |
| *6* | SQRT((1 - 0)^2 + (1 - 0.5)^2) = SQRT(1 + 0.25) | *1.118034* |

---

### 2. Ambil K Tetangga Terdekat

Misal gunakan **K = 2**

Misalnya hasil terdekat:

- Data 2 → JML = 3
- Data 5 → JML = 2

---

### 3. Hitung Bobot


$$
w_i = \frac{1}{d_i}
$$

$$
w_1 = \frac{1}{d_1}, \quad w_2 = \frac{1}{d_2}
$$

* Rumus Asli:* $W = \frac{1}{d^2}$
* *Rumus Excel:* = 1 / (I2^2) (Diasumsikan kolom I merupakan sel kolom khusus 'Jarak')

*Tabel Hasil Perhitungan Bobot Tetangga:*

| ID | Perhitungan Bobot W = 1 / d^2 | Bobot Jarak (W) |
|---|---|---|
| *1* | 1 / (0.5)^2 = 1 / 0.25 | *4* |
| *2* | 1 / (0.5)^2 = 1 / 0.25 | *4* |
| *3* | 1 / (1.118034)^2 = 1 / 1.25 | *0.8* |
| *4* | 1 / (0.5)^2 = 1 / 0.25 | *4* |
| *5* | 1 / (0.5)^2 = 1 / 0.25 | *4* |
| *6* | 1 / (1.118034)^2 = 1 / 1.25 | *0.8* |
---

---

## Implementasi Python WKNN

```python
import numpy as np

# Data
data = np.array([
    [2, 2000000, 2],
    [3, 3000000, 3],
    [4, 2000000, 2],
    [2, 2000000, 3],
    [3, 3000000, 2],
    [4, 4000000, 3]
])

target = np.array([2, 3000000])

def euclidean(a, b):
    return np.sqrt(np.sum((a - b)**2))

def wknn(data, target, k=2):
    distances = []

    for row in data:
        d = euclidean(row[:2], target)
        distances.append((d, row[2]))

    distances.sort(key=lambda x: x[0])
    neighbors = distances[:k]

    weights = []
    values = []

    for d, val in neighbors:
        if d == 0:
            d = 1e-5
        weights.append(1/d)
        values.append(val)

    weights = np.array(weights)
    values = np.array(values)

    return np.sum(weights * values) / np.sum(weights)

hasil = wknn(data, target)
print("Prediksi JML:", hasil)
```

# Hasil Eksekusi Kode WKNN

Hasil dari eksekusi kode Python tersebut adalah:

**Prediksi JML: 2.5**

### 1. Menghitung Jarak (Euclidean Distance)

Program menghitung jarak dari data `target = [2, 3000000]` ke setiap baris data (menggunakan 2 kolom pertama):

- **Baris 1:** $\sqrt{(2-2)^2 + (2000000-3000000)^2} = 1000000$ (Nilai = 2)
- **Baris 2:** $\sqrt{(3-2)^2 + (3000000-3000000)^2} = \sqrt{1^2 + 0} = 1$ (Nilai = 3)
- **Baris 3:** $\sqrt{(4-2)^2 + (2000000-3000000)^2} \approx 1000000$ (Nilai = 2)
- **Baris 4:** $\sqrt{(2-2)^2 + (2000000-3000000)^2} = 1000000$ (Nilai = 3)
- **Baris 5:** $\sqrt{(3-2)^2 + (3000000-3000000)^2} = \sqrt{1^2 + 0} = 1$ (Nilai = 2)
- **Baris 6:** $\sqrt{(4-2)^2 + (4000000-3000000)^2} \approx 1000000$ (Nilai = 3)

### 2. Mencari Tetangga Terdekat ($k=2$)

Setelah jarak diurutkan dari yang terkecil, dua data terdekat (karena `k=2`) adalah:

1. **Baris 2:** Jarak = 1, Nilai = 3
2. **Baris 5:** Jarak = 1, Nilai = 2

### 3. Menghitung Bobot (Weights)

Bobot dihitung dengan rumus $\frac{1}{\text{jarak}}$:

- Bobot tetangga 1 = $\frac{1}{1} = 1$
- Bobot tetangga 2 = $\frac{1}{1} = 1$

### 4. Menghitung Rata-rata Terbobot (Weighted Average)

Program kemudian menghitung hasil akhir dengan mengalikan nilai dengan bobotnya, lalu membaginya dengan total bobot:

- $\text{Hasil} = \frac{(1 \times 3) + (1 \times 2)}{1 + 1} = \frac{5}{2} = 2.5$

---

_Catatan tambahan: Dalam kode ini, perbedaan skala antara fitur pertama (satuan) dan fitur kedua (jutaan) sangat besar. Pada praktiknya, data seperti ini biasanya perlu dinormalisasi (misalnya dengan StandardScaler atau MinMaxScaler) agar fitur dengan angka besar (jutaan) tidak mendominasi perhitungan jarak._

# 🔹 2. Normalisasi Data

## Pengertian Normalisasi

Normalisasi adalah proses mengubah nilai data ke dalam skala tertentu agar setiap atribut memiliki rentang yang sebanding.

Tujuan normalisasi:

- Menghindari dominasi fitur dengan nilai besar (misalnya PO dibanding IPK)
- Memperbaiki akurasi perhitungan jarak (terutama pada KNN)
- Mempercepat proses pembelajaran model

---

## 🔸 Jenis-Jenis Normalisasi

---

### 1. Min-Max Normalization

#### Penjelasan

Metode ini mengubah nilai data ke dalam rentang **0 sampai 1**.  
Nilai terkecil menjadi 0 dan nilai terbesar menjadi 1.

#### Rumus

$$
X' = \frac{X - X_{min}}{X_{max} - X_{min}}
$$

#### Contoh

Data IPK: **2, 3, 4**

- Min = 2
- Max = 4

Hasil:

- 2 → (2-2)/(4-2) = 0
- 3 → (3-2)/(4-2) = 0.5
- 4 → (4-2)/(4-2) = 1

👉 Hasil akhir: **[0, 0.5, 1]**

---

### 2. Z-Score Normalization (Standardisasi)

#### Penjelasan

Metode ini mengubah data berdasarkan rata-rata (mean) dan standar deviasi.  
Hasilnya memiliki:

- Mean = 0
- Standar deviasi = 1

Digunakan saat data memiliki distribusi normal.

#### Rumus

$$
X' = \frac{X - \mu}{\sigma}
$$

#### Contoh

Data IPK: **2, 3, 4**

- Mean (μ) = 3
- Std (σ) ≈ 0.816

Hasil:

- 2 → (2-3)/0.816 = -1.22
- 3 → 0
- 4 → 1.22

👉 Hasil akhir: **[-1.22, 0, 1.22]**

---

### 3. Decimal Scaling

#### Penjelasan

Metode ini membagi data dengan pangkat 10 sampai nilai berada di rentang kecil (biasanya < 1).

Cocok untuk data dengan nilai besar seperti gaji atau PO.

#### Rumus

$$
X' = \frac{X}{10^j}
$$

#### Contoh

Data PO: **2000000, 3000000, 4000000**

- Nilai terbesar = 4.000.000
- j = 7

Hasil:

- 2000000 → 0.2
- 3000000 → 0.3
- 4000000 → 0.4

👉 Hasil akhir: **[0.2, 0.3, 0.4]**

---

## 🔸 Implementasi dengan Sklearn untuk min-max dan z-score Normalization

```python
from sklearn.preprocessing import MinMaxScaler, StandardScaler
import numpy as np

# Dataset (IPK dan PO)
data = np.array([
    [2, 2000000],
    [3, 3000000],
    [4, 4000000]
])

# -------------------
# Min-Max Normalization
# -------------------
minmax = MinMaxScaler()
data_minmax = minmax.fit_transform(data)

# -------------------
# Z-Score Normalization
# -------------------
zscore = StandardScaler()
data_zscore = zscore.fit_transform(data)

print("Hasil Min-Max:")
print(data_minmax)

print("\nHasil Z-Score:")
print(data_zscore)

```

## 🔸 Hasil keseluruhan Normalisasi min-max dan z-score Data Menggunakan Sklearn

### Dataset Awal

| IPK | PO      |
| --- | ------- |
| 2   | 2000000 |
| 3   | 3000000 |
| 4   | 4000000 |

---

## 🔹 Hasil Min-Max Normalization

Hasil normalisasi menggunakan **MinMaxScaler**:

| IPK (MinMax) | PO (MinMax) |
| ------------ | ----------- |
| 0.0          | 0.0         |
| 0.5          | 0.5         |
| 1.0          | 1.0         |

### Penjelasan

- Nilai minimum diubah menjadi **0**
- Nilai maksimum diubah menjadi **1**
- Nilai lainnya berada di antara 0 dan 1

---

## 🔹 Hasil Z-Score Normalization

Hasil normalisasi menggunakan **StandardScaler**:

| IPK (Z-Score) | PO (Z-Score) |
| ------------- | ------------ |
| -1.2247       | -1.2247      |
| 0.0000        | 0.0000       |
| 1.2247        | 1.2247       |

### Penjelasan

- Data dinormalisasi berdasarkan **mean (rata-rata)** dan **standar deviasi**
- Nilai:
  - Di bawah rata-rata → negatif
  - Sama dengan rata-rata → 0
  - Di atas rata-rata → positif

---

## 🔸 Kesimpulan :

- **Min-Max Normalization** menghasilkan data dalam rentang **0–1**
- **Z-Score Normalization** menghasilkan data dengan rata-rata **0**
- Kedua metode menghasilkan nilai yang serupa antar kolom karena distribusi data IPK dan PO bersifat linear

## 🔸 Implementation Manual Decimal Scaling Normalization

```python
import numpy as np

# Data PO
data_po = np.array([2000000, 3000000, 4000000])

def decimal_scaling(data):
    max_val = np.max(np.abs(data))
    j = len(str(int(max_val)))  # jumlah digit

    return data / (10 ** j)

hasil_decimal = decimal_scaling(data_po)

print("Decimal Scaling:")
print(hasil_decimal)
```

## 🔹 Hasil Decimal Scaling

### Dataset Awal (PO)

| PO      |
| ------- |
| 2000000 |
| 3000000 |
| 4000000 |

---

### Hasil Normalisasi Decimal Scaling

| PO (Decimal Scaling) |
| -------------------- |
| 0.2                  |
| 0.3                  |
| 0.4                  |

---

### Penjelasan

- Nilai terbesar = **4.000.000**
- Jumlah digit = **7**
- Maka digunakan pembagi $10^7$

Perhitungan:

- 2000000 / 10^7 = 0.2
- 3000000 / 10^7 = 0.3
- 4000000 / 10^7 = 0.4

---

## 🔸 Kesimpulan

- Decimal Scaling menyederhanakan nilai besar menjadi lebih kecil
- Skala data menjadi lebih mudah diproses
- Metode ini menjaga proporsi antar data tetap sama
