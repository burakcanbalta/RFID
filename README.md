<img width="1154" height="664" alt="access-control-system-app-story" src="https://github.com/user-attachments/assets/e0a8c40d-4615-400a-9ab5-2c4b4e124be8" />


## Bir Pentester Gözünden RFID Güvenliği, Fiziksel Erişim Sistemleri ve Modern Saldırı Yüzeyi

> **Yazar:** Burak Balta  
> **Kategori:** Physical Security • RFID • Red Team • Pentest  
> **Tahmini Okuma Süresi:** 20–25 Dakika

---

> [!NOTE]
> Bu makale, RFID tabanlı fiziksel erişim kontrol sistemlerini savunma perspektifinden ele almaktadır. Amaç; RFID teknolojisinin çalışma prensiplerini, kurumsal mimarilerdeki yerini ve modern güvenlik yaklaşımlarını teknik temelleriyle açıklamaktır. Makalede yer alan bilgiler, fiziksel güvenlik risklerinin anlaşılması ve daha güvenli sistemlerin tasarlanmasına katkı sağlamak amacıyla hazırlanmıştır.

---
# Giriş

> *"Bir saldırgan için kurum ağına açılan ilk kapı çoğu zaman VPN değildir. O kapı, her sabah yüzlerce çalışanın farkında bile olmadan kullandığı kart okuyucusudur."*

Kurumsal siber güvenlik denildiğinde akla çoğunlukla güvenlik duvarları, uç nokta koruma çözümleri (EDR), çok faktörlü kimlik doğrulama (MFA), Active Directory altyapıları veya bulut güvenliği gelir. Son yıllarda kurumlar güvenlik yatırımlarını büyük ölçüde bu alanlara yöneltmektedir. Bunun temel nedeni, siber saldırıların büyük bölümünün internet üzerinden gerçekleştiği düşüncesidir.

Ancak gerçek Red Team operasyonları, saldırı yüzeyinin yalnızca internete açık sistemlerden ibaret olmadığını gösterir. Fiziksel güvenlik katmanında bulunan bir zafiyet, en güçlü görünen mantıksal güvenlik kontrollerini bile dolaylı olarak etkisiz hâle getirebilir. Bir saldırganın kurum ağına fiziksel olarak erişebilmesi; boşta bırakılmış ağ portlarının kullanılması, yönetim cihazlarına doğrudan ulaşılması veya operasyonel süreçlerdeki eksikliklerden yararlanılması gibi birçok riski beraberinde getirir.

Bu nedenle günümüzde fiziksel güvenlik ile bilgi güvenliği birbirinden bağımsız iki disiplin olarak değerlendirilemez. Özellikle büyük ölçekli organizasyonlarda fiziksel erişim kontrol sistemleri (Physical Access Control Systems – **PACS**), kimlik yönetimi, güvenlik operasyon merkezleri (SOC), olay izleme platformları ve merkezi dizin servisleriyle doğrudan entegre çalışmaktadır.

Bir çalışanın bina girişinde kartını okutması yalnızca kapının açılması anlamına gelmez. Aynı anda;

- Kimlik doğrulama gerçekleştirilir.
- Yetkilendirme politikaları değerlendirilir.
- Olay kayıtları oluşturulur.
- Merkezi log altyapısına veri gönderilir.
- SIEM platformlarında korelasyon kuralları çalıştırılabilir.
- Fiziksel erişim olayları dijital güvenlik olaylarıyla ilişkilendirilebilir.

Dolayısıyla fiziksel erişim sistemleri artık yalnızca bina güvenliğinden sorumlu bağımsız altyapılar değildir. Modern kurumsal siber güvenlik mimarisinin ayrılmaz bir bileşeni hâline gelmiştir.

Bu mimarinin merkezinde ise uzun yıllardır **RFID (Radio Frequency Identification)** teknolojisi bulunmaktadır.

---

# Neden RFID Hâlâ Bu Kadar Yaygın?

Bugün ofis binalarında kullanılan personel kartlarından veri merkezlerine, üretim tesislerinden üniversite kampüslerine kadar birçok fiziksel erişim sistemi RFID tabanlıdır.

RFID teknolojisinin yaygın olarak tercih edilmesinin başlıca nedenleri şunlardır:

