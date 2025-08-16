# RFID ile Bina Erişimi:

## RFID Nedir ve Ne İşe Yarar?
RFID (Radio Frequency Identification), radyo frekansı ile **temassız kimlik tanımlama** sağlayan bir teknolojidir. Küçük bir çip (kart, anahtarlık, telefon) ve anten aracılığıyla kimlik bilgileri kablosuz olarak okuyucuya aktarılır.  

**Başlıca kullanım alanları:**  
- **Kimlik doğrulama:** Kartlı geçiş sistemleri, bina erişimi  
- **Takip:** Lojistik ve envanter yönetimi  
- **Ödeme:** Temassız kart/telefon ile hızlı ödeme  
- **Sağlık:** Hasta ve ekipman takibi  
- **Erişim kontrolü:** Ofis, kampüs, laboratuvar, kritik altyapı alanları  

Avantajlar: hızlı, pratik, düşük maliyetli, temassız.  
Dezavantajlar: eski protokoller zayıf, kopyalanma riski, RF parazit etkisi.  

---

## Giriş: Neden RFID?
- Kullanıcı dostu: kartı/telefonu yaklaştır, geç.  
- Dağıtımı ucuz ve hızlı.  
- Loglama ve izlenebilirlik sağlıyor.  
- NFC entegrasyonu ile mobil cihazlarla uyumlu.  
- Ancak: **yanlış yapılandırma ve eski protokoller** → ciddi güvenlik riskleri.

---

## Tarihçe ve Gelişim
- **1940’lar:** Radar sistemleri ve uçak dost-düşman tanımlama.  
- **1970’ler:** Ticari RFID uygulamaları.  
- **1990’lar:** Lojistik, depo yönetimi.  
- **2000’ler:** Kartlı geçiş sistemlerinin patlaması.  
- **2020 sonrası:** Mobil NFC, bulut erişim kontrolü, IoT entegrasyonu.

---

## Temel Kavramlar ve Mimari
- **Etiket (Tag):** Pasif/aktif; sadece ID veya kriptografik veri.  
- **Okuyucu (Reader):** Anten + RF modülü + backend bağlantısı.  
- **Kontrol Paneli:** Yetkiyi kontrol eder, kapıyı açar.  
- **Access Control Server:** Politika motoru, loglama.  
- **Protokoller:** ISO 14443, ISO 15693, NFC Forum, OSDP.  

---

## RFID Türleri
- **LF (125–134 kHz):** Basit ID, düşük güvenlik.  
- **HF/NFC (13.56 MHz):** ISO 14443, MIFARE, DesFire; orta-yüksek güvenlik.  
- **UHF (860–960 MHz):** Uzun mesafe, lojistik.  
- **Aktif RFID:** Bataryalı, metrelerce mesafe.  

---

## Tipik Bina Erişim Sistemleri
- Kartlar/anahtarlıklar (MIFARE, DESFire, HID iCLASS)  
- Reader (kapı, turnike, asansör)  
- Panel/Controller  
- Merkezi Access Management (AD/LDAP, PDKS)  
- Log & SIEM entegrasyonu  
- Fiziksel güvenlik (reader konumu, kablo koruma)  

---

## Standartlar ve Regülasyonlar
- **ISO/IEC 14443 & 15693** — yakın alan/vicinity kartları  
- **MIFARE Classic / DESFire** — en yaygın kartlar  
- **NFC Forum spesifikasyonları** — mobil entegrasyon  
- **OSDP vs Wiegand:** Wiegand şifresiz/zayıf, OSDP şifreli/güvenli  
- **NIST SP 800-116r1 & FIPS 201-3:** ABD federal standartları  
- **AB GDPR & 2009/387/EC:** RFID ve kişisel verilerin korunması  
- **Türkiye KVKK 6698:** RFID logları = kişisel veri  

---

## Tehdit Modeli ve Saldırı Kategorileri
- **Kimlik ele geçirme (Cloning)**  
- **Replay saldırıları**  
- **Relay (uzatma) saldırıları**  
- **Reader/Backend compromise**  
- **DoS (RF paraziti, jammer)**  
- **Yan kanal saldırıları**  
- **Insider abuse (iç tehdit)**  

---

