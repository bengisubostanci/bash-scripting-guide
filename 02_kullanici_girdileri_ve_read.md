# Bash ile İnteraktif Betikler: Kullanıcı Girdilerini Yönetmek (`read`)

Sistem otomasyonlarında veya veri süreçlerinde kullanıcıdan dinamik parametreler, onaylar (Evet/Hayır) veya hassas veriler (şifreler, API anahtarları) almak gerekebilir. Bash kabuğunda bu işlem için `read` komutu ve çeşitli parametreleri kullanılır.

---

## 1. Temel Kullanıcı Girdisi Yakalama

En temel seviyede `read` komutu, kullanıcının klavyeden giriş yapmasını bekler ve `Enter` tuşuna basıldığında bu veriyi belirtilen bir değişkene atar.

Aşağıdaki blok, dinamik olarak basit bir interaktif script oluşturur:

bash
cat << 'EOF' | tee interaktif_girdi.sh
#!/bin/bash

# Kullanıcıya soru yönlendiriyoruz
echo "Lütfen adınızı giriniz:"

# Kullanıcının yazdığı metni 'KULLANICI_ADI' değişkenine atıyoruz
read KULLANICI_ADI

# Yakalanan değişkeni ekrana yazdırıyoruz
echo "Tanıştığımıza memnun oldum, $KULLANICI_ADI."
EOF

Betiği Çalıştırılabilir Yapma ve Test Etme:
chmod +x interaktif_girdi.sh
./interaktif_girdi.sh

Çalışma Mantığı:

Betik echo satırını çalıştırır ve alt satırdaki read komutuna geldiğinde yürütmeyi durdurarak kullanıcının veri girmesini bekler.

Kullanıcı metni yazıp Enter tuşuna bastığı an, girdiler KULLANICI_ADI değişkenine aktarılır ve betik kaldığı yerden devam eder.

2. İleri Seviye ve Profesyonel read Parametreleri
Gerçek dünya senaryolarında (DevOps süreçleri, script konfigürasyonları) sadece read kullanmak yetersiz kalabilir. Kod kalitesini artıracak bazı kritik parametreler şunlardır:

A. -p Parametresi (Prompt - Satır İçi Soru)
echo ve read komutlarını ayrı ayrı yazmak yerine, soruyu doğrudan read komutunun içine gömebiliriz. Bu, kodun daha temiz kalmasını sağlar.

read -p "Lütfen projenizin ortam adını girin (Örn: dev, prod): " ENVIRONMENT_NAME
echo "Seçilen ortam: $ENVIRONMENT_NAME"
B. -s Parametresi (Silent - Gizli Girdi)
Veri tabanı şifresi, token veya API key gibi hassas veriler alınırken, kullanıcının yazdığı karakterlerin ekranda görünmemesi (maskelenmesi) gerekir. Güvenlik için -s parametresi kritik önem taşır.

read -s -p "Lütfen AWS Gizli Anahtarınızı (Secret Key) giriniz: " AWS_SECRET_KEY
echo -e "\n[GÜVENLİ] Anahtar başarıyla hafızaya alındı."

(Not: -e parametresi, gizli girdiden sonra alt satıra geçebilmek için kullanılan \n kaçış karakterinin çalışmasını sağlar.)

C. -t Parametresi (Timeout - Zaman Aşımı)
Eğer script bir CI/CD pipeline'ında veya otomasyonda çalışıyorsa ve bir kullanıcı girdisi bekliyorsa, sonsuza kadar askıda kalmamalıdır. -t parametresi ile saniye cinsinden bir bekleme süresi sınırı konulabilir.

read -t 5 -p "5 saniye içinde onay verin (y/n): " USER_APPROVAL

if [ -z "$USER_APPROVAL" ]; then
    echo -e "\n[UYARI] Zaman aşımı gerçekleşti, işlem iptal edildi."
else
    echo -e "\nİşleminiz işleme alınıyor..."
fi

Özet Parametre Tablosu
Parametre,Görevi,Kullanım Amacı
-p,Ekrana mesaj basarak girdi bekler,"Kod kalabalığını önler, satır içi girdi sunar."
-s,Yazılan karakterleri ekranda gizler,Şifre ve API Key gibi hassas verilerin güvenliği.
-t,Belirlenen süre sonunda girdiyi sonlandırır,Otomasyonların kilitlenmesini engeller.
-n,Belirli sayıda karaktere ulaşıldığında Enter beklemeden geçer,Hızlı onay mekanizmaları (Tek karakterlik y/n seçimleri).