- Düşük operasyonel maliyet
- Temassız kullanım kolaylığı
- Milisaniyeler içerisinde kimlik doğrulama
- Merkezi yönetim desteği
- Kimlik yönetim sistemleriyle entegrasyon
- Uzun donanım ömrü
- Farklı sektörlerde kullanılabilmesi

Kullanıcı açısından süreç son derece basittir.

Kart okuyucuya yaklaştırılır.

Kimlik doğrulama gerçekleştirilir.

Kapı açılır.

Ancak bu kadar kısa sürede gerçekleşen işlem, arka planda oldukça karmaşık bir güvenlik mimarisi tarafından yürütülmektedir.

---

# Kart Okutulduğunda Arka Planda Ne Olur?

Bir RFID kartı okuyucuya yaklaştırıldığında yalnızca kart ile okuyucu arasında veri alışverişi gerçekleşmez.

Kimlik bilgisi, kurumsal fiziksel erişim zinciri boyunca aşağıdaki bileşenlerden geçerek değerlendirilir.

```text
+------------------+
| RFID Credential  |
+------------------+
          │
          ▼
+------------------+
| RFID Reader      |
+------------------+
          │
          ▼
+------------------+
| Access Controller|
+------------------+
          │
          ▼
+------------------------+
| Access Control Server  |
+------------------------+
      │        │        │
      │        │        │
      ▼        ▼        ▼
 Active   SIEM / SOC   LDAP
Directory              IAM
```

Bu mimaride her bileşen farklı güvenlik sorumluluklarına sahiptir.

| Bileşen | Görevi |
|---------|---------|
| RFID Kart | Dijital kimliği temsil eder. |
| RFID Okuyucu | Kimlik bilgisini alır ve ilk doğrulama sürecini başlatır. |
| Controller | Yetkilendirme kararını üretir. |
| Access Server | Politika yönetimi ve merkezi erişim kontrolünü sağlar. |
| Active Directory / IAM | Kullanıcı yaşam döngüsünü yönetir. |
| SIEM | Güvenlik olaylarını ilişkilendirerek analiz eder. |

Dolayısıyla güvenlik yalnızca kart üzerinde değil, zincirin tamamında değerlendirilmelidir.

---

# Bir Pentester Aynı Sisteme Nasıl Bakar?

Kullanıcı için okuyucu yalnızca duvara monte edilmiş küçük bir cihazdır.

Bir penetrasyon test uzmanı için ise aynı cihaz;

- Radyo frekansı haberleşmesi
- Gömülü sistem güvenliği
- Kimlik doğrulama protokolleri
- Haberleşme güvenliği
- Ağ segmentasyonu
- Active Directory entegrasyonu
- Kimlik yaşam döngüsü
- Merkezi loglama
- Güvenlik politikaları

gibi birçok farklı güvenlik katmanının başlangıç noktasıdır.

Bu nedenle profesyonel fiziksel güvenlik değerlendirmelerinde temel amaç yalnızca kart teknolojisini incelemek değildir.

Asıl amaç;

> **"Fiziksel erişim güven zincirinin herhangi bir halkasında kurumun güvenliğini etkileyebilecek mimari veya operasyonel eksiklikler bulunuyor mu?"**

sorusuna teknik verilerle cevap verebilmektir.

---

# Bu Makalede Neleri İnceleyeceğiz?

Bu makale boyunca RFID teknolojisini yalnızca kartlar üzerinden değerlendirmeyeceğiz.

Bunun yerine fiziksel erişim kontrol sistemlerini uçtan uca ele alacağız.

İnceleyeceğimiz başlıca konular şunlardır:

- RFID teknolojisinin çalışma prensibi
- Elektromanyetik indüksiyon
- LF, HF ve UHF sistemleri
- ISO/IEC 14443 standardı
- RFID kart teknolojileri
- PACS mimarisi
- Active Directory ve IAM entegrasyonu
- SIEM korelasyonu
- OSDP Secure Channel
- Zero Trust Physical Access
- Mobil kimlik teknolojileri
- Yapay zekâ destekli davranış analitiği
- Fiziksel erişim sistemlerinin geleceği

Makalenin amacı saldırı yöntemlerini öğretmek değildir.

Amaç; kurumların fiziksel erişim altyapılarının neden kritik olduğunu açıklamak, modern güvenlik standartlarını tanıtmak ve fiziksel güvenlik mimarisinin bütünsel olarak nasıl değerlendirildiğini teknik temelleriyle ortaya koymaktır.

