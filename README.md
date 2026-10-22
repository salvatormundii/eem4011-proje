# Az Kanallı EEG’de Yanlış Nöbet Alarmlarının Azaltılması

EEM4011 Biyomedikal Programlama projesi. İki kanallı kulak arkası EEG’den üretilen basit nöbet alarmlarına ivmeölçer tabanlı hareket kapısı uygulanır; yanlış alarm hızı ve motor nöbet kaybı karşılaştırılır. Proje ayrıntıları `proje.md` dosyasındadır.

## Veri seti

**SeizeIT2 — Wearable Dataset of Patients With Focal Epilepsy**  
İndirme: [OpenNeuro ds005873](https://openneuro.org/datasets/ds005873)

Çalışma, 5 geliştirme ve 5 test kişisinin birer kaydıyla sınırlıdır. Veri dosyaları depoya eklenmemiştir; `.gitignore` içindeki `veri/` satırı veri klasörünü hariç tutar.

## Çalıştırma

1. Projenin notebook dosyasını Google Colab’da aç.
2. İlk hazırlık hücresini çalıştır: MNE kurulumu yapılır; seçilen 10 kaydın yalnız EEG, hareket ve olay dosyaları `/content/veri` klasörüne indirilip kontrol edilir.
3. Ardından şekil, baseline ve hareket kapısı hücrelerini sırayla çalıştır. Baseline eşiği ve hareket eşiği yalnız geliştirme grubunda belirlenir; testte sabit tutulur.
4. `sekil1_ll_acc_sub073.png` ve `tablo1_baseline_kapi.csv` çıktıları `/content` klasörüne kaydedilir.

## Ana sonuç

Beş test kaydında hareket kapısı, yanlış alarm hızını **9,9166’dan 6,6110 FA/saat’e (%33,33)** düşürdü. Yakalanan nöbet sayısı **7/11’den 4/11’e**, motor nöbet sayısı **4/5’ten 2/5’e**, motor dışı nöbet sayısı **3/6’dan 2/6’ya** düştü. Bu ilk kapı kuralı yanlış alarmları azaltırken nöbet duyarlılığını koruyamadı.

Sonuçlar, EEG ve hareket pencerelerinin sıra numarasıyla eşleştirilmesi varsayımına dayanır; zaman eşleşmesi ayrıca doğrulanmamıştır.
# eem4011-proje
