# Business Data Analysis — SiCepat Express
**Capstone Project 3 | Yoseph CL**

---

## Deskripsi Proyek

Proyek ini merupakan analisis data bisnis berbasis Python untuk mengidentifikasi dua masalah utama yang dihadapi **SiCepat Express**: kebocoran pendapatan akibat manipulasi berat volumetrik paket oleh seller, serta praktik manipulasi data pickup (Phantom Pickup) oleh kurir First-Mile yang mengancam kepercayaan merchant.

---

## Struktur Notebook

| Bagian | Deskripsi |
|--------|-----------|
| **Section 1 — Business Understanding** | Latar belakang, problem statement, goals, dan kamus data |
| **Section 2 — Import Library & Load Data** | Memuat 3 dataset dan melakukan merge menjadi satu dataframe |
| **Section 3 — Exploratory Data Analysis (EDA)** | Inspeksi tipe data, missing values, duplikat, dan distribusi kategorik |
| **Section 4 — Data Wrangling & Cleansing** | Konversi datetime, flagging outlier berat, standardisasi item category |
| **Section 5 — Analisis** | Revenue leakage, SLA & Phantom Pickup, analisis HALU vs BEST |
| **Section 6 — Visualisasi Data** | 8 chart: bar, pie, histogram, boxplot, line plot, heatmap, scatter |
| **Section 7 — Hypothesis Testing** | Independent t-test dan Chi-Square test |
| **Section 8 — Kesimpulan & Rekomendasi** | Ringkasan temuan dan rekomendasi aksi bisnis |

---

## Problem Statement

**Masalah Utama:**
Tingginya kebocoran pendapatan akibat manipulasi berat volumetrik paket oleh seller, serta memburuknya kepercayaan merchant akibat praktik manipulasi data pickup (Phantom Pickup) oleh kurir First-Mile.

**Pertanyaan Analisis:**
1. Berapa estimasi total kerugian (dalam rupiah/kilogram) akibat selisih antara *Stated Weight* dan *Actual Volume Weight*?
2. Siapa saja **Top 10 Seller** yang paling sering merugikan perusahaan melalui manipulasi dimensi paket?
3. Pada jam berapa request pickup paling sering berujung pada **SLA Miss** (terlambat > 24 jam)?
4. Seberapa berantakan kolom `item_category` dan bagaimana cara menstandarisasinya?
5. Seberapa banyak pesanan yang `pickup_time`-nya tercatat **sebelum** `request_time` (Phantom Pickup)?
6. Apakah layanan promo **HALU** menyumbang lebih banyak anomali berat volumetrik dibanding **BEST**?

---

## Goals

- **Revenue Recovery** — Menemukan selisih total tonase yang tidak tertagih agar tim Finance dapat melakukan klaim (*chargeback*) ke pihak E-Commerce.
- **Data Cleansing** — Menghasilkan tabel data pengiriman yang bersih untuk keperluan Machine Learning prediksi kapasitas truk harian.

---

## Dataset

| Dataset | Jumlah Baris | Deskripsi |
|---------|-------------|-----------|
| `sicepat_sellers.csv` | — | Data dimensi seller (ID, nama toko, kota, tanggal bergabung) |
| `sicepat_services.csv` | — | Data dimensi layanan (kode & nama layanan: HALU, BEST, dll.) |
| `sicepat_pickups.csv` | ~300.000 | Data fakta pickup, berat paket, status, dan timestamp |

Dataset utama analisis (`df_sicepat`) merupakan hasil merge ketiga dataset tersebut, menghasilkan **300.000 baris** dan **13 kolom**.

---

## 🔧 Library yang Digunakan

```python
import pandas as pd          # Manipulasi dan analisis data
import numpy as np           # Komputasi numerik
import matplotlib.pyplot as plt  # Visualisasi dasar
import seaborn as sns        # Visualisasi statistik
from scipy import stats      # Uji hipotesis statistik
import math                  # Operasi matematika (floor, ceil)
import warnings              # Manajemen peringatan sistem
```

---

## Temuan Utama

### 1. Revenue Leakage
- Setelah **rekalibrasi & standarisasi berat**, ditemukan **leakage riil** sebesar ±416.161 kg tidak tertagih.
- Estimasi kerugian: **~Rp 2,91 Miliar** (asumsi tarif Rp 7.000/kg).
- Aturan standarisasi berat yang diterapkan:
  - Desimal ≤ 0,30 → bulatkan ke **bawah** (`math.floor`)
  - Desimal > 0,30 → bulatkan ke **atas** (`math.ceil`)

### 2. Top 10 Seller
- 10 seller teratas masing-masing terbukti memanipulasi >78 kg berat paket.

### 3. SLA Miss
- **11.687 kasus (4,37%)** pickup melewati batas SLA 24 jam.
- Kasus SLA Miss tersebar merata pada jam **06:00–19:00** → indikasi masalah kapasitas armada, bukan jam tertentu.

### 4. Item Category
- Ditemukan 11 nilai kotor yang tidak konsisten → distandarisasi menjadi **4 kategori bersih**:
  - Fashion & Pakaian
  - Kosmetik & Skincare
  - Elektronik & Gadget
  - Lainnya / Tidak Diketahui

### 5. Phantom Pickup
- **10.199 kasus (3,40%)** terdeteksi sebagai Phantom Pickup (`pickup_time < request_time`).

### 6. HALU vs BEST
- Hasil **Independent t-test** menunjukkan tidak ada perbedaan signifikan anomali berat antara layanan HALU dan BEST (~99% keduanya mengalami anomali).
- Hasil **Chi-Square test** menunjukkan ada asosiasi signifikan antara jenis layanan dan SLA status.

---

## Rekomendasi

| Rekomendasi | Deskripsi |
|-------------|-----------|
| **Auto-Chargeback System** | Tagih otomatis ke marketplace berdasarkan selisih berat scan vs stated |
| **Phantom Pickup Alert** | Validasi backend: `pickup_time` tidak boleh lebih awal dari `request_time` |
| **Server-side Timestamp** | Ganti client-side timestamp untuk mencegah manipulasi data |
| **Dropdown Item Category** | Ganti input bebas dengan dropdown terstruktur di aplikasi seller |
| **Evaluasi Kapasitas Armada** | Tambah jumlah kurir SiGESIT untuk mengurangi SLA Miss |

---

## Cara Menjalankan

1. Pastikan Python 3 dan semua library sudah terinstal.
2. Letakkan file berikut dalam direktori yang sama:
   - `sicepat_sellers.csv`
   - `sicepat_services.csv`
   - `sicepat_pickups.csv`
3. Buka dan jalankan notebook:
   ```bash
   jupyter notebook Capstone_Project_3_SiCepat_Express_YosephCL.ipynb
   ```
4. Jalankan cell secara berurutan dari Section 1 hingga Section 8.

---

## Author

**Yoseph CL**
Capstone Project 3 — Business Data Analysis