---

# RFID Teknolojisinin Temelleri

RFID (Radio Frequency Identification), nesnelerin radyo frekansları aracılığıyla kablosuz olarak tanımlanmasını sağlayan otomatik kimlik tanımlama teknolojilerinden biridir. Barkod sistemlerinden farklı olarak doğrudan görüş hattına ihtiyaç duymaz. Kart veya etiket ile okuyucu arasında elektromanyetik alan üzerinden gerçekleşen haberleşme sayesinde kimlik doğrulama işlemleri milisaniyeler içerisinde tamamlanabilir.

Günümüzde RFID teknolojisi yalnızca fiziksel erişim kontrol sistemlerinde değil; lojistik, tedarik zinciri yönetimi, sağlık sektörü, perakende, toplu taşıma, üretim tesisleri ve kimlik doğrulama sistemleri gibi çok geniş bir kullanım alanına sahiptir.

Kurumsal yapılarda ise RFID'nin en yaygın kullanım amacı, personel ve ziyaretçi erişimlerinin güvenli ve merkezi olarak yönetilmesidir.

---

# RFID Nasıl Çalışır?

Her RFID sistemi temelde dört ana bileşenden oluşur.

| Bileşen | Görevi |
|----------|---------|
| RFID Tag (Credential) | Kimlik bilgisini taşır. |
| RFID Reader | Kart ile haberleşmeyi başlatır. |
| Access Controller | Yetkilendirme kararını verir. |
| Access Control Server | Merkezi politika ve kullanıcı yönetimini gerçekleştirir. |

Bu bileşenler birlikte çalışarak fiziksel erişim kararının verilmesini sağlar.

Aşağıdaki diyagram tipik bir kurumsal mimariyi göstermektedir.

```text
RFID Card
     │
     ▼
RFID Reader
     │
     ▼
Access Controller
     │
     ▼
Access Control Server
     │
 ┌───┴─────────────┐
 ▼                 ▼
Identity        SIEM
Management
```

Kart okuyucuya yaklaştırıldığında haberleşme yalnızca kart ile okuyucu arasında gerçekleşmez. Kimlik bilgisi erişim paneline, merkezi sunucuya ve çoğu zaman kimlik yönetim sistemlerine kadar iletilir.

---

# Elektromanyetik İndüksiyon

Pasif RFID kartlarının içerisinde pil bulunmaz.

Bunun yerine okuyucunun oluşturduğu elektromanyetik alan kullanılarak gerekli enerji elde edilir.

Bu süreç Faraday'ın elektromanyetik indüksiyon prensibine dayanır.

Okuyucu anteni sürekli belirli bir frekansta elektromanyetik alan üretir.

Kart bu alanın içerisine girdiğinde anten bobini üzerinde indüklenen enerji kartın entegre devresini çalıştırır.

Kart aktif hâle geldikten sonra okuyucuyla veri alışverişi gerçekleştirilir.

Bu sayede herhangi bir dahili güç kaynağı bulunmamasına rağmen pasif RFID kartları uzun yıllar boyunca kullanılabilir.

---

# Pasif ve Aktif RFID Etiketleri

RFID sistemleri kullanılan enerji kaynağına göre üç ana gruba ayrılır.

| Tür | Güç Kaynağı | Okuma Mesafesi | Tipik Kullanım |
|------|-------------|----------------|----------------|
| Pasif | Okuyucudan alınır | Kısa | Personel kartları |
| Aktif | Dahili pil | Uzun | Araç takibi |
| Yarı Pasif | Pil destekli | Orta | Endüstriyel uygulamalar |

Kurumsal fiziksel erişim kontrol sistemlerinde neredeyse her zaman pasif RFID kartları kullanılmaktadır.

Bunun temel nedeni düşük maliyet, uzun kullanım ömrü ve bakım gerektirmemesidir.

---

# Haberleşme Süreci

Bir RFID kartı okuyucuya yaklaştırıldığında süreç saniyenin çok küçük bir bölümünde tamamlanır.

Temel işlem sırası aşağıdaki gibidir.

1. Okuyucu elektromanyetik alan üretir.
2. Kart gerekli enerjiyi toplar.
3. Kart ve okuyucu haberleşmeye başlar.
4. Kimlik doğrulama gerçekleştirilir.
5. Yetkilendirme değerlendirilir.
6. Sonuç erişim kontrol paneline iletilir.
7. Olay kayıtları merkezi sistemlerde saklanır.

