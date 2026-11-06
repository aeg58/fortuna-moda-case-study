<div align="center">

# Fortuna Moda — Sohbetten Kargoya Sipariş Paneli

**WhatsApp ve Instagram DM siparişlerini kontrol edilmiş DHL eCommerce gönderilerine çevirir; etiketler doğrudan panelden basılır**

![Durum](https://img.shields.io/badge/panel-Ekim%202026'dan%20beri%20canl%C4%B1da-2e7d32)
![Sürüm](https://img.shields.io/badge/s%C3%BCr%C3%BCm-v1.30.0%20·%204%20Kas%C4%B1m%202026-455a64)
![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178c6?logo=typescript&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169e1?logo=postgresql&logoColor=white)
![Vitest](https://img.shields.io/badge/s%C4%B1nav-453%20durum-6e9f18?logo=vitest&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-GHCR-2496ed?logo=docker&logoColor=white)

Black Wool Project · **Project Lead & Developer** · [English](README.md)

<img src="docs/screenshots/01-orders-masked.jpg" alt="Sipariş listesi" width="900">

</div>

> **Bu depo hakkında.** Black Wool'da, siparişlerini WhatsApp ve Instagram sohbetlerinden alan giyim
> markası **Fortuna Moda** için geliştirildi. Kaynak kodu gizli; bu bir vaka çalışmasıdır. Ekran
> görüntüleri 28 Ekim 2026'da canlı panelden çekildi (v1.12); ana ekranlar 4 Kasım 2026'da yenilendi.
> Müşteri adları kapatıldı.

---

## Özet

| | |
|---|---|
| **Müşteri** | Fortuna Moda — WhatsApp ve Instagram DM'den satış yapan, kapıda ödemeli giyim markası |
| **Akış** | Sohbeti yapıştır ya da ekran görüntüsü bırak → alanlar okunur → işletme kontrol edip onaylar → DHL kaydı ve etiket |
| **Okuma doğruluğu** | 24 sohbetlik ölçüm setinde model okuyucuyla ad, adres ve tutar 24/24; bütün okuyucularda **0 "yanlış ama kesin"** |
| **Durum** | Panel Ekim 2026'dan beri kendi sunucusunda canlı (v1.30.0). 2 Kasım 2026'dan beri işletmenin gerçek DHL eCommerce hesabına bağlı; ilk gerçek siparişler gönderildi ve etiketleri dükkânda panelden basıldı |
| **Kod tabanı** | 11 model · ~12.300 satır TypeScript · 56 sınav dosyası / 453 sınav durumu · ana dalda 464 commit |

<sub>Kod tabanı sayıları 6 Kasım 2026'da depodan ölçüldü (ilk yayın 28 Ekim: v1.12.1, 236 sınav). Ölçüm seti uydurma sohbetlerden oluşuyor, gerçek müşterilerden değil.</sub>

---

## Sorun

Küçük bir marka sohbetten satış yapıyor: müşteri adını, telefonunu, ilini, ilçesini ve adresini
birkaç mesaja dağıtarak, çoğu zaman tek uzun satırda, bazen adı bir cümlenin içine gömerek yazıyor;
işletme fiyatla cevap veriyor. Sonra her sipariş elle kargo sistemine yeniden yazılıyordu — yavaş,
ve tek bir yanlış ilçe geri dönen bir paket demek.

Hedef "yapay zekâ formu doldursun" değildi. Hedef şuydu: **okunabileni oku, okunamayanı asla
uydurma ve gönderi açılmadan önce şüpheli her alanı gözden kaçırılamaz yap.**

## Ne yaptım

| Alan | Ne yapar |
|---|---|
| **Metinden yeni sipariş** | WhatsApp'tan kopyalanan mesajları yapıştır (iPhone ve Android biçimleri, çok satırlı mesajlar); metin mesajlara ayrılır, her mesajın göndereni bulunur |
| **Ekran görüntüsünden yeni sipariş** | Bir ya da birkaç sohbet görüntüsü bırak; metin yerel bir OCR modeliyle çıkarılır ve aynı okuma hattından geçer |
| **Kontrol ve onay** | Her alan *kesin* ya da *kontrol edin* diye gösterilir; işletme düzeltir ve onaylar — insan onayı olmadan hiçbir şey kargoya gitmez |
| **Gönderi** | Onay siparişi saniyeler içinde DHL'e aktarır; barkod ve etiket iki dakika sonra arka planda, yeniden denemeyle istenir. Etiket önizlemesi, durum çizelgesi, saatlik DHL durum okuyucu |
| **Yazdırma** | Etiketler QZ Tray ile doğrudan dükkânın termal yazıcısına gider — tek tek ya da toplu, "basıldı" takibiyle, yazdırma penceresi açılmadan. Yazıcının yazı tipinin basamadığı harfler (İ, Ğ, Ş) etikette çevrilir |
| **Siparişler ve özet** | Kartları aynı zamanda süzgeç olan tek durum şeridi, 14 günlük çubuk grafikler, kapıda tahsilat toplamları; ad, telefon, ürün, beden, tutar, adres ve sipariş no'da arama |
| **Müşteriler** | Her müşterinin sipariş sayısı, toplamı ve son siparişi; tek dokunuşla bütün siparişleri ürünleriyle |
| **Raporlar** | On rapor bölümü (ürünler, bedenler, yerler, zaman, müşteriler, kanal, sepet, kargo, durum, kâr); her biri Excel olarak indirilebilir |
| **Ürünler ve kâr** | Ürün ve beden/renk listesi istendiğinde markanın kendi sitesinden, siteyi yormadan — sırayla ve aralarında bekleyerek — aktarılır; ürün görselleri; maliyet girişi ve sipariş başına kâr |
| **Birden çok işletme** | Tek hesap birden fazla işletmede çalışıp aralarında geçiş yapabilir; her işletme yalnız kendi verisini görür |

---

## Ekranlar

<img src="docs/screenshots/02-new-order-paste.jpg" alt="Yapıştırılan sohbetten yeni sipariş" width="900">

<sub><b>Yeni sipariş.</b> İki giriş yolu: ekran görüntüsü ya da yapıştırılan sohbet metni. İkisi de aynı okuma kurallarından, sonra kontrol ekranından geçiyor.</sub>

---

## Okuma hattı

```mermaid
flowchart LR
    IN1[Yapıştırılan sohbet] --> SPLIT[Mesajlara ayır<br/>göndereni bul]
    IN2[Ekran görüntüleri] --> OCR[Yerel OCR<br/>çalışırken hiçbir şey indirilmez]
    OCR --> SPLIT
    SPLIT --> R{Model anahtarı<br/>var mı?}
    R -- evet --> J[Model okuyucu<br/>satır seçer, değer yazmaz]
    R -- hayır / cevap yok --> K[Kurallı okuyucu]
    J --> V[Alan kuralları<br/>telefon, tutar, il ve ilçe<br/>DHL'in kendi listesine karşı]
    K --> V
    V --> UI["Kontrol ekranı<br/>her alan: kesin / kontrol edin"]
    UI -->|insan onayı| DHL[DHL eCommerce<br/>sipariş → barkod → etiket]
```

**Yapay zekâ okur, kod karar verir.** Model bir sınıflandırıcı olarak kullanılıyor: adın hangi
satırda olduğunu ve her satırın teslimat adresine ait olup olmadığını seçiyor. Değerlerin kendisi
özgün metinden kurallarla kesiliyor ve her değer sonra alan kurallarından geçiyor. Bir il ya da ilçe
ancak DHL'in kendi il/ilçe listesinde **tam olarak bir** kayda uyuyorsa kabul ediliyor (bilinen başka
yazılışlarıyla, ör. *Afyonkarahisar → AFYON*); sıfır ya da birden çok aday *kontrol edin* demek,
asla tahmin değil.

---

## Mühendislik kararları

### 1. Önemli olan ölçü "yanlış ama kesin"

Bir alanı boş ve işaretli bırakan okuyucu işletmeye on saniyeye mal olur. Yanlış bir değeri
**işaretsiz** dolduran okuyucu ise kaybolan bir pakete. Bu yüzden her okuyucu aynı ölçüm setinde alan
alan ölçülüyor ve asıl sayı *yanlış ama kesin* — hedef 0.

| Okuyucu (24 sohbet) | Ad | Telefon | Adres | Tutar | Yanlış ama kesin |
|---|---|---|---|---|---|
| OCR + kural | 19/24 | 24/24 | 22/24 | 20/24 | **0** |
| Metin → model okuyucu | **24/24** | 24/24 | 24/24 | 24/24 | **0** |
| Görüntü → OCR → model okuyucu | **24/24** | 24/24 | 24/24 | 20/24 | **0** |

Ölçümün kendisi de sınanıyor: her cevabı bilen sahte bir model 24/24 alıyor, kendinden emin uyduran
sahte bir model ise *yanlış ama kesin* sayacına düşüyor.

### 2. Uçtan uca bulunan hata sınav durumuna dönüşüyor

İki durum, canlı akışın uydurma sohbet görüntüleriyle sınanmasından çıktı: cümlenin ortasındaki bir ad
etiketi ("Olur. Ad soyad: …") okunmadı, "Tamam alıyorum. Mehmet …" gibi bir cümle adın parçası
sanıldı **ve kesin diye işaretlendi**. İkisi de ölçüm setine eklendi ve ad okuma, bütün okuyucuların
kullandığı tek bir ortak modüle taşındı.

### 3. Tarayıcının dosya hakkında söylediğine güvenme

Yüklenen ekran görüntülerinin türü, saklanmadan önce tarayıcının bildirdiği türden değil, dosyanın
**ilk baytlarından** anlaşılıyor. Onay ya da iptalden sonra formdan gelen "dönülecek adres" yalnız
panelin kendi sıra sayfasını gösteriyorsa kabul ediliyor — açık yönlendirme yok.

### 4. Parolayı sızdırmayan giriş sınırı

Hatalı girişler veritabanında sayılıyor (uygulama yeniden başlasa da sayaç sürüyor). Sınır dolunca
pencerenin sonuna kadar doğru parola da reddediliyor; böylece sınırın kendisi, tahmin edilen bir
parolayı doğrulamak için kullanılamıyor. Parolalar Node'un yerleşik scrypt'iyle özetleniyor.

### 5. Üç DHL kipi, tek arayüz

DHL istemcisinin tek bir arayüz arkasında birbirinin yerine geçebilen üç uygulaması var: `fake`
(DHL'e hiç gitmez), `sandbox` ve `live`. Anahtar, parola ve belirteçler hata mesajına ya da kayda hiç
yazılmıyor. Sistemin geri kalanı hangisiyle konuştuğunu bilmiyor — panel bu sayede kargo hesabı
bağlanmadan canlıya çıkıp uçtan uca sınanabildi. 2 Kasım 2026'da gerçek hesaba geçmek bir kod
değişikliği değil, bir ayar değişikliği oldu.

### 6. Onay kargoyu beklememeli

Başta sipariş onayı, DHL'in hem kaydı hem barkodu oluşturmasını bekliyordu. DHL barkodu bazen
dakikalar sonra veriyor ya da yedek barkod dönüyor; düğme takılı kalabiliyordu. Artık onay yalnız
siparişi aktarıp saniyeler içinde bitiyor; arka plandaki bir iş etiketi iki dakika sonra istiyor, beş
dakikada bir yeniden deniyor, otuz dakikada vazgeçiyor. Zamanlama kuralı kendi sınavları olan saf bir
fonksiyon — iki dakika dolmadan "şimdi iste" derse düşen bir kalibrasyon sınavı da dahil.

### 7. İşletme ayrımı yalnız kodda değil, veritabanında

İşletme verisi tutan her tablo PostgreSQL satır düzeyi güvenliğiyle (row-level security) korunuyor:
uygulamanın veritabanı rolü yalnız o isteğin işletmesine ait satırları görüyor. İşletme belirtilmemişse
**hiçbir satır görünmüyor** — hata durumu "boş", "her şey" değil. Sınavlar bir işletmenin ötekinin
siparişini okuyamadığını, yazamadığını ve değiştiremediğini; bir kalibrasyon sınavı da uygulama rolünün
üyelik tablosunu doğrudan okuyamadığını denetliyor. Tek hesabın iki işletme arasında geçişini güvenli
kılan bu.

---

## Teknolojiler

| Katman | Teknoloji |
|---|---|
| Uygulama | Next.js 16, React 19, TypeScript (strict), Prisma ORM 7, satır düzeyi güvenlikli PostgreSQL, Zod 4 |
| Okuma | Kurallı okuyucu, model okuyucu (HTTP üzerinden sınıflandırıcı, SDK yok), yerel model dosyalı tesseract.js OCR |
| Kargo ve yazdırma | DHL eCommerce (MNG ApiZone) REST istemcisi: belirteç → createOrder → createbarcode, arka planda etiket işi · RSA-SHA512 imzalı isteklerle QZ Tray |
| Kalite | Vitest — 56 sınav dosyası, 453 durum; her birleşmede imaj derlenmeden önce `tsc --noEmit` ile birlikte çalışır |
| Dağıtım | Docker, GitHub Actions → GHCR, Linux sunucuda kendi konteynerleri, Cloudflare Tunnel |

---

## Rolüm

Projeyi Black Wool adına, marka sahibi ilk kullanıcı olacak şekilde yönettim: sipariş akışı ve
güvenlik kuralları, okuma hattı ve ölçüm seti, resmî API tanımından DHL entegrasyonu, dükkânda
etiket basımı, panel ve dağıtım. Her sürüm ancak benim açık onayımla canlıya çıkıyor ve geri alma
adımıyla birlikte bir GitHub sürümü olarak kaydediliyor.

## Bu depoda olmayanlar

Kaynak kod, API anahtarları, müşteri verileri, panelin adresi. Ölçüm sohbetleri uydurmadır.
