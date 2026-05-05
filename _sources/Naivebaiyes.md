# Analisis Data IRIS dengan Gaussian Naive Bayes Classifier

> **Mata Kuliah:** Struktur Data / Machine Learning  
> **Dataset:** IRIS.csv (UCI Machine Learning Repository)  
> **Library:** scikit-learn, pandas, numpy, matplotlib, seaborn  
> **Referensi:** https://scikit-learn.org/stable/api/sklearn.naive_bayes.html

---

## 1. Pendahuluan

Proyek ini mengimplementasikan **Gaussian Naive Bayes Classifier** menggunakan Python dan scikit-learn untuk mengklasifikasikan spesies bunga iris. Naive Bayes merupakan algoritma klasifikasi probabilistik berbasis **Teorema Bayes** yang mengasumsikan independensi antar fitur (*naive assumption*).

### Teorema Bayes

$$P(C_k \mid \mathbf{x}) = \frac{P(\mathbf{x} \mid C_k) \cdot P(C_k)}{P(\mathbf{x})}$$

Untuk distribusi Gaussian, likelihood dihitung dengan:

$$P(x_i \mid C_k) = \frac{1}{\sqrt{2\pi\sigma_{k,i}^2}} \exp\left(-\frac{(x_i - \mu_{k,i})^2}{2\sigma_{k,i}^2}\right)$$

Di mana $\mu_{k,i}$ adalah rata-rata fitur $i$ untuk kelas $k$, dan $\sigma_{k,i}$ adalah standar deviasinya.

---

## 2. Dataset

Dataset IRIS merupakan dataset klasik dalam *machine learning* yang pertama kali diperkenalkan oleh Ronald Fisher pada tahun 1936.

| Properti | Nilai |
|---|---|
| Jumlah Sampel | 150 |
| Jumlah Fitur | 4 |
| Jumlah Kelas | 3 |
| Missing Values | 0 |
| Sumber | UCI Machine Learning Repository |

### Fitur Dataset

| No | Nama Fitur | Satuan | Deskripsi |
|---|---|---|---|
| 1 | `sepal_length` | cm | Panjang kelopak bunga luar |
| 2 | `sepal_width` | cm | Lebar kelopak bunga luar |
| 3 | `petal_length` | cm | Panjang mahkota bunga |
| 4 | `petal_width` | cm | Lebar mahkota bunga |

### Distribusi Kelas

| Kelas | Jumlah Sampel | Proporsi |
|---|---|---|
| Iris-setosa | 50 | 33.33% |
| Iris-versicolor | 50 | 33.33% |
| Iris-virginica | 50 | 33.33% |

Dataset ini **perfectly balanced** — tidak ada masalah class imbalance.

### Statistik Deskriptif

| Fitur | Min | Max | Mean | Std |
|---|---|---|---|---|
| sepal_length | 4.30 | 7.90 | 5.843 | 0.828 |
| sepal_width | 2.00 | 4.40 | 3.054 | 0.434 |
| petal_length | 1.00 | 6.90 | 3.759 | 1.764 |
| petal_width | 0.10 | 2.50 | 1.199 | 0.763 |

---

## 3. Eksplorasi Data (EDA)

### 3.1 Distribusi Fitur per Kelas

![EDA — Histogram dan Boxplot]![Scatter Plot](images/fig1_eda.png)

**Temuan:**
- **Iris-setosa** memiliki nilai `petal_length` dan `petal_width` yang jauh lebih kecil dibanding dua kelas lainnya → **sangat mudah dipisahkan**
- **Iris-versicolor** dan **Iris-virginica** memiliki overlap pada `sepal_length` dan `sepal_width`
- Fitur petal memberikan separasi kelas yang lebih baik dibanding fitur sepal

### 3.2 Korelasi Fitur

![Correlation & Scatter]![Scatter Plot](images/fig2_corr.png)
**Temuan Korelasi:**
- `petal_length` dan `petal_width` berkorelasi sangat tinggi (**r = 0.96**) → fitur paling informatif
- `sepal_length` berkorelasi moderat dengan petal features
- `sepal_width` berkorelasi rendah/negatif dengan fitur lain → fitur paling independen