Modern erişim sistemlerinde bu süreç karşılıklı kimlik doğrulama ve güvenli haberleşme mekanizmalarıyla desteklenmektedir.

---

# ISO/IEC 14443 Standardı

Kurumsal erişim kontrol sistemlerinde kullanılan modern kartların önemli bir bölümü ISO/IEC 14443 standardını temel alır.

Bu standart yalnızca fiziksel haberleşmeyi değil, kart ile okuyucu arasındaki veri alışverişinin temel kurallarını da tanımlar.

Standart dört temel bölümden oluşur.

| Bölüm | Açıklama |
|--------|----------|
| Physical Layer | Fiziksel haberleşme |
| Radio Frequency Interface | RF iletişim kuralları |
| Initialization & Anti-Collision | Kart seçimi |
| Transmission Protocol | Veri aktarımı |

Bu yapı sayesinde farklı üreticilere ait kartlar aynı standart çerçevesinde çalışabilir.

---

# UID Nedir?

Her RFID kartı üretim sırasında benzersiz bir tanımlayıcı ile ilişkilendirilir.

Bu tanımlayıcıya **UID (Unique Identifier)** adı verilir.

UID, kartın seri numarası olarak düşünülebilir.

Ancak UID tek başına güvenli kimlik doğrulama anlamına gelmez.

Modern erişim sistemleri yalnızca UID bilgisine güvenmek yerine kriptografik doğrulama mekanizmalarından yararlanır.

Bu yaklaşım, kimlik doğrulama sürecinin yalnızca kart numarasına dayanmasını engelleyerek güvenliği artırır.

---

# Anti-Collision Mekanizması

Bir okuyucunun kapsama alanında aynı anda birden fazla RFID kartı bulunabilir.

Bu durumda hangi kartla haberleşileceğinin belirlenmesi gerekir.

ISO/IEC 14443 standardı bu amaçla **Anti-Collision** mekanizmasını tanımlar.

Bu mekanizma sayesinde okuyucu aynı anda birden fazla kart algılasa bile iletişimi kontrollü şekilde yönetebilir.

Böylece veri çakışmaları önlenir ve doğru kart seçilerek haberleşme güvenilir biçimde devam eder.

---

# RFID Haberleşmesinde Güvenlik

Modern RFID sistemlerinde güvenlik yalnızca kart üzerinde depolanan bilgilerle sınırlı değildir.

Kimlik doğrulama süreci boyunca aşağıdaki güvenlik hedefleri sağlanmaya çalışılır.

- Kimliğin doğrulanması
- Haberleşmenin gizliliği
- Veri bütünlüğünün korunması
- Tekrar oynatma saldırılarının önlenmesi
- Yetkisiz erişimin engellenmesi

Bu nedenle güncel erişim sistemleri güçlü kriptografik algoritmalar, oturum anahtarları ve karşılıklı doğrulama mekanizmalarını destekleyen kart teknolojilerini tercih etmektedir.

---
---

# RFID Frekansları ve Standartları

RFID sistemleri çoğu zaman tek bir teknoloji gibi değerlendirilse de gerçekte farklı frekans bantlarında çalışan, farklı haberleşme standartlarını kullanan ve birbirinden oldukça farklı güvenlik özelliklerine sahip birçok teknolojiyi kapsar.

Kurumsal bir fiziksel erişim kontrol sistemi değerlendirilirken ilk incelenen konulardan biri kullanılan frekans bandıdır. Çünkü haberleşme mesafesi, veri aktarım hızı, çevresel koşullara dayanıklılık ve desteklenen güvenlik mekanizmaları büyük ölçüde kullanılan frekans standardına bağlıdır.

Genel olarak RFID sistemleri üç ana frekans grubunda incelenir.

| Frekans | Açılım | Tipik Kullanım |
|---------|---------|----------------|
| 125–134 kHz | Low Frequency (LF) | Eski nesil erişim sistemleri |
| 13.56 MHz | High Frequency (HF) | Kurumsal PACS, NFC, akıllı kartlar |
| 860–960 MHz | Ultra High Frequency (UHF) | Lojistik ve tedarik zinciri |

