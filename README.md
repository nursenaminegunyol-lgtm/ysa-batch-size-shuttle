# Mini-Batch Büyüklüğünün Genelleme ve Hesaplama Verimliliğine Etkisi: UCI Shuttle Veri Seti

Bu depo, *Applied Neural Networks: Theory, Algorithms, and Engineering Cases* (Springer, Emerging Trends in Mechatronics) kitabı için hazırlanan bölümün kodlarını, verisini ve çıktılarını içerir. Çalışmada, çok katmanlı bir algılayıcı (MLP) UCI Statlog (Shuttle) veri setinde yedi farklı mini-batch büyüklüğüyle (16–1024) eğitilmiş; batch büyüklüğünün genelleme performansına (doğruluk, macro F1) ve hesaplama verimliliğine (epoch süresi) etkisi incelenmiştir.

**Yazar:** Nursena Mine Günyol — Manisa Celal Bayar Üniversitesi, Konstrüksiyon ve İmalat Yüksek Lisans Programı

## Veri seti

UCI Statlog (Shuttle): 58.000 örnek (43.500 eğitim, 14.500 test), 9 sayısal öznitelik, 7 sınıf. Veri dosyaları `data/` klasöründedir.

> D. Dua ve C. Graff, "UCI Machine Learning Repository," 2019. [Çevrimiçi]. Erişim: https://archive.ics.uci.edu/ml

## Klasör yapısı

```
├── odev.ipynb          # Tüm kodlar ve çıktılar (Jupyter Notebook)
├── requirements.txt    # Python kütüphaneleri ve sürümleri
├── data/               # shuttle.trn (eğitim), shuttle.tst (test)
├── sekiller/           # Grafikler (300 DPI .png): G1–G7, G6b
└── tablolar/           # Tablolar (.csv, .xlsx): T1–T7, T10, epoch geçmişleri, çoklu seed sonuçları
```

## Kurulum ve çalıştırma

Python 3.11 ile test edilmiştir.

```bash
git clone https://github.com/nursenaminegunyol-lgtm/ysa-batch-size-shuttle.git
cd ysa-batch-size-shuttle
python -m venv venv
venv\Scripts\activate          # macOS/Linux: source venv/bin/activate
pip install -r requirements.txt
jupyter notebook odev.ipynb
```

Notebook hücreleri yukarıdan aşağıya sırayla çalıştırılmalıdır. Batch büyüklüğü deneyi CPU'da yaklaşık 15 dakika, ablasyon çalışması yaklaşık 8 dakika, 5 seed'li tekrar deneyi yaklaşık 40 dakika sürer.

## Deney düzeni

| Parametre | Değer |
| --- | --- |
| Model | MLP: 9 → 64 → 32 → 7, ReLU, Dropout 0,2 (2.951 parametre) |
| Kayıp / optimizasyon | Çapraz entropi / Adam, öğrenme oranı 0,001 |
| Epoch | 50 |
| Batch büyüklükleri | 16, 32, 64, 128, 256, 512, 1024 |
| Ön işleme | Z-skor standartlaştırma (yalnızca eğitim setiyle) |
| Bölme | %80 eğitim / %20 doğrulama (tabakalı), orijinal UCI test seti |
| Seed | 42 (ana deney); 42, 1, 7, 123, 2024 (tekrar deneyi) |
| Donanım | Intel CPU (12 mantıksal çekirdek), 32 GB RAM, GPU kullanılmadı |

## Özet sonuçlar (5 seed, ortalama ± standart sapma)

| Batch | Test doğruluğu | Test macro F1 | Epoch süresi (sn) |
| --- | --- | --- | --- |
| 16 | 0,9990 ± 0,0002 | 0,836 ± 0,042 | 3,04 ± 0,22 |
| 32 | 0,9989 ± 0,0002 | 0,841 ± 0,046 | 1,63 ± 0,02 |
| 64 | 0,9987 ± 0,0003 | 0,798 ± 0,049 | 1,00 ± 0,01 |
| 128 | 0,9983 ± 0,0003 | 0,742 ± 0,070 | 0,67 ± 0,00 |
| 256 | 0,9976 ± 0,0003 | 0,706 ± 0,045 | 0,53 ± 0,00 |
| 512 | 0,9968 ± 0,0002 | 0,638 ± 0,022 | 0,45 ± 0,01 |
| 1024 | 0,9961 ± 0,0001 | 0,536 ± 0,029 | 0,43 ± 0,01 |

Batch büyüklüğü arttıkça epoch süresi kısalmakta, ancak macro F1 skoru belirgin biçimde düşmektedir. Test doğruluğu tüm konfigürasyonlarda %99,6'nın üzerindedir; farklar özellikle az örnekli sınıflarda ortaya çıkmaktadır.

## Tekrarlanabilirlik

Tüm rastgele işlemler (`random`, `numpy`, `torch`, `DataLoader` karıştırması) sabit seed ile kontrol edilmektedir. Aynı donanım ve kütüphane sürümleriyle sonuçlar birebir tekrar edilebilir; farklı donanımlarda süre ölçümleri değişebilir.