> ⚠️ **Catatan:** Korelasi tinggi antar fitur sebenarnya melanggar asumsi independensi Naive Bayes. Namun dalam praktik, GaussianNB tetap memberikan performa baik meskipun asumsi ini dilanggar.

---

## 4. Preprocessing

### 4.1 Encoding Label

Kelas string dikonversi ke integer menggunakan `LabelEncoder`:

```python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()
y = le.fit_transform(y_raw)
# Iris-setosa=0, Iris-versicolor=1, Iris-virginica=2
```

### 4.2 Train/Test Split

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y,
    test_size=0.2,      # 80% train, 20% test
    random_state=42,    # reproducibility
    stratify=y          # jaga proporsi kelas
)
```

| Set | Jumlah Sampel |
|---|---|
| Training | 120 sampel (80%) |
| Testing | 30 sampel (20%) |

> **Stratify** digunakan untuk memastikan setiap kelas terwakili secara proporsional di kedua set.

---

## 5. Model Naive Bayes

### Jenis-jenis Naive Bayes di scikit-learn

| Kelas | Asumsi Distribusi | Cocok untuk |
|---|---|---|
| `GaussianNB` | Distribusi Normal/Gaussian | **Fitur kontinu** (seperti IRIS) |
| `MultinomialNB` | Distribusi Multinomial | Count data, NLP |
| `BernoulliNB` | Distribusi Bernoulli | Fitur biner |
| `CategoricalNB` | Distribusi Kategorik | Fitur kategorikal diskret |
| `ComplementNB` | Komplemen Multinomial | Dataset tidak seimbang |

### Pemilihan Model

Dataset IRIS memiliki **fitur kontinu** (nilai desimal), sehingga **`GaussianNB`** adalah pilihan yang tepat karena:
1. Fitur berupa nilai real (panjang/lebar dalam cm)
2. Distribusi fitur mendekati distribusi normal per kelas
3. Tidak memerlukan normalisasi/scaling (GaussianNB mengestimasi μ dan σ sendiri)

```python
from sklearn.naive_bayes import GaussianNB