Her frekans bandı farklı ihtiyaçlara yönelik geliştirilmiştir ve güvenlik beklentileri de buna göre değişmektedir.

---

# Low Frequency (LF)

125–134 kHz aralığında çalışan LF sistemleri RFID teknolojisinin en eski örnekleri arasında yer alır.

Bu sistemler uzun yıllar boyunca;

- bina girişleri,
- otopark sistemleri,
- personel takip uygulamaları,
- endüstriyel tesisler

gibi alanlarda yaygın biçimde kullanılmıştır.

LF teknolojisinin en önemli avantajları şunlardır.

- Metal yüzeylerden daha az etkilenmesi
- Kararlı haberleşme
- Düşük maliyet
- Uzun donanım ömrü

Bununla birlikte günümüz güvenlik beklentileri açısından sınırlı özelliklere sahiptir.

Modern kurumsal yapılarda yeni kurulumlarda LF yerine HF tabanlı çözümler tercih edilmektedir.

---

# High Frequency (HF)

Bugün kurumsal fiziksel erişim sistemlerinin büyük bölümü 13.56 MHz bandında çalışan High Frequency (HF) teknolojisini kullanmaktadır.

HF sistemler yalnızca bina girişlerinde değil;

- elektronik pasaportlarda,
- temassız banka kartlarında,
- üniversite kimliklerinde,
- toplu taşıma kartlarında,
- mobil ödeme sistemlerinde,
- NFC tabanlı uygulamalarda

yaygın olarak kullanılmaktadır.

Bu teknolojinin yaygınlaşmasının temel nedenleri arasında daha gelişmiş haberleşme mekanizmaları, daha yüksek veri aktarım kapasitesi ve modern güvenlik standartlarını desteklemesi yer almaktadır.

HF sistemlerin önemli bir bölümü ISO/IEC 14443 standardını temel alır.

---

# Ultra High Frequency (UHF)

860–960 MHz bandında çalışan UHF RFID sistemleri erişim kontrolünden çok envanter yönetimi ve lojistik uygulamalarında kullanılmaktadır.

Başlıca kullanım alanları şunlardır.

- Depolar
- Kargo merkezleri
- Üretim hatları
- Perakende stok yönetimi
- Araç takip sistemleri

UHF sistemlerin en büyük avantajı metrelerce uzaktan okunabilmeleridir.

Buna karşılık fiziksel erişim kontrolü gibi yüksek güvenlik gerektiren senaryolarda genellikle HF teknolojisi tercih edilir.

---

# LF, HF ve UHF Karşılaştırması

| Özellik | LF | HF | UHF |
|----------|----|----|------|
| Frekans | 125–134 kHz | 13.56 MHz | 860–960 MHz |
| Okuma Mesafesi | Kısa | Kısa-Orta | Uzun |
| Veri Aktarım Hızı | Düşük | Orta | Yüksek |
| Tipik Kullanım | Eski erişim sistemleri | Kurumsal PACS | Lojistik |
| NFC Desteği | Hayır | Evet | Hayır |

---

# ISO/IEC 14443 Standardı

Modern erişim kartlarının büyük bölümü ISO/IEC 14443 standardına göre geliştirilmektedir.

Bu standart yalnızca kart ile okuyucu arasındaki fiziksel haberleşmeyi değil, aynı zamanda veri aktarım kurallarını da tanımlar.

ISO/IEC 14443 dört temel bölümden oluşur.

| Bölüm | Açıklama |
|--------|----------|
| Part 1 | Fiziksel özellikler |
| Part 2 | RF arayüzü |
| Part 3 | Başlatma ve kart seçimi |
| Part 4 | İletişim protokolü |

Bu standart sayesinde farklı üreticilerin geliştirdiği kartlar ortak kurallar çerçevesinde çalışabilir.

---

# NFC ile RFID Arasındaki İlişki

RFID ve NFC çoğu zaman aynı teknoloji olarak düşünülmektedir.

Gerçekte ise NFC, HF RFID teknolojisinin belirli özelliklerini temel alan daha üst seviyede bir haberleşme standardıdır.

Başka bir ifadeyle her NFC cihazı belirli RFID standartlarını destekler; ancak her RFID sistemi NFC değildir.

Akıllı telefonlarda bulunan NFC donanımları sayesinde kullanıcılar;

