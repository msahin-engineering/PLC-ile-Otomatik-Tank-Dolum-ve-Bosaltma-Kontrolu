# PLC ile Otomatik Tank Dolum ve Boşaltma Kontrolü

## Proje Hakkında

Bu proje, Siemens S7-1200 PLC ve TIA Portal kullanılarak Factory I/O simülasyon ortamında geliştirilen otomatik tank dolum ve boşaltma kontrol uygulamasıdır.

Projenin amacı, bir tank içerisindeki sıvı seviyesini analog sensör aracılığıyla ölçmek, ölçülen verileri PLC üzerinde işlemek ve belirlenen kontrol koşullarına göre doldurma ve boşaltma valflerini otomatik olarak yönetmektir.

Sistem, tankın dolum ve boşaltma işlemlerini belirli bir çalışma döngüsü içerisinde gerçekleştirir. Sıvı seviyesi gerçek zamanlı olarak izlenir ve kontrol panelindeki göstergelere aktarılır.

## Kullanılan Yazılımlar ve Donanımlar

| Bileşen | Açıklama |
|---|---|
| PLC | Siemens SIMATIC S7-1200 CPU 1214C AC/DC/Rly |
| Analog Giriş Modülü | Siemens SM 1232 AO |
| Programlama Yazılımı | Siemens TIA Portal |
| Simülasyon Yazılımı | Factory I/O |
| PLC Simülasyonu | Siemens S7-PLCSIM |
| Programlama Dili | Merdiven Diyagramı (LAD) |
| Kontrol Yöntemi | Aç-Kapa Kontrolü |
| Seviye Ölçümü | Analog Seviye Sensörü |
| Kontrol Elemanları | Doldurma ve Boşaltma Valfleri |

## Sistemin Çalışma Prensibi

**1. Sistemin Başlatılması**

Başlatma butonuna basıldığında PLC içerisindeki sistem çalışma bilgisi etkinleştirilir. Sistem, sensörden gelen verileri işlemeye ve tank kontrolünü gerçekleştirmeye başlar.

**2. Analog Seviye Bilgisinin Okunması**

Tank içerisindeki sıvı seviyesi analog sensör üzerinden ölçülür. Sensörden gelen sayısal veri PLC tarafından okunur ve hesaplamalarda kullanılmak üzere gerçek sayı veri tipine dönüştürülür.

**3. Sıvı Seviyesinin Hesaplanması**

Sensörden alınan analog değer, tankın fiziksel ölçüm aralığına dönüştürülür.

Projede kullanılan hesaplama:

`Sıvı Seviyesi = (Analog Değer × 300) / 27648`

Bu işlem sayesinde sensörden alınan ham veri, tankın seviye bilgisi olarak kullanılabilir.

**4. Tankın Doldurulması**

Sistem çalışırken sıvı seviyesi belirlenen üst sınırın altındaysa ve zamanlayıcı dolum işlemini engellemiyorsa doldurma valfi etkinleştirilir.

Tank içerisindeki sıvı seviyesi yükseldikçe analog sensör verisi sürekli güncellenir.

**5. Dolumun Durdurulması**

Sıvı seviyesi 150 birime ulaştığında doldurma valfine sıfır değeri gönderilerek dolum işlemi durdurulur.

**6. Zamanlayıcı Kontrolü**

Sistemde 45 saniyelik bir zamanlayıcı kullanılmıştır.

Zamanlayıcı, tankın dolum ve boşaltma işlemlerinin belirli bir çalışma sırasına göre gerçekleştirilmesini sağlar.

**7. Tankın Boşaltılması**

Programdaki koşullar sağlandığında boşaltma valfine kontrol değeri gönderilir ve tank içerisindeki sıvı tahliye edilmeye başlanır.

Sıvı seviyesi azaldıkça sensör verisi PLC tarafından izlenmeye devam eder.

**8. Boşaltmanın Tamamlanması**

Tank seviyesi alt sınır olan 0,1 birimine veya daha düşük bir değere ulaştığında boşaltma valfine sıfır değeri gönderilir.

Sistem, programlanan koşullara göre yeniden dolum aşamasına geçebilir.

## PLC Programının Bölümleri

PLC programı toplam 9 ağdan oluşmaktadır.

| Ağ | Görevi |
|---|---|
| 1 | Factory I/O Haberleşme Bağlantısı |
| 2 | Sistemi Başlatma ve Durdurma Kontrolü |
| 3 | Sistem Durdurma ve Valfleri Kapatma |
| 4 | Seviye Sensöründen Veri Okuma ve Dönüştürme |
| 5 | Sıvı Seviyesini Hesaplama |
| 6 | Gerçek Seviye Değerini Çıkışa Aktarma |
| 7 | Doldurma Süresini Kontrol Etme |
| 8 | Tank Doldurma Kontrolü |
| 9 | Tank Boşaltma Kontrolü |

