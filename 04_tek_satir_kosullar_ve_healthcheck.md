# Bash ile Tek Satır Koşulları (Inline) ve Mantıksal Sağlık Kontrolleri (Healthcheck)

Bash scriptlerinde her zaman çok satırlı `if-else` blokları kullanmak gerekmez. Özellikle terminal üzerinde anlık script çalıştırırken veya basit sağlık kontrolleri (healthcheck) yaparken, kodları tek satıra indirgemek (inline) ve mantıksal operatörlerden (`||`, `&&`) yararlanmak süreçleri hızlandırır.

---

## 1. Tek Satırda `if-else` Kullanımı (Inline Conditionals)

Çok satırlı bir `if` bloğunu tek bir satırda birleştirmek için komutların bittiği yerleri noktalı virgül (`;`) ile ayırmamız gerekir.

### A. Metinsel ve Değer Karşılaştırması
Aşağıdaki örneklerde `x` değişkeninin değeri tek satırda kontrol edilmektedir:


# Senaryo 1: Koşulun doğru (true) olma durumu
x=5
if [ $x = 5 ]; then echo "Doğru"; else echo "Yanlış"; fi
# Çıktı: Doğru

# Senaryo 2: Koşulun yanlış (false) olma durumu
x=4
if [ $x = 5 ]; then echo "Doğru"; else echo "Yanlış"; fi
# Çıktı: Yanlış

B. Sayısal ve Matematiksel Karşılaştırma
Daha önce de belirttiğimiz gibi, büyüktür (>) veya küçüktür (<) gibi matematiksel operatörleri tek satırda kullanırken çift parantez (( )) yapısını tercih ederiz:
x=4
if (( x > 5 )); then echo "5'ten büyük"; else echo "5'ten küçük veya eşit"; fi
# Çıktı: 5'ten küçük veya eşit

2. Mantıksal || (OR) Operatörü ile Sağlık Kontrolü (Healthcheck)
Sistem yönetiminde ve CI/CD süreçlerinde bir komutun başarısız olma durumunu yakalamak ve buna göre bir aksiyon almak (veya log basmak) için || (Veya) operatörü sıklıkla kullanılır.

Kısa Devre (Short-Circuit) Mantığı:
komut1 || komut2 yapısında Bash önce komut1'i çalıştırır.

Eğer komut1 başarılı olursa (Exit Status = 0), Bash komut2'yi çalıştırmaz.

Eğer komut1 başarısız olursa (Exit Status != 0), Bash hemen sağ tarafa geçer ve komut2'yi tetikler.

Canlı Senaryo: Apache HTTP Sunucusu Testi
Öncelikle test ortamımıza bir web sunucusu kurup başlatalım:
# Apache HTTP Server kurulumu ve başlatılması
sudo yum -y install httpd
sudo systemctl start httpd

Senaryo A: Başarılı Healthcheck (Çalışan Port)
Apache varsayılan olarak 80 portundan yayın yapar. curl ile bu porta istek attığımızda servis ayakta olduğu için || sağındaki hata mesajı çalıştırılmaz:
curl localhost:80 || echo "################ Sistem Çalışmıyor! ###############"
# Çıktı: (HTML kaynak kodları veya başarılı bağlantı çıktısı gelir, hata mesajı basılmaz.)
Senaryo B: Başarısız Healthcheck (Kapalı Port)
Şimdi de sistemde açık olmayan ya da dinlenmeyen bir porta (81) istek atalım. curl bağlantı kuramayacağı için başarısızlık dönecek ve || operatörü sağ taraftaki komutu tetikleyecektir:
curl localhost:81 || echo "################ Sistem Çalışmıyor! ###############"

curl: (7) Failed to connect to localhost port 81: Connection refused
################ Sistem Çalışmıyor! ###############

💡 Profesyonel İpucu: Eğer curl'ün kendi hata çıktılarını (curl: (7)...) gizlemek ve sadece kendi yazdığınız özel hata mesajını görmek istiyorsanız, curl komutuna sessiz mod -s (silent) parametresini ekleyebilir veya çıktıları çöpe (/dev/null) yönlendirebilirsiniz:
curl -s localhost:81 || echo "Servis çökmüş durumda!"
