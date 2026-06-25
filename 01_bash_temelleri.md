# Bash Scripting ve Kabuk Programlamaya Giriş

Bu dokümantasyon, Linux/Unix sistemlerin kalbi olan Bash (Born Again Shell) kabuğunda betik (script) yazmanın temellerini, kabuk mekanizmalarını ve otomasyon süreçlerinde neden kritik bir rol oynadığını incelemek amacıyla hazırlanmıştır.

---

## Neden Bash Scripting Kullanmalıyız?

Modern veri mühendisliği, DevOps ve sistem yönetimi süreçlerinde tekrarlayan görevlerin otomatikleştirilmesi verimliliğin anahtarıdır. Bash betikleri bize şu avantajları sağlar:

* **Otomasyon:** Manuel olarak yapılan sunucu yapılandırmalarını, yedekleme işlemlerini ve veri taşıma (ETL) adımlarını tek bir komutla hatasız çalıştırır.
* **Hız ve Hafiflik:** Ekstra bir derleyiciye veya ağır çalışma zamanı (runtime) ortamlarına ihtiyaç duymadan, doğrudan işletim sisteminin çekirdeği ile konuşur.
* **Entegrasyon Gücü:** CLI (Komut Satırı Arayüzü) araçlarını, Docker konteynerlerini ve bulut servislerini (AWS CLI vb.) bir orkestra şefi gibi bir arada yönetebilir.

---

## İlk Bash Betiğini Oluşturmak ve Çalıştırmak

Bir bash betiği oluşturmanın en pratik yolu, terminal akışını doğrudan bir dosyaya yönlendirmektir. Aşağıdaki blok, `Simple Word (Heredoc)` yöntemiyle dinamik bir betik dosyası oluşturur:

bash
cat << 'EOF' | tee basit_betik.sh
#!/bin/bash  
echo "Merhaba, ben profesyonel bir Bash betiğiyim!"
EOF

1. Çalıştırma İzni Vermek (Execution Permission)
Linux sistemlerde yeni oluşturulan dosyalar güvenlik gereği varsayılan olarak "çalıştırılabilir" (executable) modda gelmez. Dosyaya bu yetkiyi tanımlamamız gerekir:
# Dosyaya sadece gerekli çalıştırma iznini (+) ekliyoruz
chmod +x basit_betik.sh
(Not: Sistem genelinde kök kullanıcı yetkisi gerekmedikçe sudo chmod yerine doğrudan chmod kullanılması güvenlik açısından en iyi pratiktir.)

2. Betiği Tetiklemek
Eğer betiğin bulunduğu dizindeysek, işletim sistemine dosyanın konumunu tam olarak belirtmek için ./ ifadesini kullanırız:
./basit_betik.sh
# Çıktı: Merhaba, ben profesyonel bir Bash betiğiyim!

İpucu: Bulunduğunuz dizindeki bir scripti tetiklerken ./script.sh kullanabileceğiniz gibi, doğrudan kabuğu belirterek bash script.sh veya sh script.sh şeklinde de çalıştırabilirsiniz. Ancak ./ kullanımı, dosyanın başındaki "Shebang" satırına sadık kalınmasını sağlar.

Kabuk (Shell) Çeşitleri
Linux ekosisteminde farklı yeteneklere ve sözdizimlerine sahip birden fazla kabuk alternatifi bulunur. Sisteminizde kurulu ve kullanılabilir durumda olan kabukların listesini görmek için /etc/shells dosyasını inceleyebilirsiniz:
cat /etc/shells

Yaygın Çıktı Örneği:

/bin/sh: POSIX standartlarına bağlı, en eski ve en sade kabuk (Bourne Shell).

/bin/bash: Günümüz Linux dağıtımlarında varsayılan olan, sh üzerine geliştirilmiş gelişmiş kabuk.

/usr/bin/zsh: Özellikle macOS dünyasında popüler olan, kullanıcı dostu eklentilere sahip kabuk.

Shebang (#!) Nedir ve Neden Önemlidir?
Bir betik dosyasının en üst satırında yer alan #! karakter dizisine Shebang denir. İşletim sistemine, altındaki kod satırlarını hangi yorumlayıcı (interpreter) ile okuması gerektiğini söyler.

Sık karşılaşılan Shebang varyasyonları ve görevleri:

Shebang Kullanımı,Açıklama
#!/bin/bash,Dosyanın kesinlikle Bash kabuğu kullanılarak çalıştırılacağını belirtir.
#!/bin/sh,Dosyanın Bourne Shell veya onunla uyumlu (genelde daha kısıtlı) bir kabukla çalıştırılacağını söyler.
#!/usr/bin/env python3,"Dosyayı bir Python 3 betiği olarak çalıştırır. env kullanımı, Python'un sistemdeki kurulu olduğu tam yolu dinamik olarak bulmasını sağlar (En iyi pratik)."
#!/bin/false,Bu betiğin doğrudan çalıştırılmasını engeller. Tetiklendiğinde hiçbir şey yapmaz ve geriye başarısızlık (exit status 1) kodu döner. Güvenlik ve kütüphane dosyaları için tercih edilir.

Kullanıcının Varsayılan Kabuğunu Tespit Etmek
Sistemdeki spesifik bir kullanıcının (örneğin ana sunucu kullanıcısının) terminali açtığında hangi kabukla karşılandığını öğrenmek için /etc/passwd konfigürasyon dosyası filtrelenebilir:
# 'root' kullanıcısının varsayılan kabuğunu sorgulayalım
cat /etc/passwd | grep root
Çıktı Analizi:
root:x:0:0:root:/root:/bin/bash
Bu çıktının en sağındaki sütun (/bin/bash), kullanıcının sisteme login olduğunda doğrudan Bash kabuğunu kullandığını doğrular.