- dijital kimlik kullanabilir,
- mobil erişim sağlayabilir,
- temassız ödeme gerçekleştirebilir,
- elektronik kimlik doğrulaması yapabilir.

Bu nedenle modern fiziksel erişim sistemlerinde mobil kimlik çözümleri giderek yaygınlaşmaktadır.

---

# RFID Kart Teknolojileri

Kurumsal ortamlarda birçok farklı RFID kart ailesi kullanılmaktadır.

Her kart teknolojisi farklı güvenlik özellikleri ve kullanım senaryoları sunar.

En yaygın kart aileleri şunlardır.

- MIFARE Classic
- MIFARE Plus
- MIFARE DESFire
- HID iCLASS
- HID SEOS

Bu kartların tamamı aynı frekansta çalışıyor olabilir; ancak güvenlik özellikleri birbirinden önemli ölçüde farklıdır.

---

# MIFARE Classic

Uzun yıllar boyunca fiziksel erişim kontrol sistemlerinin fiili standardı hâline gelen MIFARE Classic, düşük maliyeti ve geniş donanım desteği sayesinde dünya genelinde milyonlarca kurum tarafından kullanılmıştır.

Bellek yapısı sektörler ve bloklar hâlinde düzenlenmiştir.

Her sektör ayrı erişim kurallarıyla yapılandırılabilir.

Günümüzde yeni projelerde daha gelişmiş kart teknolojileri tercih edilse de eski altyapılarda MIFARE Classic tabanlı sistemlerle hâlen karşılaşılmaktadır.

---

# MIFARE Plus

MIFARE Plus, mevcut altyapılarla uyumluluğu korurken daha gelişmiş güvenlik özellikleri sunmak amacıyla geliştirilmiştir.

Kurumların eski sistemlerden modern çözümlere geçişini kolaylaştırmak için tasarlanmıştır.

Birçok üretici geçiş sürecinde bu kart ailesini tercih etmektedir.

---

# MIFARE DESFire EV3

Kurumsal fiziksel erişim sistemlerinde en yaygın modern çözümlerden biri MIFARE DESFire EV3 kart ailesidir.

Başlıca özellikleri şunlardır.

- AES tabanlı güvenlik mekanizmaları
- Çoklu uygulama desteği
- Dosya tabanlı yapı
- Gelişmiş kimlik doğrulama desteği
- Kurumsal erişim yönetimine uygun mimari

Veri merkezleri, kritik altyapılar ve yüksek güvenlik gerektiren kurumsal yapılarda yaygın olarak tercih edilmektedir.

---

# HID iCLASS

HID Global tarafından geliştirilen iCLASS ailesi özellikle Kuzey Amerika'daki kurumsal erişim sistemlerinde yaygın olarak kullanılmaktadır.

Yıllar içerisinde farklı sürümleri geliştirilmiştir.

Bu nedenle yalnızca "iCLASS kullanıyoruz" ifadesi sistemin güvenlik seviyesi hakkında tek başına yeterli bilgi vermez.

---

# HID SEOS

HID SEOS günümüzde fiziksel erişim teknolojilerinin en modern örneklerinden biri olarak kabul edilmektedir.

SEOS mimarisi yalnızca plastik kartlarla sınırlı değildir.

Kimlik bilgileri;

- akıllı telefonlarda,
- dijital cüzdanlarda,
- giyilebilir cihazlarda,
- mobil kimlik uygulamalarında

güvenli biçimde kullanılabilir.

Mobil kimlik çözümlerinin yaygınlaşmasıyla birlikte SEOS tabanlı sistemlerin kullanım oranı da artmaktadır.

---

# Güvenliği Belirleyen Asıl Unsur Nedir?

Saha çalışmalarında sık karşılaşılan yanlış algılardan biri güvenliğin yalnızca kart modeliyle ilişkilendirilmesidir.

Gerçekte güvenlik çok daha geniş bir mimarinin sonucudur.

Bir fiziksel erişim sisteminin güvenlik seviyesi;

- kullanılan kart teknolojisine,
- haberleşme protokollerine,
- kimlik yönetimine,
- merkezi erişim politikalarına,
- olay izleme süreçlerine,
- düzenli güvenlik değerlendirmelerine

birlikte bağlıdır.

Dolayısıyla aynı kart teknolojisini kullanan iki kurum, tamamen farklı güvenlik seviyelerine sahip olabilir.

---
