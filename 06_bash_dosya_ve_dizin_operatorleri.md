# Bash Scripting'de Dosya ve Dizin Test Operatörleri

Veri mühendisliği süreçlerinde (ETL), log yönetiminde veya yedekleme otomasyonlarında ham veriyi işleme almadan önce dosyanın varlığını, tipini (dizin, sembolik link vb.) ve izinlerini (okuma, yazma, çalıştırma) kontrol etmek sistem sağlığı açısından kritik bir adımdır.

---

## 1. Dosya Koşul İfadeleri (File Expressions) Referans Tablosu

Aşağıdaki operatörler, `if [ -bayrak dosya ]` veya `if [[ -bayrak dosya ]]` kalıpları içinde kullanıldığında geriye mantıksal bir sonuç döner.

| Operatör | Koşulun Doğru (`true`) Olma Durumu | Kullanım Amacı |
| :--- | :--- | :--- |
| **`-e file`** | Dosya veya dizin sistemde **mevcutsa** | Genel varlık kontrolü |
| **`-f file`** | Dosya mevcutsa ve **normal bir dosyaysa** (dizin veya cihaz değilse) | Veri/Metin dosyası kontrolü |
| **`-d file`** | Dosya mevcutsa ve bir **dizin (klasör) ise** | Hedef klasör kontrolü |
| **`-s file`** | Dosya mevcutsa ve **boyutu sıfırdan büyükse** (boş değilse) | Log/Veri doluluk kontrolü |
| **`-L file`** | Dosya mevcutsa ve bir **sembolik link (kısayol) ise** | Bağlantı kontrolü |
| **`-r file`** | Dosya mevcutsa ve mevcut kullanıcı tarafından **okunabilirse** | İzin kontrolü |
| **`-w file`** | Dosya mevcutsa ve mevcut kullanıcı tarafından **yazılabilirse** | İzin kontrolü |
| **`-x file`** | Dosya mevcutsa ve mevcut kullanıcı tarafından **çalıştırılabilirse** | İzin kontrolü |
| **`-p file`** | Dosya mevcutsa ve bir **named pipe (FIFO)** ise | Süreçler arası iletişim |
| **`-S file`** | Dosya mevcutsa ve bir **network socket** ise | Ağ servis kontrolü |
| **`f1 -nt f2`** | `file1`, `file2` dosyasından **daha yeniyse** (Modifikasyon tarihi) | Güncellik kontrolü |
| **`f1 -ot f2`** | `file1`, `file2` dosyasından **daha eskiyse** | Arşiv/Eski veri kontrolü |
| **`f1 -ef f2`** | İki dosya da **aynı inode numarasına** sahipse (Hard link ise) | Kopya/Bağlantı doğrulaması |

---

## 2. Pratik Uygulamalar ve Örnek Senaryolar

### Örnek 1: Dosya Varlık ve İzin Kontrolü (Otomatik Yetkilendirme)
Bu betik, dışarıdan argüman olarak aldığı dosyanın var olup olmadığını ve çalıştırılabilir olup olmadığını denetler. Eğer dosya yoksa veya yetkisi eksikse gerekli aksiyonları alır.

bash
cat << 'EOF' | tee file_operators.sh
#!/bin/bash

FILE=$1

# Güvenli yaklaşım: Önce dosyanın varlığını (-e), ardından yetkisini (-x) kontrol ediyoruz.
if [ -e "$FILE" ] && [ -x "$FILE" ]; then
   echo "[BAŞARILI] $FILE dosyası zaten mevcut ve çalıştırılabilir durumda."
else
   echo "[BİLGİ] $FILE ayarlanıyor..."
   touch "$FILE"
   chmod +x "$FILE"
   echo "[BAŞARILI] $FILE oluşturuldu ve çalıştırma izni (+x) tanımlandı."
fi
EOF

Çalıştırma ve Test adımları:
chmod +x file_operators.sh

# İlk Çalıştırma (Dosya yokken)
./file_operators.sh betigim.sh
# Çıktı: [BAŞARILI] betigim.sh oluşturuldu ve çalıştırma izni (+x) tanımlandı.

# İkinci Çalıştırma (Dosya artık mevcut ve yetkili)
./file_operators.sh betigim.sh
# Çıktı: [BAŞARILI] betigim.sh dosyası zaten mevcut ve çalıştırılabilir durumda.

Örnek 2: Dinamik Betik Oluşturma ve İç İçe Tetikleme
Bu betik, argüman olarak verilen isimde yeni bir alt Bash scripti oluşturur, içerisine dinamik kod enjekte eder ve ardından otomatik olarak çalıştırır.
cat << 'EOF' | tee file_operators2.sh
#!/bin/bash

TARGET_FILE=$1

if [ -e "$TARGET_FILE" ]; then
   echo "[UYARI] $TARGET_FILE dosyası sistemde zaten mevcut. İşlem atlandı."
else
   echo "[BİLGİ] $TARGET_FILE bulunamadı. Yeni dosya inşa ediliyor..."
   
   # Dinamik alt betik içeriği oluşturuluyor
   echo "#!/bin/bash" > "$TARGET_FILE"
   echo "echo \"Merhaba, ben \$0 betiğiyim ve $0 tarafından dinamik olarak üretildim.\"" >> "$TARGET_FILE"
   
   # Yetkilendirme ve çalıştırma
   chmod +x "$TARGET_FILE"
   ./"$TARGET_FILE"
fi
EOF

Çalıştırma ve Test adımları:
chmod +x file_operators2.sh

# İlk Çalıştırma
./file_operators2.sh dinamik_test.sh
Çıktı
[BİLGİ] dinamik_test.sh bulunamadı. Yeni dosya inşa ediliyor...
Merhaba, ben ./dinamik_test.sh betiğiyim ve ./file_operators2.sh tarafından dinamik olarak üretildim.

# İkinci Çalıştırma (Güvenlik bariyeri devreye girer)
./file_operators2.sh dinamik_test.sh
# Çıktı: [UYARI] dinamik_test.sh dosyası sistemde zaten mevcut. İşlem atlandı.
