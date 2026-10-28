<div align="center">

# Fortuna Moda — Sohbetten Kargoya Sipariş Paneli

**WhatsApp ve Instagram DM siparişlerini kontrol edilmiş, gönderime hazır DHL eCommerce kayıtlarına çevirir**

![Durum](https://img.shields.io/badge/panel-Ekim%202026'dan%20beri%20canl%C4%B1da-2e7d32)
![Sürüm](https://img.shields.io/badge/s%C3%BCr%C3%BCm-v1.12.1%20·%2027%20Ekim%202026-455a64)
![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178c6?logo=typescript&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169e1?logo=postgresql&logoColor=white)
![Vitest](https://img.shields.io/badge/s%C4%B1nav-236%20durum-6e9f18?logo=vitest&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-GHCR-2496ed?logo=docker&logoColor=white)

Black Wool Project · **Project Lead & Developer** · [English](README.md)

<img src="docs/screenshots/01-orders-masked.jpg" alt="Sipariş listesi" width="900">

</div>

> **Bu depo hakkında.** Black Wool'da, siparişlerini WhatsApp ve Instagram sohbetlerinden alan giyim
> markası **Fortuna Moda** için geliştirildi. Kaynak kodu gizli; bu bir vaka çalışmasıdır. Ekran
> görüntüleri 28 Ekim 2026'da canlı panelden çekildi; müşteri adları kapatıldı.

---

## Özet

| | |
|---|---|
| **Müşteri** | Fortuna Moda — WhatsApp ve Instagram DM'den satış yapan, kapıda ödemeli giyim markası |
| **Akış** | Sohbeti yapıştır ya da ekran görüntüsü bırak → alanlar okunur → işletme kontrol edip onaylar → DHL kaydı ve etiket |
| **Okuma doğruluğu** | 24 sohbetlik ölçüm setinde model okuyucuyla ad, adres ve tutar 24/24; bütün okuyucularda **0 "yanlış ama kesin"** |
| **Durum** | Panel Ekim 2026'dan beri kendi sunucusunda canlı (v1.12.1). DHL eCommerce entegrasyonu resmî API tanımından yazıldı; işletmenin kargo hesabı bağlanıyor |
| **Kod tabanı** | 10 model · ~6.000 satır TypeScript · 36 sınav dosyası / 236 sınav durumu · 215 commit |

<sub>28 Ekim 2026'da depodan ve canlı panelden ölçüldü. Ölçüm seti uydurma sohbetlerden oluşuyor, gerçek müşterilerden değil.</sub>

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
| **Gönderi** | İki adımda DHL eCommerce kaydı ve etiket (sipariş → barkod), etiket indirme, durum çizelgesi |
| **Siparişler ve özet** | Durum halkası (onay bekleyen, kargoya hazır, yolda, hata), 14 günlük küçük grafikler; ad, telefon, sipariş no ya da ilçeyle arama; ciro kapıda tahsilat toplamı |
| **Ürünler** | Ürün ve beden/renk listesi istendiğinde markanın kendi sitesinden, siteyi yormadan — sırayla ve aralarında bekleyerek — aktarılır |

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
bağlanmadan canlıya çıkıp uçtan uca sınanabildi.

---

## Teknolojiler

| Katman | Teknoloji |
|---|---|
| Uygulama | Next.js 16, React 19, TypeScript (strict), Prisma ORM 7, PostgreSQL, Zod 4 |
| Okuma | Kurallı okuyucu, model okuyucu (HTTP üzerinden sınıflandırıcı, SDK yok), yerel model dosyalı tesseract.js OCR |
| Kargo | DHL eCommerce (MNG ApiZone) REST istemcisi: belirteç → createOrder → createbarcode |
| Kalite | Vitest — 36 sınav dosyası, 236 durum; her birleşmede imaj derlenmeden önce `tsc --noEmit` ile birlikte çalışır |
| Dağıtım | Docker, GitHub Actions → GHCR, Linux sunucuda kendi konteynerleri, Cloudflare Tunnel |

---

## Rolüm

Projeyi Black Wool adına, marka sahibi ilk kullanıcı olacak şekilde yönettim: sipariş akışı ve
güvenlik kuralları, okuma hattı ve ölçüm seti, resmî API tanımından DHL entegrasyonu, panel ve
dağıtım.

## Bu depoda olmayanlar

Kaynak kod, API anahtarları, müşteri verileri, panelin adresi. Ölçüm sohbetleri uydurmadır.
