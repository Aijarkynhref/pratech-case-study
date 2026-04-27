# Pratech Case Study — Time Series Forecasting

**Aday / Candidate:** Aizharkyn Zhumabaeva

---

## Türkçe

### Proje Hikayesi

Bu çalışma, daha önce yönettiğim bir projenin verileri üzerinde gerçekleştirildi. Proje, Trendyol'da faaliyet gösteren küçük ölçekli bir halı satış mağazasıdır. Mağaza yeni kurulmuş ve henüz büyük bir müşteri tabanına ulaşmamış olduğundan, satış hacmi haftada 0-3 adet arasında değişen düzensiz bir profil sergiliyor.

Bu durum, görev için ilginç bir vaka oluşturdu: Klasik tahmin modelleri genelde uzun ve düzenli zaman serileri ile çalışmak üzere tasarlanmış. Az veri ve düzensiz satış paterniyle modelin gerçekten işe yaraması için bazı pratik kararlar almam gerekti. Bu durum aslında küçük işletmelerin gerçek hayatta karşılaştığı bir problem — hangi büyük e-ticaret satıcısının başlangıçta yeterli verisi var ki?

### Görev

Pratech'in vaka tanımına göre amaç, geçmiş satış verilerini kullanarak gelecekteki talebi tahmin etmek ve stok yönetimi için uygulanabilir öneriler üretmektir.

### Veri ve Gizlilik

Mağazanın Trendyol satıcı panelinden indirilen aylık xlsx dosyaları kullanılmıştır. Bu dosyalar şu kişisel müşteri verilerini içermektedir:

- Müşteri ad-soyadı
- Teslimat ve fatura adresleri
- Telefon numaraları
- E-posta adresleri
- Vergi kimlik numaraları

KVKK gereği bu veriler bu repoya yüklenmemiştir. Bunun yerine:

- Notebook'taki tüm hücre çıktıları temizlenmiştir
- Sonuç grafikleri `results/` klasöründe ekran görüntüsü olarak paylaşılmıştır
- `.gitignore` dosyası ile xlsx uzantılı veri dosyalarının yanlışlıkla yüklenmesi engellenmiştir

### Yöntem

Üç model arasında (ARIMA, Prophet, XGBoost) tercihi Prophet'tan yana kullandım. Sebebi: az veriyle çalışırken yorumlanabilirliği yüksek, hiperparametreleri sezgisel ve eksik haftaları otomatik işliyor.

**Önemli karar — TL bazında tahmin:** Haftalık satış adedi 0-3 arası küçük sayılardan oluşuyor ve haftaların çoğunda satış hiç yok. Prophet bu seviyede sayılarla çalışırken anlamlı trend yakalayamadı, sürekli düz çizgi tahmin verdi. Bu nedenle modeli **ciro (TL) bazında** çalıştırdım, sonra ortalama halı fiyatına bölerek tekrar adete çevirdim. Bu, küçük örneklemli düzensiz satış verileri için makul bir yaklaşım oldu.

**Hiperparametreler:**

- `changepoint_prior_scale=0.05` — trend değişim noktalarının esneklik seviyesi (varsayılan; az veri olduğu için artırmadım)
- `n_changepoints=5` — varsayılan 25 yerine düşürdüm, çünkü 24 haftalık veride 25 değişim noktası overfitting'e yol açıyor
- `seasonality_mode='additive'` — toplamsal mevsimsellik
- Yıllık/haftalık/günlük mevsimsellikler kapalı — 1 yıldan az veri olduğu için model yanlış pattern çıkarabilir
- `interval_width=0.80` — %80 güven aralığı

### Sonuçlar

```
Veri:
  Kaynak    : Trendyol satıcı paneli, gerçek halı satışları
  Dönem     : 2025-11-02 - 2026-04-12
  Hafta     : 24
  Toplam    : 20 adet halı satılmış
  Ortalama  : 7634 TL/adet, 6582 TL/hafta

Model: Prophet (TL bazında tahmin, sonra adete çevirme)

Test seti performansı (4 hafta):
  MAE  = 4805 TL/hafta  (~0.6 adet)
  RMSE = 5540 TL/hafta  (~0.7 adet)

Önümüzdeki 4 hafta için tahmin:
  Ortalama beklenen satış: ~0.5 adet/hafta
```

### Yorum ve İş Önerisi

Modelin haftada yaklaşık 0.6 adetlik MAE değerine ulaşması, mağazanın ortalama satış hacmi (haftada ~1 adet) düşünüldüğünde anlamlı bir sonuç. Tahmin %100 isabetli değil — zaten 24 haftalık ve düzensiz veriden bunu beklemek de gerçekçi olmaz.

Pratik öneriler:

- Haftalık 2-3 adet halı stokta tutulması, hem stoksuz kalma riskini azaltır hem de fazla stok maliyetini kontrol altında tutar
- Aralık-ocak döneminde belirgin bir satış tepesi gözlendi, bu kış sezonu etkisi olabilir; gelecek yıl bu dönem için stok artırılabilir
- Daha güvenilir tahmin için en az 1-2 yıllık veri biriktikten sonra modele yıllık mevsimsellik eklenmesi önerilir
- Satışları artırmak için tahmin modelinden bağımsız olarak reklam ve görünürlük çalışmaları gerekiyor — model mevcut satış paterni üzerinden çalışıyor, talep yaratamaz

