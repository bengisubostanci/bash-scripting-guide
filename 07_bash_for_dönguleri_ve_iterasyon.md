# Bash Scripting'de `for` Döngüleri ve İterasyon Yönetimi

Veri setleri üzerinde dönmek, bir dizindeki dosyaları toplu işlemek (batch processing) veya belirli sayıda komutu tekrarlatmak için `for` döngüleri kullanılır. Bash, hem geleneksel C tipi döngü mimarisini hem de modern liste tabanlı iterasyonları destekler.

---

## 1. Geleneksel C Tipi `for` Döngüsü

Belirli bir başlangıç noktasından bitiş noktasına kadar adım adım ilerleyen döngüler için çift parantez `(( ))` yapısı kullanılır.

bash
cat << 'EOF' | tee for_dongusu.sh
#!/bin/bash

# 0'dan başla, 10'dan küçük olduğu sürece 1'er artır
for (( i=0; i<10; i++ ))
do
    echo "İndis Değeri: $i"
done
EOF

Betiği Yayına Alma ve Test:
chmod +x for_dongusu.sh
./for_dongusu.sh

2. Tek Satırda (Inline) for Döngüsü ve Menzil (Range) Kullanımı
Terminal üzerinde hızlıca otomasyon çalıştırmak veya ardışık sunucu kontrolleri yapmak için döngüyü tek satıra indirebiliriz. Küme parantezi {başlangıç..bitiş} ifadesi otomatik bir menzil üretir.

# 1'den 10'a kadar dönerek her adımda sistemin tam domain adını (FQDN) basar
for i in {1..10}; do hostname -f; done


3. Dizin İçindeki Dosyalar Üzerinde Dönmek (Globbing)
Bir dizindeki dosyaları tararken $(pwd)/* veya $(ls) kullanmak yaygın bir hatadır. Dosya isimlerinde boşluk karakteri (Örn: yeni veri.csv) varsa bu komutlar kırılır. Güvenli ve profesyonel yöntem doğrudan Globbing (Yıldız * operatörü) kullanmaktır.

cat << 'EOF' | tee dizin_tarama.sh
#!/bin/bash

# Bulunduğumuz dizindeki tüm dosya ve klasörleri güvenli şekilde tarar
for file in ./*;
do
    # Sadece normal dosyaları ekrana bas (Dizinleri filtrele)
    if [ -f "$file" ]; then
        echo "Dosya bulundu: $file"
    fi
done
EOF
Çalıştırma ve Test:
chmod +x dizin_tarama.sh
./dizin_tarama.sh

⚠️ Neden for i in $(ls) Kullanılmamalıdır?
Eğer bir dosyanın adı veri raporu.csv ise, ls çıktısı bu ismi iki ayrı dosya (veri ve raporu.csv) gibi algılar ve döngü hata üretir. Doğrudan for file in ./* kullanımı ise boşlukları kusursuz yönetir.

4. Dosya İçeriğini Satır Satır Okumak (İterasyon)
Büyük veri dosyalarını (Örn: CSV, Log) satır satır işlemek için for i in $(cat dosya) yöntemi kullanılmamalıdır. Çünkü cat komutu satırları değil, boşlukla ayrılan her kelimeyi döngüye sokar.

Bunun yerine kurumsal standart olan while read -r mekanizması tercih edilir.

Profesyonel ve Güvenli Satır Okuma Yöntemi:
cat << 'EOF' | tee dosya_okuma.sh
#!/bin/bash

TARGET_FILE="$HOME/datasets/insanlar.csv"

# Önce dosyanın varlığını kontrol ediyoruz
if [ ! -f "$TARGET_FILE" ]; then
    echo "Hata: Hedef veri seti ($TARGET_FILE) bulunamadı!"
    exit 1
fi

echo "--- Veri Seti İşleniyor ---"

# Satır satır okuma için en güvenli yaklaşım (while - stream)
while IFS= read -r line
do
    echo "İşlenen Satır: $line"
done < "$TARGET_FILE"

EOF

Çalıştırma ve Test:
chmod +x dosya_okuma.sh
./dosya_okuma.sh

Senaryo,Önerilen Yöntem,Avantajı
Belirli sayıda dönmek,for (( i=0; i<N; i++ )),Matematiksel işlemlerde en hızlı yöntemdir.
Belirli bir sayı/harf menzili,for i in {1..10},"Kod kalabalığını önler, okunabilirliği yüksektir."
Dizin içeriğini listelemek,for file in ./*,Boşluk içeren dosya isimlerinde asla kırılmaz.
Dosya satırlarını okumak,while read -r line,"Büyük dosyalarda RAM'i şişirmez, performanslı ve güvenlidir."
