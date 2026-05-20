#Regresi Linear 

Analisis Regresi Linier 

> **Proyek:** Analisis data menggunakan Regresi Linier  
> **Metode:** sklearn `LinearRegression` + Analitik β̂ = (XᵀX)⁻¹XᵀY  
> **Data:** 7 titik koordinat dari GeoGebra

---

## 1. Dataset

Data diambil dari GeoGebra Calculator Suite (7 titik koordinat):

| Point | X | Y |
|:-----:|:-:|:-:|
| A     | 2 | 2 |
| B     | 4 | 3 |
| C     | 5 | 5 |
| D     | 3 | 4 |
| E     | 3 | 3 |
| F     | 4 | 5 |
| G     | 5 | 6 |

**Statistik Deskriptif:**
- n = 7
- x̄ (rata-rata X) = 26/7 ≈ **3.7143**
- ȳ (rata-rata Y) = 28/7 = **4.0000**

---

## 2. Model Regresi Linier

Model regresi linier sederhana:

```
ŷ = β₀ + β₁·x
```

- **β₀** = intercept (nilai ŷ saat x = 0)
- **β₁** = slope / koefisien regresi (kenaikan ŷ per satuan x)

---

## 3. Metode 1 — Menggunakan `sklearn`

```python
import numpy as np
from sklearn.linear_model import LinearRegression

X = np.array([2, 4, 5, 3, 3, 4, 5]).reshape(-1, 1)
Y = np.array([2, 3, 5, 4, 3, 5, 6])

model = LinearRegression()
model.fit(X, Y)

print("β₁ (slope)     =", model.coef_[0])
print("β₀ (intercept) =", model.intercept_)
print("R² score       =", model.score(X, Y))
```

**Hasil:**
| Parameter | Nilai |
|-----------|-------|
| β₁ (slope) | **1.076923** |
| β₀ (intercept) | **0.000000** |
| R² | **0.7179** |

**Persamaan regresi:**
```
ŷ = 0 + 1.0769·x
ŷ = 1.0769·x
```

---

## 4. Metode 2 — Analitik: β̂ = (XᵀX)⁻¹XᵀY

### 4.1 Penjelasan Formula

Formula ini merupakan solusi **Ordinary Least Squares (OLS)** yang meminimalkan jumlah kuadrat residual:

```
β̂ = (XᵀX)⁻¹ · Xᵀ · Y
```

Di mana:
- **X** = matriks desain (kolom 1 untuk intercept + kolom nilai x)
- **Y** = vektor target
- **Xᵀ** = transpose dari X
- **(XᵀX)⁻¹** = invers dari XᵀX

### 4.2 Konstruksi Matriks X

Tambahkan kolom **1** di depan untuk menghitung intercept β₀:

```
X_matrix = 
⎡1  2⎤
⎢1  4⎥
⎢1  5⎥
⎢1  3⎥
⎢1  3⎥
⎢1  4⎥
⎣1  5⎦
```

```
Y_vector = [2, 3, 5, 4, 3, 5, 6]ᵀ
```

### 4.3 Langkah Perhitungan

**Step 1: Hitung XᵀX**

```
XᵀX = ⎡ n      Σxᵢ    ⎤ = ⎡  7    26  ⎤
      ⎣ Σxᵢ   Σxᵢ²   ⎦   ⎣ 26   104  ⎦
```

**Step 2: Hitung XᵀY**

```
XᵀY = ⎡ ΣYᵢ    ⎤ = ⎡ 28  ⎤
      ⎣ ΣxᵢYᵢ  ⎦   ⎣ 112 ⎦
```

**Step 3: Hitung (XᵀX)⁻¹**

```
(XᵀX)⁻¹ = (1/det) · ⎡  104  -26 ⎤
                      ⎣  -26    7 ⎦

det = (7)(104) - (26)(26) = 728 - 676 = 52

(XᵀX)⁻¹ = (1/52) · ⎡  104  -26 ⎤
                     ⎣  -26    7 ⎦
```

**Step 4: β̂ = (XᵀX)⁻¹ · XᵀY**

```
β̂ = (1/52) · ⎡ 104·28 + (-26)·112 ⎤
              ⎣ (-26)·28 + 7·112    ⎦

  = (1/52) · ⎡ 2912 - 2912 ⎤ = (1/52) · ⎡  0  ⎤ = ⎡ 0.0000  ⎤
             ⎣ -728 + 784  ⎦             ⎣  56 ⎦   ⎣ 1.0769  ⎦
```

### 4.4 Kode Python

```python
import numpy as np

X = np.array([2, 4, 5, 3, 3, 4, 5])
Y = np.array([2, 3, 5, 4, 3, 5, 6])

# Tambah kolom 1 untuk intercept
X_mat = np.column_stack([np.ones(len(X)), X])

# Formula β̂ = (XᵀX)⁻¹XᵀY
beta = np.linalg.inv(X_mat.T @ X_mat) @ X_mat.T @ Y

print("β₀ =", beta[0])  # intercept
print("β₁ =", beta[1])  # slope
```

