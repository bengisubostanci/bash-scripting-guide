# Bash Scripting'de Argüman Yönetimi ve Parametre Ayıklama (`getopts`)

Komut satırı araçları (CLI) ve otomasyon betikleri geliştirirken, betiğe dışarıdan dinamik parametreler (argümanlar) geçirmek sıklıkla başvurulan bir yöntemdir. Bash, bu parametreleri yakalamak için yerleşik özel değişkenler ve gelişmiş bayrak (flag) yönetimi için `getopts` mekanizmasını sunar.

---

## 1. Konumsal Parametreler (Positional Parameters)

Betiği tetiklerken yanına yazdığımız her kelime/değer, sırasıyla özel değişkenlere atanır:
* `$0`: Çalıştırılan betiğin (script) kendi adını veya yolunu tutar.
* `$1, $2, $3 ... $n`: Sırasıyla 1., 2. ve n. argümanları temsil eder.
* `$#`: Betiğe toplamda kaç adet argüman gönderildiğinin sayısını verir.

### Tek Argüman Alan Betik Örneği (Tek/Çift Sayı Kontrolü)

Aşağıdaki blok, dışarıdan girdi olarak aldığı sayının modunu hesaplayan dinamik bir script oluşturur:

bash
cat << 'EOF' | tee tek_arguman.sh
#!/bin/bash

echo "Çalıştırılan Betik: $0"

# Dışarıdan gelen ilk argümanı ($1) bir değişkene atıyoruz
NUMBER=$1
MODULO=$(( NUMBER % 2 ))

if [ "$MODULO" -eq 0 ]; then
   echo "$NUMBER bir ÇİFT sayıdır."
else

chmod +x tek_arguman.sh
./tek_arguman.sh 5
Çıktı:
Çalıştırılan Betik: ./tek_arguman.sh
5 bir TEK sayıdır.

2. Çoklu Argüman Yönetimi
Birden fazla argüman gönderildiğinde, bu değerler sırasıyla $1 ve $2 konumlarına yerleşir.
cat << 'EOF' | tee coklu_arguman.sh
#!/bin/bash

# İki argümanın büyüklük karşılaştırması ($1 ve $2)
if [ "$1" -gt "$2" ]; then
   echo "$1 değeri $2 değerinden büyüktür."
else
   echo "$2 değeri $1 değerinden büyüktür veya eşittir."
fi
EOF
Çalıştırma ve Test:
chmod +x coklu_arguman.sh
./coklu_arguman.sh 6 9
# Çıktı: 9 değeri 6 değerinden büyüktür veya eşittir.

3. getopts ile Gelişmiş Parametre Yönetimi (Flags)
Büyük projelerde veya prod ortamı betiklerinde argümanların sırasını akılda tutmak zordur. Bunun yerine -n (isim), -s (soyisim) gibi kurumsal standartlarda bayraklar (flags) kullanılır. Bash'in yerleşik getopts aracı bu bayrakları while ve case döngüleriyle ayıklar.

cat << 'EOF' | tee args_with_getopts.sh
#!/bin/bash

# Kullanıcıya parametre rehberi sunan fonksiyon
show_help() {
  cat << HELP_EOF
Kullanım Kılavuzu:
-n  Kullanıcının adı (String)
-s  Kullanıcının soyadı (String)
-h  Yardım menüsünü gösterir
HELP_EOF
}

# 'n:' ve 's:' ifadelerindeki iki nokta üst üste (:), o bayrağın bir değer almak zorunda olduğunu belirtir.
while getopts 'n:s:h' options
  do
    case $options in
      n ) echo "Adı: ${OPTARG}"
        ;;
      s ) echo "Soyadı: ${OPTARG}"
        ;;
      h ) show_help
          exit 0
        ;;
      * ) echo "Geçersiz parametre!"
          show_help
          exit 1
        ;;
    esac
  done
exit 0
EOF

Çalıştırma ve Test Senaryoları:
chmod +x args_with_getopts.sh
A. Yardım Menüsü Testi:
./args_with_getopts.sh -h

B. Parametre Girişi Testi:
./args_with_getopts.sh -n Mad -s Max
Çıktı
Adı: Mad
Soyadı: Max

4. getopts Sınırları ve Mimari Tavsiye (Best Practice)
Kısa Opsiyon Sınırı: Yerleşik getopts komutu yalnızca tek karakterli kısa opsiyonları (-n, -s) parse edebilir; --name veya --surname gibi uzun opsiyonları (long options) desteklemez.

Harici getopt Komutu: Sistemlerde bulunan harici getopt aracı ise POSIX standardı değildir; boşluk içeren ("Mad Max") veya boş bırakılan argümanlarda kararsız çalışarak güvenlik açıkları oluşturabilir (GNU getopt hariç).

Profesyonel Yaklaşım:
Gerçek dünya senaryolarında hem kısa hem uzun parametreleri güvenli ve taşınabilir (portable) şekilde yönetmenin en temiz yolu, harici kütüphanelere güvenmek yerine argümanları bir while döngüsü ve shift komutu kullanarak el yordamıyla (custom parsing) ayıklamaktır.

   echo "$NUMBER bir TEK sayıdır."
fi
EOF
