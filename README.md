# Bash Scripting & Kabuk Programlama Kılavuzu

Modern altyapı yönetiminde, DevOps süreçlerinde ve veri mühendisliği hatlarında (pipeline) otomasyon süreçlerinin temel taşı olan Bash (Born Again Shell) kabuk programlamaya ait temel ve ileri seviye teknik notlar, en iyi pratikler (best practices) ve pratik uygulama betikleri.

Bu depo, Linux/Unix sistemlerde süreçleri optimize etmek ve tekrarlayan görevleri güvenli bir şekilde otomatikleştirmek isteyen mühendisler için yapılandırılmış bir başvuru kılavuzu niteliğindedir.

---

## 🚀 İçerik ve Yol Haritası

Depo içerisindeki dökümantasyonlar, temel kabuk mantığından başlayarak ileri seviye süreç yönetimi ve fonksiyon mimarilerine doğru uzanan 10 modülden oluşmaktadır:

### 🛠️ Temel Kabuk Mantığı
* **[01. Kabuk Programlamaya Giriş](01_kabuk_programlamaya_giris.md):** Shebang (`#!`) mekanizması, kullanılabilir kabukların tespiti ve ilk betiğin çalıştırılma süreçleri.
* **[02. Kullanıcı Girdileri ve `read`](02_kullanici_girdileri_ve_read.md):** İnteraktif betikler yazma, satır içi prompt (`-p`), gizli girdi (`-s`) ve zaman aşımı (`-t`) yönetimi.

### 🧠 Karar Yapıları ve Mantıksal Koşullar
* **[03. Koşullu İfadeler ve Karar Yapıları](03_kosullu_ifadeler_ve_karar_yapilari.md):** `if-else` blokları, boşluk kuralları, POSIX bayrakları (`-gt`, `-lt`) ve modern çift parantez `(( ))` kullanımı.
* **[04. Tek Satır Koşullar ve Healthcheck](04_tek_satir_kosullar_ve_healthcheck.md):** Satır içi (inline) koşullar ve mantıksal `||` operatörü ile servis sağlık kontrolü mekanizmaları.
* **[06. Dosya ve Dizin Test Operatörleri](06_bash_dosya_ve_dizin_operatorleri.md):** Dosya varlığı (`-e`), tip kontrolleri (`-f`, `-d`) ve izin hiyerarşilerinin denetimi.

### 🔄 Döngüler ve Akış Yönetimi
* **[07. For Döngüleri ve İterasyon](07_bash_for_dönguleri_ve_iterasyon.md):** C tipi döngüler, menzil (range) kullanımı ve güvenli dizin tarama (`Globbing`) yöntemleri.
* **[08. While Döngüleri ve Akıştan Okuma](08_bash_while_donguleri_ve_akistan_okuma.md):** Bellek dostu (stream) yöntemlerle girdi yönlendirmesi (`<`) kullanarak büyük dosyaları satır satır işleme.

### 🎭 İleri Seviye ve Mimari Yönetim
* **[05. Argüman Yönetimi ve Parametre Yönetimi](05_bash_argumanlari_ve_parametre_yonetimi.md):** Konumsal parametreler (`$0` - `$n`), `$#` kullanımı ve yerleşik `getopts` aracıyla dinamik flag yönetimi.
* **[09. Fonksiyonlar ve Modüler Kodlama](09_bash_fonksiyonlari_ve_moduler_kodlama.md):** Modüler kod blokları, lokal değişken kapsamı (`local`), `return` sınırları ve çıktı yakalama teknikleri.
* **[10. Kontrol Operatörleri ve Süreç Yönetimi](10_bash_kontrol_operatorleri_ve_surec_yonetimi.md):** Süreçlerin arka plana atılması (`&`), sıralı çalıştırma (`;`), mantıksal zincirler (`&&`, `||`) ve boru hattı (`|`) mimarisi.

---

## 💡 Temel Tasarım İlkeleri (Best Practices)

Bu depodaki tüm betikler ve dökümanlar oluşturulurken aşağıdaki endüstri standartları gözetilmiştir:

1. **Güvenli Dosya Okuma:** Bellek şişmesini önlemek ve boşluklu dosya isimlerinde kırılmaları engellemek adına `for i in $(ls)` yerine **Globbing**; `cat file` yerine **`while read -r`** akışları tercih edilmiştir.
2. **Kapsam Güvenliği (Scoping):** Fonksiyon içi değişkenlerin ana betiği kirletmemesi adına `local` saklama belirteçleri kullanılmıştır.
3. **Kısa Devre Mantığı (Short-Circuit Evaluation):** Altyapı kontrollerinde sistemlerin kilitlenmesini önleyen akıllı `&&` ve `||` mantıksal geçişleri entegre edilmiştir.

---

## 🛠️ Nasıl Kullanılır?

1. Depoyu yerel ortamınıza klonlayın:
   ```bash
   git clone [https://github.com/kullanici_adi/bash-scripting-guide.git](https://github.com/kullanici_adi/bash-scripting-guide.git)
   cd bash-scripting-guide