## PLC Giriş ve Çıkış Adresleri

| Değişken | Adres | Veri Tipi |
|---|---|---|
| Başlatma Butonu | %I0.0 | Bool |
| Durdurma Butonu | %I0.1 | Bool |
| Sıfırlama Butonu | %I0.2 | Bool |
| Doldurma Miktarı | %IW30 | Int |
| Seviye Tespit Sensörü | %IW32 | Int |
| Başlatma Lambası | %Q0.0 | Bool |
| Durdurma Lambası | %Q0.1 | Bool |
| Sıfırlama Lambası | %Q0.2 | Bool |
| Doldurma Valfi | %QW30 | Int |
| Boşaltma Valfi | %QW32 | Int |
| Hedef Seviye | %QW34 | Int |
| Gerçek Seviye | %QW36 | Int |
| Sistem Çalışma Bilgisi | %M10.0 | Bool |
| Hesaplanan Sıvı Seviyesi | %MD26 | Real |

## Proje Görselleri

### 1. PLC Donanım Yapılandırması

Siemens S7-1200 PLC ve analog modülün TIA Portal ortamındaki donanım yapılandırması.

![PLC Donanım Yapılandırması](1%20-%20PLC%20Donanım%20Yapılandırması.png)

### 2. PLC Değişken Tablosu

PLC giriş, çıkış ve hafıza adreslerinin tanımlandığı değişken tablosu.

![PLC Değişken Tablosu](2%20-%20PLC%20Değişken%20Tablosu.png)

### 3. Sistem Başlatma ve Haberleşme

Factory I/O haberleşmesinin ve sistem başlatma kontrolünün PLC programı üzerinden gerçekleştirilmesi.

![Sistem Başlatma ve Haberleşme](3%20-%20Sistem%20Başlatma%20ve%20Haberleşme.png)

### 4. Tank Dolumunun Başlaması

Doldurma valfinin etkinleştirilmesi ve tank içerisindeki sıvı seviyesinin yükselmeye başlaması.

![Tank Dolumunun Başlaması](4-%20Tank%20Dolumunun%20Başlaması.png)

### 5. Tank Dolum Seviyesi Kontrolü

Tank içerisindeki sıvı seviyesinin analog sensör aracılığıyla ölçülmesi ve PLC üzerinden takip edilmesi.

![Tank Dolum Seviyesi Kontrolü](5%20-%20Tank%20Dolum%20Seviyesi%20Kontrolü.png)

### 6. Tank Boşaltma İşleminin Başlaması

Boşaltma valfinin etkinleştirilmesiyle tank içerisindeki sıvının tahliye edilmeye başlanması.

![Tank Boşaltma İşleminin Başlaması](6%20-%20Tank%20Boşaltma%20İşleminin%20Başlaması.png)

### 7. Tank Boşaltma İşleminin Devamı

Tank içerisindeki sıvı seviyesinin azalması ve boşaltma işleminin PLC tarafından kontrol edilmesi.

![Tank Boşaltma İşleminin Devamı](7%20-%20Tank%20Boşaltma%20İşleminin%20Devamı.png)

### 8. Tankın Yeniden Doldurulması

Boşaltma aşamasından sonra sistemin yeniden dolum işlemine geçmesi ve çalışma döngüsünün devam etmesi.

![Tankın Yeniden Doldurulması](8%20-%20Tankın%20Yeniden%20Doldurulması.png)

## Proje Dosyası

Projenin TIA Portal arşivi depo içerisinde `.zap18` uzantısıyla bulunmaktadır.

Arşiv, uygun TIA Portal sürümü kullanılarak açılabilir.

## Proje Kapsamında Uygulanan Konular

- Siemens S7-1200 PLC programlama
- Merdiven diyagramı ile kontrol algoritması geliştirme
- Factory I/O ile PLC haberleşmesi
- Analog giriş ve çıkış işlemleri
- Analog verilerin ölçeklendirilmesi
- Tam sayı ve gerçek sayı veri tipi dönüşümleri
- Karşılaştırma komutlarıyla seviye kontrolü
- Zamanlayıcı kullanımı
- Aç-kapa kontrol yöntemi
- Otomatik tank dolum ve boşaltma işlemleri
- Gerçek zamanlı simülasyon ve sistem izleme

## Sonuç

Bu projede, endüstriyel otomasyon sistemlerinde kullanılan analog veri işleme, seviye ölçümü, zamanlayıcı ve valf kontrolü uygulamaları Siemens S7-1200 PLC üzerinde gerçekleştirilmiştir.

TIA Portal ve Factory I/O birlikte kullanılarak tankın otomatik dolum ve boşaltma süreçleri simülasyon ortamında uygulanmış ve sistemin farklı çalışma aşamaları gözlemlenmiştir.
