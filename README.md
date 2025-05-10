Bu çalışmada, fizyolojik veriler ve aktivite özellikleri kullanarak yakılan kaloriyi tahmin eden bir model geliştirdik. Hedefimiz, Kaggle’daki “Calories Burned” yarışmasında mümkün olan en düşük RMSE değerini elde etmekti. Nihai olarak 0.05748 skoruna ulaştık ve 0.056 civarına inmek için çeşitli ileri teknikleri test ettik.

1. Temel Varsayımlar

Sürekli değişkenlerde lineer ilişki varsayımı

Heteroskedastisite ve normal dağılım kontrolleri

-Keşifçi Analiz

Korelasyon matrisi: Yüksek korelasyonlu (multicollinearity) değişken çiftleri tespit edildi

VIF analizi: Bazı değişkenlerin VIF > 10 değerleri nedeniyle model kararlılığı riske girdi

------------------------------------------------------------

3. Özellik Mühendisliği
Temel özellikler: Age, Duration, Heart_Rate, Body_Temp, Height, Weight

Yeni türev özellikler:

BMI = Weight/(Height/100)²

Dur_HR = Duration × Heart_Rate

Polinom terimler: Age², Age³, Duration², Heart_Rate², Dur_HR²

Etkileşimler: Dur_HR × BMI, Heart_Rate/Duration

Binning & Kategorizasyon:

Duration → 4 çeyreklik “Dur_bin”

Heart_Rate → “HR_zone” (low/med/high)

Aykırı değer tespiti: Cook’s Distance ve IQR ile uç gözlemler kırpıldı

--------------------------------------------------------------------

5. Model Geliştirme 


-Base Modeller:
XGBoost

CatBoost

LightGBM


-Multicollinearity Çözümü:

VIF > 10 değerli değişkenler elendi

PCA alternatifi düşünüldü ancak feature eliminasyonunda karar kıldık

-------------------------------------------------------

6. Ensemble Stratejileri

-K‐Fold Stacking (Stage-1)
K = 10 ile veriyi parçalayıp her fold’da base modelleri eğittik
OOF tahminleri ve test seti tahminleri toplanarak meta-özellik matrisi oluşturuldu

-Meta-Model ile Öğrenme (Stage-2)
Meta-özellikler (xgb, cb, lgb) → XGBoost regresyon modeli
5‐fold CV ile en iyi iterasyon sayısı belirlendi

-Ağırlıklı Ensemble
OOF sonuçları kullanılarak Nelder–Mead optimizasyonu ile en iyi ağırlıklar bulundu
Test tahminleri bu ağırlıklarla birleştirildi

-Sonuç
Bu çok aşamalı pipeline ile 0.05748 RMSE elde ettik

---------------------------------------------------------

7. Derin Öğrenme Denemesi (MLP)

R-Keras ve Python-TensorFlow ile MLP

5-fold CV’de ~0.065 RMSE

Tek başına ağaç tabanlı ensemble’a yetişemedi;



