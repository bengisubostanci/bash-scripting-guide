# Bash Scripting'de Fonksiyonlar ve Modüler Kodlama

Büyük ve karmaşık Bash scriptleri yazarken kod tekrarını önlemek, okunabilirliği artırmak ve bakımı kolaylaştırmak adına fonksiyonlar (functions) kullanılır. Fonksiyonlar, belirli bir görevi yerine getiren ve ihtiyaç duyulduğunda ismiyle çağrılan küçük kod bloklarıdır.

---

## 1. Fonksiyon Tanımlama Sözdizimi (Syntax)

Bash kabuğunda bir fonksiyon iki farklı yöntemle tanımlanabilir. Modern ve en çok tercih edilen yöntem ilkidir:

`bash
# Yöntem A (Önerilen - Standart Sözdizimi)
fonksiyon_adi() {
    # Komutlar
}

# Yöntem B ('function' anahtar kelimesi ile)
function fonksiyon_adi {
    # Komutlar
}

Örnek 1: Temel Fonksiyon Kullanımı
cat << 'EOF' | tee fonksiyon_temel.sh
#!/bin/bash

# Fonksiyonun tanımlanması
merhaba_de() {
    echo "Selam! Ben sistemdeki ilk fonksiyonum."
}

# Fonksiyonun çağrılması (Parantez kullanılmaz)
merhaba_de
EOF

Çalıştırma ve Test:
chmod +x fonksiyon_temel.sh
./fonksiyon_temel.sh
# Çıktı: Selam! Ben sistemdeki ilk fonksiyonum.

2. Fonksiyonlara Parametre (Argüman) Geçmek
Fonksiyonlar, scriptin genel argümanlarından ($1, $2) bağımsız olarak kendi içlerinde de özel konumsal parametreler alırlar. Fonksiyona gönderilen ilk değer fonksiyon içinde $1, ikincisi ise $2 olarak yakalanır.

Örnek 2: Parametreli Matematiksel Fonksiyon (Toplama)
cat << 'EOF' | tee fonksiyon_parametre.sh
#!/bin/bash

# İki sayıyı toplayıp ekrana basan fonksiyon
topla() {
    # $1 ve $2 bu fonksiyona özel lokal parametrelerdir
    echo "$1 + $2 = $(( $1 + $2 ))"
}

# Fonksiyonu argümanlarla birlikte çağırıyoruz
topla 3 5
EOF

Çalıştırma ve Test:
chmod +x fonksiyon_parametre.sh
./fonksiyon_parametre.sh
# Çıktı: 3 + 5 = 8
3. Fonksiyonlarda Geri Dönüş Değerleri (return ve $?)
Bash fonksiyonlarındaki return ifadesi, diğer programlama dillerindeki (Python, JavaScript vb.) gibi doğrudan bir metin veya nesne döndürmez. Bash'te return, yalnızca fonksiyonun çıkış durumunu (Exit Status) belirtmek için 0 ile 255 arasında sayısal bir değer alır.

Son çalışan komutun veya fonksiyonun çıkış kodunu yakalamak için $? özel değişkeni kullanılır.

Örnek 3: Çıkış Kodu Kontrollü Fonksiyon
cat << 'EOF' | tee fonksiyon_return.sh
#!/bin/bash

hesapla_ve_don() {
    local sayi1=$1
    local sayi2=$2
    local sonuc=$(( sayi1 + sayi2 ))
    
    # Sonucu çıkış kodu (exit status) olarak geriye döndürüyoruz
    return $sonuc
}

# Fonksiyon tetikleniyor
hesapla_ve_don 13 15

# Fonksiyondan dönen değer hemen alt satırda '$?' ile yakalanır
FONKSIYON_CIKISI=$?

echo "Fonksiyondan dönen durum kodu (Result): $FONKSIYON_CIKISI"
EOF
Çalıştırma ve Test:
chmod +x fonksiyon_return.sh
./fonksiyon_return.sh
# Çıktı: Fonksiyondan dönen durum kodu (Result): 28

⚠️ Kritik Mühendislik Uyarısı: return Sınırı ve Alternatif Çözüm
Bash'te return ile en fazla 255 sayısına kadar değer döndürebilirsiniz. Eğer fonksiyonun çıktısı 256 olursa, sistem bunu 0 olarak kabul eder (Mod 256 aritmetiği nedeniyle).

Büyük Sayılar veya Metin (String) Döndürmek İçin Doğru Yöntem:
Fonksiyondan veri tabanı bağlantı dizesi, büyük bir sayı veya metin döndürmek istiyorsanız return yerine fonksiyon içinde echo kullanmalı ve fonksiyonu Command Substitution $(...) ile çağırmalısınız:
buyuk_islem() {
    local hesaplama=$(( 500 + 400 ))
    echo "$hesaplama" # Çıktıyı doğrudan standart output'a basıyoruz
}

# Fonksiyonun echo çıktısını değişkene hapsediyoruz
PROD_RESULT=$(buyuk_islem)
echo "Güvenli Alınan Çıktı: $PROD_RESULT"
# Çıktı: Güvenli Alınan Çıktı: 900 (Asla 255 sınırına takılmaz)


