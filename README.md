# Pratech Case Study — Time Series Forecasting

**Aday / Candidate:** Aizharkyn Zhumabaeva

---

## 🇹🇷 Türkçe

### Proje Hakkında

Bu çalışma, Pratech şirketi için hazırlanan vaka çalışmasıdır. Görev, geçmiş satış verilerini kullanarak gelecekteki talebi tahmin etmek ve stok yönetimi için kullanılabilir bir model üretmektir.

### Veri Kaynağı ve Gizlilik Notu

Modelde gerçek bir Trendyol satıcısının halı satış verileri kullanılmıştır. Bu veriler şu kişisel bilgileri içerdiği için **KVKK gereği bu repoya yüklenmemiştir**:

- Müşteri ad-soyadı
- Teslimat ve fatura adresleri
- Telefon numaraları
- E-posta adresleri
- Vergi kimlik numaraları

Bu yüzden:
- Notebook'taki tüm hücre çıktıları temizlenmiştir
- Sonuç grafikleri `results/` klasöründe ekran görüntüsü olarak paylaşılmıştır
- `.gitignore` dosyası ile xlsx uzantılı veri dosyaları repoya alınmaz

### Kullanılan Yöntem

- **Model:** Prophet (Meta/Facebook)
- **Tahmin periyodu:** 4 hafta
- **Eğitim/Test ayrımı:** %80 / %20
- **Metrikler:** MAE, RMSE
- **Naif tahmin ile karşılaştırma:** Modelin gerçekten değer kattığını doğrulamak için

### Önemli Karar: TL Bazında Tahmin

Haftalık satış adedi 0-3 arası küçük sayılardan oluştuğu için Prophet anlamlı bir trend yakalayamıyordu. Bu nedenle tahmin **ciro (TL) bazında** yapıldı, sonra ortalama halı fiyatına bölünerek tekrar adete çevrildi. Bu yaklaşım, küçük örneklemli düzensiz satış verileri için pratik bir çözümdür.

### Notebook İçeriği

1. Veri yükleme ve hazırlama
2. Keşifsel veri analizi (EDA) — trend, dağılım, eksik veri
3. Eğitim/test ayrımı
4. Prophet modeli ve hiperparametre seçimi
5. Performans değerlendirmesi (MAE, RMSE)
6. Sonuç grafiği — gerçek ciro ve 4 haftalık tahmin
7. Sonuç ve yorum

### Çalıştırmak İçin

```
pip install pandas numpy matplotlib prophet scikit-learn openpyxl
jupyter notebook hali_satis_tahmini.ipynb
```

Not: Notebook'u çalıştırmak için Trendyol satıcı panelinden indirilmiş aylık xlsx dosyalarına ihtiyaç vardır. Dosya yapısı için notebook'taki "Veri Yükleme" bölümüne bakınız.

---

## 🇬🇧 English

### About the Project

This is a case study prepared for Pratech. The task is to forecast future demand based on historical sales data and produce a model usable for inventory planning.

### Data Source and Privacy Note

The model uses real sales data from a Trendyol seller (carpet store). Because this data contains the following personal information, **it has not been uploaded to this repository in accordance with KVKK / GDPR**:

- Customer names
- Delivery and billing addresses
- Phone numbers
- Email addresses
- Tax ID numbers

For this reason:
- All notebook output cells have been cleared
- Result plots are shared as screenshots under the `results/` folder
- A `.gitignore` file prevents xlsx data files from being committed

### Methodology

- **Model:** Prophet (Meta/Facebook)
- **Forecast horizon:** 4 weeks
- **Train/test split:** 80% / 20%
- **Metrics:** MAE, RMSE
- **Naive baseline comparison:** to verify that the model actually adds value

### Key Decision: Forecasting Revenue Instead of Units

Weekly unit sales fluctuate between 0-3, which is too sparse for Prophet to detect a meaningful trend. Therefore the forecast is built on **revenue (TL)** and then divided by the average carpet price to convert back to units. This is a practical workaround for small, irregular sales data.

### Notebook Structure

1. Data loading and preparation
2. Exploratory Data Analysis (EDA) — trend, distribution, missing values
3. Train/test split
4. Prophet model and hyperparameter tuning
5. Performance evaluation (MAE, RMSE)
6. Final plot — actual revenue and 4-week forecast
7. Conclusion and interpretation

### How to Run

```
pip install pandas numpy matplotlib prophet scikit-learn openpyxl
jupyter notebook hali_satis_tahmini.ipynb
```

Note: To execute the notebook, monthly xlsx export files from the Trendyol seller panel are required. Refer to the "Data Loading" section in the notebook for the expected file structure.

---

## Files in This Repository / Repodaki Dosyalar

| File | Description |
|------|-------------|
| `hali_satis_tahmini.ipynb` | Main notebook with all analysis / Tüm analizleri içeren ana notebook |
| `results/final_forecast.png` | Final forecast plot / Sonuç tahmin grafiği |
| `results/eda_charts.png` | EDA visualizations / Keşifsel analiz grafikleri |
| `README.md` | This file / Bu dosya |
| `.gitignore` | Excludes data files from version control / Veri dosyalarını sürüm kontrolünden hariç tutar |