**Hasil:**
| Parameter | Nilai |
|-----------|-------|
| β₀ (intercept) | **0.000000** |
| β₁ (slope) | **1.076923** |

---

## 5. Cara Alternatif: Rumus Manual (Perhitungan Langsung)

```python
n    = 7
x̄   = Σxᵢ/n  = 26/7  = 3.7143
ȳ   = ΣYᵢ/n  = 28/7  = 4.0000

Sxy = Σ(xᵢ - x̄)(Yᵢ - ȳ) = 8.0000
Sxx = Σ(xᵢ - x̄)²         = 7.4286

β₁  = Sxy / Sxx = 8.0000 / 7.4286 = 1.076923
β₀  = ȳ - β₁·x̄ = 4.0000 - 1.076923 × 3.7143 = 0.000000
```

---

## 6. Hasil Prediksi & Residual

| Point | X | Y (Aktual) | Ŷ (Prediksi) | e = Y - Ŷ |
|:-----:|:-:|:---------:|:------------:|:---------:|
| A     | 2 | 2         | 2.1538       | -0.1538   |
| B     | 4 | 3         | 4.3077       | -1.3077   |
| C     | 5 | 5         | 5.3846       | -0.3846   |
| D     | 3 | 4         | 3.2308       |  0.7692   |
| E     | 3 | 3         | 3.2308       | -0.2308   |
| F     | 4 | 5         | 4.3077       |  0.6923   |
| G     | 5 | 6         | 5.3846       |  0.6154   |

---

## 7. Evaluasi Model

| Metrik | Nilai | Interpretasi |
|--------|-------|--------------|
| SS_res (Sum of Squared Residuals) | 3.3846 | Total error kuadrat |
| SS_tot (Total Sum of Squares) | 12.0000 | Total variasi Y |
| **R²** | **0.7179** | 71.79% variasi Y dijelaskan model |
| **R (Korelasi)** | **0.8473** | Korelasi positif kuat |

### Interpretasi R²:
- **R² = 0.7179** → model menjelaskan **71.79%** variasi data
- **R = 0.8473** → korelasi X dan Y **kuat dan positif**
- Setiap kenaikan X sebesar 1 satuan → Y naik rata-rata **1.0769 satuan**

---

## 8. Persamaan Akhir

```
ŷ = 1.0769 · x
```

> Menarik: β₀ = 0, artinya garis regresi **melewati titik origin (0,0)**

---

## 9. Kode Lengkap

```python
import numpy as np
from sklearn.linear_model import LinearRegression

# ── Data ──────────────────────────────────────────────────
X_vals = np.array([2, 4, 5, 3, 3, 4, 5]).reshape(-1, 1)
Y_vals = np.array([2, 3, 5, 4, 3, 5, 6])
labels = ['A', 'B', 'C', 'D', 'E', 'F', 'G']

# ── Metode 1: sklearn ─────────────────────────────────────
model = LinearRegression()
model.fit(X_vals, Y_vals)
print("=== Metode 1: sklearn ===")
print(f"β₁ = {model.coef_[0]:.6f}")
print(f"β₀ = {model.intercept_:.6f}")
print(f"R² = {model.score(X_vals, Y_vals):.6f}")

# ── Metode 2: Analitik β̂ = (XᵀX)⁻¹XᵀY ─────────────────
X_mat = np.column_stack([np.ones(len(X_vals)), X_vals.flatten()])
beta  = np.linalg.inv(X_mat.T @ X_mat) @ X_mat.T @ Y_vals

print("\n=== Metode 2: Analitik ===")
print(f"β₀ = {beta[0]:.6f}")
print(f"β₁ = {beta[1]:.6f}")

# ── Tabel Prediksi & Residual ─────────────────────────────
print("\n=== Tabel Prediksi ===")
print(f"{'Point':<6}{'X':>4}{'Y':>5}{'Ŷ':>8}{'Residual':>12}")
for i, lbl in enumerate(labels):
    x  = X_vals.flatten()[i]
    y  = Y_vals[i]
    yp = beta[0] + beta[1] * x
    e  = y - yp
    print(f"{lbl:<6}{x:>4}{y:>5}{yp:>8.4f}{e:>12.4f}")

# ── Evaluasi ──────────────────────────────────────────────
y_pred = beta[0] + beta[1] * X_vals.flatten()
SS_res = np.sum((Y_vals - y_pred) ** 2)
SS_tot = np.sum((Y_vals - np.mean(Y_vals)) ** 2)
R2     = 1 - SS_res / SS_tot

print(f"\nR²  = {R2:.6f}")
print(f"R   = {np.sqrt(R2):.6f}")
print(f"Persamaan: ŷ = {beta[0]:.4f} + {beta[1]:.4f}·x")
```

---

*Analisis selesai — kedua metode menghasilkan koefisien yang identik, membuktikan konsistensi antara implementasi sklearn dan pendekatan analitik OLS.*
