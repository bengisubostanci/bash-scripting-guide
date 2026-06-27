# Bash Scripting'de `while` Döngüleri ve Akış Yönetimi

`while` döngüleri, belirli bir koşul doğru (`true`) olduğu sürece komut bloklarını tekrarlamak için kullanılır. İndis tabanlı klasik döngülerin yanı sıra Bash'te `while`, bir dosyanın veya komut çıktısının içeriğini satır satır, bellek dostu (stream) bir şekilde okumak için en profesyonel yöntemdir.

---

## 1. Temel Sayısal `while` Döngüsü

Belirli bir sınıra kadar dinamik olarak artan veya azalan döngüler için matematiksel çift parantez `(( ))` yapısı kullanılır.

Aşağıdaki script, dışarıdan başlangıç değeri ve sınır değerini parametre olarak alarak çalışır:

`bash
cat << 'EOF' | tee while_temel.sh
#!/bin/bash

# Dışarıdan gelen argümanları değişkenlere atıyoruz
CURRENT_VAL=$1
BORDER_VAL=$2

echo "[BİLGİ] Başlangıç Değeri: $CURRENT_VAL"
echo "[BİLGİ] Sınır Değeri: $BORDER_VAL"

# Koşul doğru olduğu sürece döngü çalışmaya devam eder
while (( CURRENT_VAL < BORDER_VAL ))
do
  echo "Mevcut Değer: $CURRENT_VAL"
  
  # Değeri 1 artırıyoruz

  Betiği Yayına Alma ve Test:
  chmod +x while_temel.sh
./while_temel.sh 1 5


2. Girdi Yönlendirmesi (<) ile Dosya Okuma ve Ağ Kontrolü (Ping)
Veri mühendisliği veya sistem yönetiminde, bir text dosyasındaki listeyi (örneğin sunucu adresleri, DNS listeleri) okuyup toplu işlem yapmak çok yaygındır. while read bloğunun sonuna eklenen < dosya.txt ifadesi, dosya içeriğini döngüye girdi (input stream) olarak besler.

Canlı Senaryo: Toplu DNS Sağlık Kontrolü
Öncelikle hedef adres listemizi oluşturalım:
cat << 'EOF' | tee dns.txt
8.8.8.8
8.8.4.4
1.1.1.1
EOF

Şimdi bu dosyayı satır satır okuyarak her adrese 3 adet ICMP paketi (ping) gönderen scriptimizi inşa edelim:

cat << 'EOF' | tee toplu_ping.sh
#!/bin/bash

TARGET_FILE="dns.txt"

# Güvenlik Kontrolü: Hedef dosya mevcut mu?
if [ ! -f "$TARGET_FILE" ]; then
    echo "[HATA] Okunacak adres listesi ($TARGET_FILE) bulunamadı!"
    exit 1
fi

echo "--- Ağ Sağlık Kontrolü Başlatıldı ---"

# 'read -r' kullanarak ters eğik çizgi (\) kaçış karakterlerinin bozulmasını önlüyoruz
while read -r IP_ADDRESS
do
    echo "--------------------------------------------------"
    echo "[TEST] Hedef Adres: $IP_ADDRESS ping leniyor..."
    echo "--------------------------------------------------"
    
    # -c 3 bayrağı 3 paket gönderir
    ping -c 3 "$IP_ADDRESS"
    
done < "$TARGET_FILE"

echo "--- İşlem Tamamlandı ---"
EOF

Çalıştırma ve Test:
chmod +x toplu_ping.sh
./toplu_ping.sh

3. En Temel ve Hızlı Dosya Yazdırma Kalıbı
Sadece bir dosyanın içeriğini ekrana basmak veya satırları modifiye etmek istediğinizde kullanabileceğiniz en temiz while kalıbı şudur:

# 'peptides.txt' dosyasını satır satır okuyup ekrana yazar
while read -r line; do
  echo "Satır İçeriği: $line"
done < peptides.txt

💡 Neden for Yerine while read Tercih Edilmeli? (Mühendislik Notu)
Büyük boyutlu log dosyalarını veya CSV veri setlerini işlerken for line in $(cat dosya.txt) kalıbını kullanmak tehlikelidir. Çünkü cat tüm dosyayı aynı anda RAM'e yüklemeye çalışır ve dosya çok büyükse sistemin kilitlenmesine (Out of Memory) yol açar.

Oysa while read yapısı, dosyayı diskten satır satır (stream) akış halinde okur. RAM'de sadece o an okunan tek bir satır yer kaplar, bu sayede gigabaytlarca büyüklükteki veri setleri bile minimum kaynak tüketimiyle güvenle işlenebilir.

  CURRENT_VAL=$(( CURRENT_VAL + 1 ))
done
EOF