### Notebook İçeriği

1. Kütüphaneler ve veri yükleme
2. Veri hazırlama (filtreleme, tarih parse, haftalık agregasyon)
3. Keşifsel veri analizi (EDA) — trend, dağılım, eksik veri analizi
4. Eğitim/test ayrımı (%80/%20)
5. Prophet modeli ve hiperparametre seçimi
6. Performans değerlendirmesi (MAE, RMSE, naif tahmin ile karşılaştırma)
7. Sonuç grafiği — gerçek ciro ve 4 haftalık tahmin
8. Sonuç ve yorum

### Çalıştırmak İçin

```
pip install pandas numpy matplotlib prophet scikit-learn openpyxl
jupyter notebook hali_satis_tahmini.ipynb
```

Not: Notebook'u çalıştırmak için Trendyol satıcı panelinden indirilmiş aylık xlsx dosyalarına ihtiyaç vardır. Beklenen dosya yapısı için notebook'taki "Veri Yükleme" bölümüne bakınız.

---

## English

### Project Background

This study was carried out using data from a project I previously managed — a small-scale carpet store operating on Trendyol. The store is relatively new and has not yet built a large customer base, so weekly sales fluctuate between 0 and 3 units in an irregular pattern.

This made for an interesting case: most forecasting models are designed for long, regular time series. Working with sparse, irregular data forced me to make some practical decisions to get the model to actually deliver useful results. Honestly, this is the situation most small businesses face in real life — which large e-commerce seller has enough data on day one?

### Task

According to Pratech's case description, the goal is to forecast future demand based on historical sales data and produce actionable recommendations for inventory management.

### Data and Privacy

Monthly xlsx files exported from the store's Trendyol seller panel were used. These files contain the following customer personal data:

- Customer names
- Delivery and billing addresses
- Phone numbers
- Email addresses
- Tax ID numbers

In compliance with KVKK / GDPR, this data has not been uploaded to this repository. Instead:

- All notebook output cells have been cleared
- Result plots are shared as screenshots under the `results/` folder
- A `.gitignore` file prevents xlsx data files from being accidentally committed

### Methodology

Among the three suggested models (ARIMA, Prophet, XGBoost), I chose Prophet. Reasoning: with limited data, it offers high interpretability, intuitive hyperparameters, and handles missing weeks automatically.

**Key decision — forecasting in TL instead of units:** Weekly unit sales range from 0 to 3, with most weeks having no sales at all. Prophet failed to capture a meaningful trend at this scale, consistently producing flat-line forecasts. Therefore, I built the forecast on **revenue (TL)** and divided by the average carpet price to convert back to units. This is a reasonable workaround for small, irregular sales data.

**Hyperparameters:**

- `changepoint_prior_scale=0.05` — trend changepoint flexibility (default; not increased given limited data)
- `n_changepoints=5` — reduced from default 25, since 25 changepoints over 24 weeks would lead to overfitting
- `seasonality_mode='additive'` — additive seasonality
- Yearly/weekly/daily seasonalities disabled — with less than a year of data, the model could pick up false patterns
- `interval_width=0.80` — 80% confidence interval

### Results

```
Data:
  Source    : Trendyol seller panel, real carpet sales
  Period    : 2025-11-02 - 2026-04-12
  Weeks     : 24
  Total     : 20 carpets sold
  Average   : 7634 TL/unit, 6582 TL/week

Model: Prophet (forecast in TL, then converted to units)

Test set performance (4 weeks):
  MAE  = 4805 TL/week  (~0.6 units)
  RMSE = 5540 TL/week  (~0.7 units)

Forecast for the next 4 weeks:
  Average expected sales: ~0.5 units/week
```

### Interpretation and Business Recommendation

Achieving a MAE of about 0.6 units per week is a meaningful result given the store's average sales volume (~1 unit/week). The forecast is not 100% accurate — but expecting that from 24 weeks of irregular data would be unrealistic.

Practical recommendations:

- Keeping a weekly stock of 2-3 carpets reduces stockout risk while keeping inventory costs under control
- A noticeable sales peak was observed in December-January, likely a winter season effect; stock could be increased for the same period next year
- For more reliable forecasts, the model could include yearly seasonality once 1-2 years of data are available
- To grow sales, advertising and visibility efforts are needed independently of the forecast model — the model works on the existing sales pattern, it cannot create demand

### Notebook Structure

1. Libraries and data loading
2. Data preparation (filtering, date parsing, weekly aggregation)
3. Exploratory Data Analysis (EDA) — trend, distribution, missing values
4. Train/test split (80%/20%)
5. Prophet model and hyperparameter selection
6. Performance evaluation (MAE, RMSE, comparison with naive forecast)
7. Final plot — actual revenue and 4-week forecast
8. Conclusion and interpretation

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
