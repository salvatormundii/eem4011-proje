# Kulak Arkası EEG’de Hareket Kapısı ve Zamanlama Kuralı

Bu çalışma, line length tabanlı bir EEG nöbet dedektörüne eklenen ivmeölçer hareket kapısının yanlış alarm hızını ne kadar azalttığını ve bunun motor nöbet kaybıyla birlikte gerçekleşip gerçekleşmediğini inceler. Proje sahibi: Gülbahar Zeynep.

Depo: https://github.com/salvatormundii/eem4011-proje

## Veri ve kapsam

- Veri: [SeizeIT2, OpenNeuro ds005873](https://openneuro.org/datasets/ds005873).
- On kişinin birer kaydı: kişi ayrık beş geliştirme ve beş test kişisi.
- Geliştirme: 035-04, 047-01, 092-50, 109-08, 011-05.
- Test: 073-41, 115-52, 089-61, 101-02, 079-15.
- Test grubunda 11 nöbet: 5 motor ve 6 motor dışı.
- Aynı test kişilerinin önceki sonuçları görülmüştür; Aşama 3 sonuçları pilot keşif analizi olarak sunulur.

## Aşama 3 ana sonuçları

Aşağıdaki sayılar `tablo2_baseline_kapi_zamanlama_kesif.csv` dosyasının TOPLAM_TEST satırlarından alınmıştır. Ondalık ayırıcı virgüldür. FA/h, yüzde veya accuracy değil, saat başına yanlış alarm olayıdır.

| Yöntem | FA/h | Yakalanan /11 | Motor /5 | Motor dışı /6 |
|---|---:|---:|---:|---:|
| Kapısız LL baseline | 9,9166 | 7/11 | 4/5 | 3/6 |
| 99. yüzdelikli hareket kapısı | 9,1980 | 5/11 | 2/5 | 3/6 |
| 99. yüzdelikli kapı + zamanlama | 9,1980 | 6/11 | 3/5 | 3/6 |

Nihai kapı + zamanlama sonucu 9,1980 FA/h, 6/11 toplam ve 3/5 motor yakalamadır. Baseline'a göre yanlış alarm hızı azalırken toplam ve motor nöbet yakalama da azalmıştır. Zamanlama, kapının kaybettirdiği motor nöbetlerden birini geri korumuş, kaybı tamamen giderememiştir. sub-101 testten çıkarılmamış; bu kayıtta kapı hiçbir alarmı silmemiştir.

## Yöntem

- İlk EEG kanalı: 256 Hz, mikrovolt dönüşümü, dördüncü dereceden 1–25 Hz Butterworth tasarımı ve `filtfilt`.
- Örtüşmeyen iki saniyelik pencerelerde line length: ardışık örnek farklarının mutlak değerleri toplamı. Kayıt LL medyanına bölünerek LLn elde edilir.
- Hareket: `EEG SD ACC X`, `EEG SD ACC Y`, `EEG SD ACC Z` büyüklüğünün iki saniyelik pencere içi standart sapması. Kayıt medyanına bölünerek ACCn elde edilir.
- `k=3`; Aşama 3 çalışma noktası geliştirmedeki ACCn değerlerinin 99. yüzdeliğidir: `T_acc=12,25181338`. Eşikler geliştirme kişilerinden seçilmiş, testte sabit tutulmuştur.
- `LLn > k` olan pencereler arasındaki boşluk en fazla 30 saniyeyse tek alarm olayında birleştirilir.
- Bir alarmın yalnız pozitif EEG pencerelerinin en az yarısında `ACCn > T_acc` ise alarm silinir. Aradaki negatif EEG pencereleri oranın paydasına alınmaz.
- Zamanlama yalnız kapının sildiği alarmlarda uygulanır. İlk pozitif EEG penceresinin başlangıcı t0 kabul edilir; t0'dan 10 saniye öncesi ile alarm bitişi arasında ilk yüksek hareket aranır. Kayıt başlangıcından önceye geçilmez. İlk yüksek hareket t0'dan en az dört saniye sonra başlıyorsa veya bu aralıkta yüksek hareket yoksa alarm korunur.
- Nöbet başlangıcından 30 saniye önce ile bitişinden 60 saniye sonraki aralığa temas eden alarm o nöbeti yakalamış sayılır. Bir nöbet birden fazla alarm eşleşse de bir kez sayılır. Bir nöbete değen farklı alarmlar ayrı doğru alarm olabilir; bire bir eşleştirme yapılmaz.
- FA/h = toplam yanlış alarm olayı / nöbet süreleri dahil toplam kayıt saati. Toplam test sonucu kişi FA/h değerlerinin ortalaması değildir.

## Notebook ve sonuç dosyaları

- [`EEG.ipynb`](EEG.ipynb): Aşama 2, Aşama 3, zamanlama ve jüri şekillerini içeren güncel yerel notebook'un kopyası.
- [`tablo2_baseline_kapi_zamanlama_kesif.csv`](tablo2_baseline_kapi_zamanlama_kesif.csv): Aşama 3 ana sonuçları ve kişi bazlı sayımlar.
- [`tablo1_baseline_kapi.csv`](tablo1_baseline_kapi.csv): Önceki Aşama 2, 90. yüzdelikli kapı deneyi. Bu dosyanın kapı sayıları Aşama 3 nihai sonucu yerine kullanılmaz.
- [`.gitignore`](.gitignore): Ham veri klasörünü ve EDF dosyalarını hariç tutar.

## Şekiller

1. [`sekil1_ll_acc_sub073.png`](sekil1_ll_acc_sub073.png): sub-073 motor nöbet çevresinde EEG, LLn, ACCn ve sabit eşikler.
2. [`sekil2_pilot_kapisiz_kapili_ll.png`](sekil2_pilot_kapisiz_kapili_ll.png): Baseline ile 99. yüzdelikli kapı karşılaştırması. Zamanlama sonucu bu şekle dahil değildir; kapı motor yakalaması 2/5'tir.
3. [`sekil3_kapinin_sildigi_motor_alarm_sub-115_run-52.png`](sekil3_kapinin_sildigi_motor_alarm_sub-115_run-52.png): Kapının sildiği ve zamanlamanın geri koruduğu motor alarm örneği.

## Çalıştırma

Notebook'u Colab'da açıp hücreleri sırayla çalıştır. Hazırlık akışı yalnız seçilen on kaydın EEG, hareket ve olay dosyalarını `/content/veri` altında kullanır; bütün veri seti gerekmez. Ham veriler bu depoya eklenmez.

Notebook önceki denemeleri ve Aşama 2 çıktılarını da içerir. Son doğrulanmış Aşama 2 kontrol akışı süre veya pencere uyuşmazlığında durur; ardından Aşama 3 ve zamanlama karşılaştırması gelir. Ana sonuç için `tablo2_baseline_kapi_zamanlama_kesif.csv` ve yukarıdaki Aşama 3 tablosu esas alınır. Önceki bir hücrenin çıktısı nihai sonuç olarak sunulmaz.

## Sınırlılıklar

- Süre ve iki saniyelik pencere sayıları on kayıtta eşittir. Bu kontrol, EEG ve hareketin ortak mutlak başlangıcını bağımsız olarak doğrulamaz. Sıra numarasıyla eşleştirme ve dört saniyelik zamanlama kuralı bu varsayımla yorumlanır.
- İleri-geri filtre ve tüm kayıt medyanıyla normalizasyon nedeniyle analiz çevrim dışıdır.
- k seçiminde kodun F1 adıyla kullandığı puanın kesinliği alarm sayısına, duyarlılığı nöbet sayısına dayanır; bire bir olay eşleştirmesi yapılmaz.
- Kayıt sayısı küçüktür ve aynı test kişileri yeniden incelenmiştir. Bağımsız doğrulama, klinik kullanım veya genellenebilir üstünlük iddiası yoktur; p değeri ve bootstrap eklenmemiştir.
- Veri sürümünün yerel dosyalarla eşleşme kanıtı, gerçek çalışma ortamı sürümleri ve kod lisansı henüz tamamlanmamış belgeleme konularıdır.

Bu README düzenlemesinde analiz yeniden çalıştırılmamış, sonuç sayıları ve eşikler değiştirilmemiştir.
