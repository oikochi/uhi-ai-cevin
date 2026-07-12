# 🌡️ GeoAI Decision Support System — Urban Heat Island Kota Surabaya

> **Tugas Akhir** · Cevin Abinaya Malewa (NPT 21.24.0005) · Klimatologi STMKG · 2026

[![GitHub Pages](https://img.shields.io/badge/🌐_Live_Dashboard-oikochi.github.io%2Fuhi--ai--cevin-0694A2?style=for-the-badge)](https://oikochi.github.io/uhi-ai-cevin/)
[![License: MIT](https://img.shields.io/badge/License-MIT-10B981?style=for-the-badge)](LICENSE)
[![Data: BIG RBI](https://img.shields.io/badge/Batas_Admin-BIG_RBI_1:10.000-1A56DB?style=for-the-badge)]()

---

## 🖥️ Live Dashboard

**[→ Buka GeoAI DSS Platform](https://oikochi.github.io/uhi-ai-cevin/)**

Visualisasi interaktif 154 kelurahan Surabaya: peta zona UHI, analisis per kelurahan, proyeksi LSTM, simulasi mitigasi, dan Spatial AI Assistant.

---

## 🏗️ Framework Sistem

```mermaid
flowchart TD
    %% ── DATA INPUT ──
    subgraph INPUT ["📥 DATA INPUT"]
        direction LR
        D1["🛰️ Landsat 8/9 C2 L2\nLST · NDVI · NDBI · NDWI\nGEE · 30m · 2022–2024"]
        D2["🌍 Dynamic World V1\nFraksi Terbangun\nGEE · 10m"]
        D3["🌡️ BMKG Tanjung Perak\nSuhu Udara Bulanan\n2009–2023 · ~5.844 obs."]
        D4["🗺️ BIG RBI 1:10.000\n154 Kelurahan · 31 Kecamatan\nWGS 1984 / UTM 49S"]
        D5["👥 BPS Surabaya\nKepadatan Penduduk 2023\nLevel Kecamatan"]
    end

    %% ── MODUL 1 ──
    subgraph M1 ["① SPATIAL ANALYSIS"]
        direction LR
        M1A["Cloud Masking\n& Komposit Median"] --> M1B["Ekstraksi Parameter\nBiofisik"] --> M1C["Zonal Statistics\nper Kelurahan"]
    end
    M1OUT[/"📦 Master Dataset\n154 kelurahan × 8 variabel"/]

    %% ── MODUL 2 ──
    subgraph M2 ["② UHI ZONING · K-Means"]
        M2A["Normalisasi Z-Score\n5 Variabel Fitur"] --> M2B["Elbow Method +\nSilhouette Score\nk = 4 optimal"] --> M2C["K-Means++\nClustering"]
    end
    M2OUT[/"🗺️ 4 Zona Intensitas UHI\nRendah · Sedang · Tinggi · Sangat Tinggi\nSilhouette = 0.374"/]

    %% ── MODUL 3 ──
    subgraph M3 ["③ AI INTERPRETATION · Random Forest"]
        M3A["Korelasi Pearson\nNDBI r=+0.906\nNDVI r=−0.575"] --> M3B["Random Forest\n500 Trees · 5-Fold CV\nAkurasi 87.9% internal"]
    end
    M3OUT[/"⚡ Feature Importance\nNDBI 22.5% → Kepadatan 18.0%\n→ Fraksi Terbangun 12.1%"/]

    %% ── MODUL 4 ──
    subgraph M4 ["④ FUTURE PROJECTION · LSTM"]
        M4A["Min-Max Scaling\nSliding Window 12 bln"] --> M4B["LSTM\n128–64 Neuron · Adam\nMAE 0.61°C · R²=0.233"]
    end
    M4OUT[/"📈 Proyeksi Suhu 2025–2029\nRata-rata 28.64°C · +0.16°C\nInterval ±RMSE"/]

    %% ── MODUL 5 ──
    subgraph M5 ["⑤ MITIGATION SIMULATION"]
        M5A["Gradien Korelasi Pearson\nΔLST = f(ΔNDVI, ΔFraksi)"] --> M5B["Interactive Slider\nReal-time LST Estimation"]
    end
    M5OUT[/"🧪 Estimasi Dampak Intervensi\nΔNDVI +0.1 → LST turun ~1.2°C\nIdentifikasi Kelurahan Prioritas"/]

    %% ── PLATFORM ──
    subgraph PLATFORM ["🖥️ GeoAI WebGIS Decision Support Platform"]
        direction LR
        P1["🗺️ Peta Zonasi\nInteraktif"] --- P2["📊 Analisis\nPer Kelurahan"] --- P3["🤖 Spatial AI\nAssistant"] --- P4["📈 Proyeksi &\nConfidence Band"] --- P5["🧪 Simulasi\nMitigasi"] --- P6["🔍 Filter Spasial\n31 Kecamatan"]
    end

    %% ── ALUR ──
    D1 & D2 & D4 --> M1
    D5 --> M3
    D3 --> M4
    M1 --> M1OUT --> M2 & M3
    M2 --> M2OUT --> M3
    M2OUT -.->|label K-Means| M3
    M3 --> M3OUT
    M3OUT -.->|gradien korelasi| M5
    M4 --> M4OUT
    M5 --> M5OUT
    M2OUT & M3OUT & M4OUT & M5OUT --> PLATFORM

    %% ── STYLE ──
    style INPUT fill:#EFF6FF,stroke:#BFDBFE,color:#1E40AF
    style M1 fill:#F0FDF4,stroke:#BBF7D0,color:#065F46
    style M2 fill:#ECFDF5,stroke:#A7F3D0,color:#065F46
    style M3 fill:#FFFBEB,stroke:#FDE68A,color:#92400E
    style M4 fill:#FEF2F2,stroke:#FECACA,color:#991B1B
    style M5 fill:#F5F3FF,stroke:#DDD6FE,color:#4C1D95
    style PLATFORM fill:#0694A2,stroke:#0E7490,color:#FFFFFF
    style M1OUT fill:#F0FDF4,stroke:#10B981,color:#065F46
    style M2OUT fill:#ECFDF5,stroke:#10B981,color:#065F46
    style M3OUT fill:#FFFBEB,stroke:#F59E0B,color:#92400E
    style M4OUT fill:#FEF2F2,stroke:#EF4444,color:#991B1B
    style M5OUT fill:#F5F3FF,stroke:#8B5CF6,color:#4C1D95
```

---

## 📊 Hasil Penelitian

| Metode | Metrik | Hasil |
|--------|--------|-------|
| **K-Means** | Silhouette Score | 0,374 (marginal → gradien termal) |
| **K-Means** | Zona terbentuk | 4 zona (Rendah · Sedang · Tinggi · Sangat Tinggi) |
| **Random Forest** | Akurasi (internal) | 87,9% · F1 = 0,879 |
| **RF Feature #1** | NDBI | Importance 0,225 · Korelasi r = +0,906 |
| **RF Feature #2** | Kepadatan | Importance 0,180 |
| **RF Feature #3** | Fraksi Terbangun | Importance 0,121 · Korelasi r = +0,848 |
| **LSTM** | MAE | 0,61 °C |
| **LSTM** | RMSE | 0,80 °C |
| **LSTM** | R² | 0,233 (proyeksi tren) |
| **LSTM** | Proyeksi 2025–2029 | +0,16 °C kenaikan suhu udara |

### Distribusi Zona UHI
```
🔴 Sangat Tinggi (Inti UHI)  ████████████░░░░░░░░░░░░░  45 kelurahan · LST 42,54°C · kepadatan 23.236/km²
🟠 Tinggi                    ████████████████████████░  71 kelurahan · LST 42,25°C · terbangun 96%
🟢 Sedang                    ██████████░░░░░░░░░░░░░░░  34 kelurahan · LST 40,20°C · transisi
🔵 Rendah                    ████░░░░░░░░░░░░░░░░░░░░░  12 kelurahan · LST 36,99°C · vegetasi baik
```

---

## 🗂️ Struktur Repository

```
uhi-ai-cevin/
├── index.html                    # GeoAI DSS Platform (web utama)
├── data/
│   └── surabaya_uhi.geojson     # 154 kelurahan + data analisis
├── hasil_kmeans_zonasi.csv       # Output K-Means per kelurahan
├── hasil_random_forest.csv       # Output Random Forest
├── hasil_prediksi_lstm.csv       # Proyeksi LSTM 2025–2029
├── data_suhu_bulanan.csv         # Historis BMKG 2009–2023
├── kmeans.py                     # Script K-Means clustering
├── randomforest.py               # Script Random Forest
├── lstm.py                       # Script LSTM time series
├── peta_uhi_surabaya.py          # Script kartografi (4 peta)
├── uji_statistik_zona.py         # ANOVA + post-hoc analysis
└── README.md
```

---

## ⚙️ Cara Menjalankan

```bash
# 1. Install dependencies
pip install geopandas matplotlib mapclassify pandas scikit-learn tensorflow scipy

# 2. Analisis K-Means
python kmeans.py

# 3. Random Forest
python randomforest.py

# 4. LSTM Proyeksi
python lstm.py

# 5. Peta (butuh Surabaya_Kelurahan.geojson)
python peta_uhi_surabaya.py

# 6. Uji statistik ANOVA
python uji_statistik_zona.py

# 7. Web (lokal)
python -m http.server 8000
# buka http://localhost:8000
```

---

## 📚 Data & Sumber

| Data | Sumber | Resolusi / Periode |
|------|--------|--------------------|
| LST, NDVI, NDBI, NDWI | Landsat 8/9 C2 L2 (Google Earth Engine) | 30 m · 2022–2024 |
| Fraksi Terbangun | Dynamic World V1 (Google Earth Engine) | 10 m · 2022–2024 |
| Suhu Udara Bulanan | BMKG Sta. Maritim Tanjung Perak | Harian · 2009–2023 |
| Batas Administrasi | **BIG — RBI 1:10.000** | Vektor · WGS 1984 |
| Kepadatan Penduduk | BPS Kota Surabaya | Level kecamatan · 2023 |

> ⚠️ **Catatan transparansi:** Akurasi Random Forest (87,9%) merupakan konsistensi internal terhadap label K-Means — bukan validasi independen. R² LSTM (0,233) rendah — proyeksi diposisikan sebagai arah tren, bukan prediksi presisi. Kepadatan penduduk tersedia level kecamatan, diterapkan pada analisis kelurahan.

---

## 📖 Sitasi

```bibtex
@thesis{malewa2026uhi,
  author  = {Malewa, Cevin Abinaya},
  title   = {Zonasi, Faktor Dominan, dan Proyeksi Tren Suhu Urban Heat Island Kota Surabaya},
  school  = {Sekolah Tinggi Meteorologi Klimatologi dan Geofisika (STMKG)},
  year    = {2026},
  url     = {https://oikochi.github.io/uhi-ai-cevin/}
}
```

---

<div align="center">
  <sub>© 2026 Cevin Abinaya Malewa · Klimatologi STMKG · Data BIG RBI 1:10.000</sub>
</div>