gnb = GaussianNB()
gnb.fit(X_train, y_train)
```

---

## 6. Pelatihan Model

### 6.1 Parameter yang Diestimasi Model

Setelah training, model mempelajari **mean (μ)** dan **variance (σ²)** untuk setiap kombinasi fitur–kelas:

#### Class Prior P(C)

| Kelas | Prior |
|---|---|
| Iris-setosa | 0.3333 |
| Iris-versicolor | 0.3333 |
| Iris-virginica | 0.3333 |

#### Mean (μ) per Fitur per Kelas

| Kelas | sepal_length | sepal_width | petal_length | petal_width |
|---|---|---|---|---|
| Iris-setosa | 4.9983 | 3.4483 | 1.4633 | 0.2433 |
| Iris-versicolor | 5.9447 | 2.7842 | 4.2526 | 1.3289 |
| Iris-virginica | 6.6184 | 2.9868 | 5.5684 | 2.0342 |

#### Std Deviasi (σ) per Fitur per Kelas

| Kelas | sepal_length | sepal_width | petal_length | petal_width |
|---|---|---|---|---|
| Iris-setosa | 0.3513 | 0.3858 | 0.1769 | 0.1064 |
| Iris-versicolor | 0.5181 | 0.3083 | 0.4662 | 0.1969 |
| Iris-virginica | 0.6270 | 0.3176 | 0.5516 | 0.2724 |

### 6.2 Cara Prediksi

Untuk sampel baru **x**, model menghitung:

```
P(C=setosa | x)     ∝ P(setosa) × P(x₁|setosa) × P(x₂|setosa) × P(x₃|setosa) × P(x₄|setosa)
P(C=versicolor | x) ∝ P(versicolor) × P(x₁|versicolor) × ...
P(C=virginica | x)  ∝ P(virginica) × P(x₁|virginica) × ...
```

Kelas dengan probabilitas posterior tertinggi dipilih sebagai prediksi.

---

## 7. Evaluasi Model

### 7.1 Hasil Prediksi pada Test Set

![Model Evaluation]![Scatter Plot](images/fig3_results.png)

### 7.2 Confusion Matrix

|  | Pred: setosa | Pred: versicolor | Pred: virginica |
|---|---|---|---|
| **Act: setosa** | **10** | 0 | 0 |
| **Act: versicolor** | 0 | **9** | 1 |
| **Act: virginica** | 0 | 0 | **10** |

- **Iris-setosa**: 10/10 benar (100%) → kelas paling mudah diklasifikasi
- **Iris-versicolor**: 9/10 benar, 1 salah diprediksi sebagai virginica
- **Iris-virginica**: 10/10 benar (100%)

### 7.3 Classification Report

| Kelas | Precision | Recall | F1-Score | Support |
|---|---|---|---|---|
| Iris-setosa | 1.0000 | 1.0000 | 1.0000 | 10 |
| Iris-versicolor | 1.0000 | 0.9000 | 0.9474 | 10 |
| Iris-virginica | 0.9091 | 1.0000 | 0.9524 | 10 |
| **macro avg** | **0.9697** | **0.9667** | **0.9666** | **30** |
| **weighted avg** | **0.9697** | **0.9667** | **0.9666** | **30** |

### 7.4 Ringkasan Metrik

| Metrik | Nilai |
|---|---|
| **Accuracy** | **96.67%** |
| Macro Precision | 96.97% |
| Macro Recall | 96.67% |
| Macro F1-Score | 96.66% |

> **Interpretasi:** Model berhasil mengklasifikasikan **29 dari 30 sampel** dengan benar. Satu kesalahan terjadi pada Iris-versicolor yang diprediksi sebagai Iris-virginica, yang dapat dipahami karena kedua kelas ini memiliki karakteristik fitur yang lebih mirip.

---

## 8. Cross Validation

### 8.1 5-Fold Stratified Cross Validation

```python
from sklearn.model_selection import cross_val_score, StratifiedKFold

cv = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)
cv_scores = cross_val_score(gnb, X, y, cv=cv, scoring='accuracy')
```

| Fold | Accuracy |
|---|---|
| Fold 1 | 96.67% |
| Fold 2 | 96.67% |
| Fold 3 | 90.00% |
| Fold 4 | 100.00% |
| Fold 5 | 90.00% |
| **Mean ± Std** | **94.67% ± 4.00%** |

### 8.2 Interpretasi Cross Validation

- **Mean accuracy 94.67%** menunjukkan model konsisten generalize dengan baik
- **Std 4.00%** tergolong rendah → model stabil, tidak overfitting
- Fold 4 mencapai 100% → ada pembagian data tertentu yang menguntungkan
- Fold 3 dan 5 sebesar 90% → tetap dalam batas performa baik

---

## 9. Visualisasi Decision Boundary

![Decision Boundary & Likelihood]!![Scatter Plot](images/fig4_boundary.png)

### 9.1 Decision Boundary (Petal Features)

Menggunakan dua fitur terbaik (petal_length dan petal_width):
- **Setosa** (hijau) terpisah dengan sempurna dari dua kelas lain
- **Versicolor** (biru) dan **Virginica** (kuning) memiliki sedikit overlap di batas decision boundary
- Batas antar kelas berbentuk **kurva non-linear** — karakteristik GaussianNB

### 9.2 Gaussian Likelihood

Grafik menunjukkan distribusi Gaussian yang diestimasi model untuk fitur `petal_length`:
- **Setosa**: μ=1.46 — distribusi sangat sempit (σ kecil), menandakan nilai petal kecil dan seragam
- **Versicolor**: μ=4.25 — distribusi lebih lebar
- **Virginica**: μ=5.57 — distribusi paling lebar (σ terbesar)

Tumpang tindih antara distribusi Versicolor dan Virginica menjelaskan mengapa terjadi 1 misclassification.

---

## 10. Contoh Prediksi

```python
# Prediksi sampel baru
samples = [
    [5.1, 3.5, 1.4, 0.2],   # sepal/petal kecil → setosa
    [6.2, 2.9, 4.3, 1.3],   # fitur medium     → versicolor
    [7.3, 2.9, 6.3, 1.8],   # fitur besar      → virginica
]