## Güvenlik Zafiyetleri ve Sömürü Mantığı
### Donanımsal Açıklar
- **Wiegand hattı:** Şifresiz, kolay dinlenebilir.  
- **Tamper koruması olmayan reader:** Fiziksel müdahaleye açık.  

### Yazılımsal Açıklar
- **MIFARE Classic (Crypto-1):** Kırılmış, kolay kopyalanır.  
- **Varsayılan parolalar / zayıf config:** Kontrol paneli ve backend için risk.  
- **Anahtar yönetimi eksikliği:** Tek master key, tüm sistemi tehlikeye sokar.  

### İstismar Senaryoları (mantıksal seviye)
- **Sniffing:** Trafiği dinleyip kart ID’sini yakalamak.  
- **Replay:** Kaydedilen sinyali tekrar göndermek.  
- **Cloning:** Kartın kopyasını üretip sisteme giriş.  
- **Relay:** Kullanıcı uzakta olsa bile sinyali genişletmek.  

---

## Algılama ve Forensik İpuçları
- Aynı kart ID’sinin aynı anda farklı kapılarda kullanılması.  
- Normal dışı saatlerde yoğun erişim denemeleri.  
- Panel reboot logları ile eşleşen erişimler.  
- Kamera kayıtları ile erişim loglarını korelasyon.  
- Okuyucu firmware versiyon kontrolü.  

---

## Mitigasyon ve Güçlendirme
- Eski kartları bırak, modern DESFire/EV3 veya PIV kullan.  
- OSDP Secure Channel zorunlu, Wiegand’ı terk et.  
- Anahtar yönetimi: rotasyon, HSM/SAM, benzersiz key.  
- Loglama ve SIEM entegrasyonu.  
- Fiziksel güvenlik: reader kapak koruma, anti-tamper.  
- Çok faktörlü kimlik doğrulama (kart + PIN/biometrik).  
- Eğitim: tailgating ve sosyal mühendislik farkındalığı.  

---

## AR-GE Deneyleri (Savunma Odaklı)
- **Envanter çıkarma:** Kart tipleri, reader modelleri.  
- **Log tutarlılığı testi:** Sahte anomali senaryoları.  
- **Fiziksel güvenlik testleri:** Kablo ve panel incelemesi.  
- **Latency ölçümü:** Relay saldırılarına karşı zaman-of-flight analizi.  
- **Entegrasyon testi:** HR/AD offboarding → erişim iptali.  

---

## Yanlış Algılar
- “RFID kopyalanamaz” → Yanlış.  
- “Kısa mesafe güvenlidir” → Yanlış.  
- “Modern kart aldık, iş bitti” → Yanlış. Yanlış config her şeyi zayıflatır.  

---

## Karşılaştırmalı Analiz: RFID vs Alternatifler
- **RFID vs QR Kod:** Ucuz ama kolay kopyalanır.  
- **RFID vs BLE:** Mobil cihazlarla esnek ama batarya bağımlı.  
- **RFID vs Biometrik:** Güçlü ama gizlilik hassasiyeti var.  
- **Mekanik anahtar:** Dayanıklı ama yönetimi zor.  

---

## Gelecek Trendleri
- Mobil NFC kimlikler (telefon/akıllı saat)  
- Bulut tabanlı erişim yönetimi  
- AI tabanlı anomali tespiti  
- Kuantum sonrası kriptografi hazırlıkları  

---

## Case Study
- **MIFARE Classic kırılması (2008):** Hollanda metrosu → milyonlarca kart kopyalandı.  
- **HID iCLASS zafiyeti (2010):** Kart klonlama vakaları.  
- **Ders:** Modern DESFire/OSDP çözümlerine geçiş şart.  

---

## Sonuç
RFID, bina güvenliği için vazgeçilmez ama **güvenlik seviyesi kullanılan teknolojiye ve konfigürasyona bağlıdır.**  
Yanlış yapılandırılmış veya eski protokollere dayalı RFID sistemleri kolayca istismar edilebilir.  

**Öneriler:**  
- MIFARE Classic / Wiegand → terk edilmeli.  
- DESFire / OSDP Secure Channel → standart olmalı.  
- Anahtar yönetimi ve loglama disiplinli yürütülmeli.  
- Düzenli sızma testleri ve AR-GE tatbikatları yapılmalı.  

