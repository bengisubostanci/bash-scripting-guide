# Bash Scripting'de Koşullu İfadeler ve Karar Yapıları (`if-else`)

Sistem otomasyonlarında, dosya kontrollerinde ve mantıksal karar süreçlerinde `if-else` blokları betiğin akışını yöneten temel yapılardır. Bash kabuğunda koşullu ifadeler yazılırken sözdizimi (syntax) kurallarına ve boşluklara dikkat etmek kritik önem taşır.

---

## 1. Temel Mantıksal Yapı (Sözdizimi)

Bash üzerindeki en kapsamlı karar yapısı şu şekildedir:

bash
if [ kosul ]; then
   # Koşul doğruysa çalışacak komutlar
elif [ diger_kosul ]; then
   # İlk koşul yanlış, bu koşul doğruysa çalışacak komutlar
else
   # Verilen tüm koşullar yanlışsa çalışacak komutlar
fi

⚠️ Kritik Kural: if [ kosul ] yapısında, köşeli parantezlerin [ iç kısmında ve dış kısmında mutlaka birer boşluk bırakılmalıdır. Aksi takdirde Bash, parantezi bir komut olarak algılamaya çalışır ve syntax error hatası fırlatır.

2. Dosya ve Dizin Kontrolleri (-d, -f, -e)
Veri mühendisliği hatlarında (pipeline) veya yedekleme betiklerinde, işlem yapmadan önce hedef dizinin veya dosyanın varlığını kontrol etmek en temel güvenlik adımıdır.

Aşağıdaki betik, dinamik olarak bir dizin kontrol mekanizması oluşturur:

cat << 'EOF' | tee dizin_kontrol.sh
#!/bin/bash

DIRECTORY="/home/prod_user/play/test"

# -d parametresi belirtilen yolun bir 'dizin' olup olmadığını kontrol eder
if [ -d "$DIRECTORY" ]; then
    echo "Başarılı: $DIRECTORY dizini sistemde mevcut."
else
    echo "Hata: $DIRECTORY dizini bulunamadı!"
fi
EOF

Betiği Yayına Alma ve Test Adımları:
chmod +x dizin_kontrol.sh
./dizin_kontrol.sh

Test Senaryosu:

Dizin yokken çalıştırıldığında hata mesajı döner.

mkdir -p /home/prod_user/play/test komutuyla dizin oluşturulup script tekrar tetiklendiğinde "Mevcut" çıktısı alınır.

3. Sayısal Karşılaştırmalar ve Operatör Farkları
Bash kabuğunda sayıları karşılaştırırken iki farklı yaklaşım mevcuttur: Klasik POSIX tarzı metinsel operatörler ve modern matematiksel çift parantez (( )) yapısı.

A. Matematiksel Operatörler ile (( )) Kullanımı
Eğer <, >, <=, >= gibi geleneksel matematiksel sembolleri kullanmak istiyorsanız, koşulu çift parantez (( )) içerisine almalısınız. Bu yapının içinde değişkenlerin başına $ işareti koymak zorunlu değildir.

cat << 'EOF' | tee basamak_kontrol.sh
#!/bin/bash

NUMBER=8

# Matematiksel büyüktür (>) kontrolü
if (( NUMBER > 9 )); then
   echo "$NUMBER iki veya daha fazla basamaklı bir sayıdır."
else
   echo "$NUMBER tek basamaklı bir sayıdır."
fi
EOF

B. Klasik Köşeli Parantez [ ] ile Bayrak Kullanımı
Klasik tekli köşeli parantez içinde < veya > işaretleri doğrudan kullanılamaz (kullanılırsa dosya yönlendirme olarak algılanır). Bunun yerine şu bayraklar (flags) tercih edilir:

-gt (Greater Than - Büyüktür)

-lt (Less Than - Küçüktür)

-eq (Equal - Eşittir)

-ne (Not Equal - Eşit Değildir)

4. Mantıksal Operatörler ile Çoklu Koşul Yönetimi (AND / OR)
Birden fazla koşulu aynı anda değerlendirmek istediğimizde AND (Ve) ile OR (Veya) mantığını iki farklı yöntemle kurabiliriz.

Yöntem A: Modern Çift Parantez / Çift Köşeli Parantez (&& ve ||)
En güvenli ve okunabilir yöntem, koşulları ayrı ayrı gruplayıp araya && koymaktır:
cat << 'EOF' | tee mantiksal_kontrol.sh
#!/bin/bash

NUMBER=8

# Sayının 0'dan büyük VE 9'dan küçük eşit olma durumu (Pozitif tek basamaklı kontrolü)
if [ "$NUMBER" -gt 0 ] && [ "$NUMBER" -lte 9 ]; then
   echo "$NUMBER pozitif ve tek basamaklı bir sayıdır."
else
   echo "$NUMBER istenen kriterlere uymuyor."
fi
EOF
Yöntem B: Eski Tip Klasik Parametreler (-a ve -o)
Tek bir köşeli parantez içinde -a (AND) veya -o (OR) parametreleri de kullanılabilir. Ancak modern Bash scriptlerinde okunabilirliği artırmak adına yukarıdaki Yöntem A daha sık tercih edilir.
if [ "$NUMBER" -gt 0 -a "$NUMBER" -lt 9 ]; then
   echo "$NUMBER başarıyla doğrulandı."
fi

5. Koşul İçinde Aritmetik İfadeler ve Mod Alma (Tek/Çift Sayı Kontrolü)
Bir sayının çift mi yoksa tek mi olduğunu anlamak için matematiksel mod alma (%) işleminin sonucunu if bloğuna parametre olarak geçebiliriz:
cat << 'EOF' | tee tek_cift_kontrol.sh
#!/bin/bash

NUMBER=3

# Aritmetik işlem çift parantez içinde yapılarak değişkene atanır
MODULO=$(( NUMBER % 2 ))

# Sonuç 0'a eşit mi kontrolü
if [ "$MODULO" -eq 0 ]; then
   echo "$NUMBER bir ÇİFT sayıdır."
else
   echo "$NUMBER bir TEK sayıdır."
fi
EOF