for sample in samples:
    pred = gnb.predict([sample])[0]
    prob = gnb.predict_proba([sample])[0]
    print(f"{sample} → {le.inverse_transform([pred])[0]}")
```

**Output:**

| Sampel | Prediksi | P(setosa) | P(versicolor) | P(virginica) |
|---|---|---|---|---|
| [5.1, 3.5, 1.4, 0.2] | **Iris-setosa** | ~1.000 | ~0.000 | ~0.000 |
| [6.2, 2.9, 4.3, 1.3] | **Iris-versicolor** | ~0.000 | ~0.993 | ~0.007 |
| [7.3, 2.9, 6.3, 1.8] | **Iris-virginica** | ~0.000 | ~0.001 | ~0.999 |

---

## 11. Kesimpulan

### 11.1 Ringkasan Hasil

| Aspek | Hasil |
|---|---|
| Algoritma | Gaussian Naive Bayes (`GaussianNB`) |
| Accuracy (test set) | **96.67%** |
| Cross Validation Mean | **94.67% ± 4.00%** |
| Kelas paling mudah | Iris-setosa (precision & recall = 1.0) |
| Kelas paling sulit | Iris-versicolor (1 misclassification) |
| Overfitting | Tidak (CV std rendah) |

### 11.2 Kelebihan Gaussian Naive Bayes

- ✅ **Cepat** dan efisien secara komputasi — O(n·d) untuk training
- ✅ **Tidak memerlukan hyperparameter tuning** yang kompleks
- ✅ **Bekerja baik dengan dataset kecil** (seperti IRIS)
- ✅ **Tidak memerlukan feature scaling** (normalisasi)
- ✅ **Probabilistic output** — memberikan confidence score setiap prediksi

### 11.3 Kekurangan Gaussian Naive Bayes

- ❌ Asumsi independensi fitur sering tidak realistis (fitur IRIS saling berkorelasi)
- ❌ Asumsi distribusi Gaussian mungkin tidak selalu tepat
- ❌ Performa lebih rendah dibanding model kompleks (SVM, Random Forest) untuk dataset besar

### 11.4 Perbandingan dengan Baseline

Dengan akurasi **96.67%** dan cross-validation **94.67%**, Gaussian Naive Bayes menunjukkan performa yang **sangat kompetitif** untuk dataset IRIS — sebanding dengan algoritma yang jauh lebih kompleks, membuktikan bahwa model sederhana pun dapat memberikan hasil luar biasa jika karakteristik data sesuai.

---

## 12. Referensi

1. Fisher, R.A. (1936). *The use of multiple measurements in taxonomic problems.* Annals of Eugenics, 7(2), 179-188.
2. scikit-learn. *sklearn.naive_bayes.GaussianNB.* https://scikit-learn.org/stable/api/sklearn.naive_bayes.html
3. Pedregosa et al. (2011). *Scikit-learn: Machine Learning in Python.* JMLR 12, 2825-2830.
4. Murphy, K.P. (2012). *Machine Learning: A Probabilistic Perspective.* MIT Press.
5. Géron, A. (2019). *Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow (2nd ed.).* O'Reilly Media.

---

## Appendix: Cara Menjalankan

```bash
# 1. Install dependencies
pip install scikit-learn pandas numpy matplotlib seaborn

# 2. Pastikan IRIS.csv ada di folder yang sama

# 3. Jalankan script
python naive_bayes_iris.py
```

**File yang dihasilkan:**
- `images/fig1_eda.png` — Histogram dan Boxplot per fitur
- `images/fig2_corr.png` — Heatmap korelasi dan scatter plot
- `images/fig3_results.png` — Confusion matrix, per-class metrics, cross validation
- `images/fig4_boundary.png` — Decision boundary, prior, dan likelihood

---

*Dibuat dengan Python 3.x · scikit-learn · pandas · matplotlib · seaborn*