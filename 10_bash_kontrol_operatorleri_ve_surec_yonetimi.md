# Bash Scripting'de Kontrol Operatörleri ve Süreç Yönetimi

Linux terminalinde veya otomasyon betiklerinde birden fazla komutu belirli sembollerle (`|`, `&`, `&&`, `||`, `;`) birbirine bağlarız. Bash kabuğunda bunlara **Kontrol Operatörleri (Control Operators)** denir. Bu operatörler, komutların hangi sıra dizisiyle, hangi mantıksal şartla veya hangi işlem modunda (ön plan/arka plan) çalışacağını belirler.

Bash tarafından tanınan temel kontrol operatörleri şunlardır:
`&` , `&&` , `(` , `)` , `;` , `;;` , `\n (yeni satır)` , `|` , `||`

---

## 1. Sıralı Çalıştırma Operatörü ( `;` ) — "Sorgusuz Sürdür"

Noktalı virgül (`;`), kendisinden önceki komutun başarı durumuna (Exit Status) bakmaksızın, sıradaki komutun çalıştırılmasını sağlar. Komutlar tamamen bağımsızdır.

bash
# Mantıksal olarak 3 < 2 ifadesi '0' (yanlış) üretse bile sonraki komutlar kesintisiz çalışır
echo $((3 < 2)) ; echo "İlk komutun sonucundan bağımsız olarak çalıştım." ; echo $((2 > 1))

2. Arka Plan Operatörü ( & ) — "Asenkron Çalışma"
Bir komutun veya döngünün sonuna & işareti konulduğunda, Bash o işlemi arka plana (background) fırlatır. Böylece terminal kilitlenmez ve kullanıcı ön planda (foreground) yeni komutlar çalıştırmaya devam edebilir.

Canlı Senaryo: Uzun Süren Bir Döngüyü Arka Plana Atmak
Aşağıdaki blok, 20 saniye süren ağır bir işlemi arka plana atarak terminal akışını nasıl özgür bıraktığımızı gösterir:
# 20 saniyelik döngü arka plana (bg) atılır ve çıktı bir dosyaya yönlendirilir
for i in {1..20}; do sleep 1; echo $i; done > for_20_sleep.txt & 

# Bu komut döngünün bitmesini beklemeden ANINDA çalışır:
echo "Döngünün arka planda bitmesini beklemeden bu satıra geçebildim!"

Terminal Çıktı Analizi:
[1] 26134  <-- [İş Numarası] ve Süreç ID'si (PID)
Döngünün arka planda bitmesini beklemeden bu satıra geçebildim!

20 saniye sonra dosya kontrol edildiğinde verilerin başarıyla yazıldığı ve terminale [1]+ Done şeklinde sürecin başarıyla tamamlandığı bilgisi düşer.

3. Mantıksal VE Operatörü ( && ) — "Başarılıysa Devam Et"
&& operatörü, kendisinden sonraki komutu yalnızca kendisinden önceki komut başarıyla (Exit Code = 0) tamamlandıysa çalıştırır.

Mantıksal Karşılığı (Pseudo-code):
if komut1; then
   komut2
fi

Canlı Senaryo: Servis Durumuna Bağlı Entegrasyon Kontrolü
Aşağıdaki örnekte, Jenkins servisi kapalıyken && sağındaki mesaj basılmaz. Servis başlatıldıktan sonra ise bağlantı başarılı olacağı için mesaj ekrana yazdırılır:

# Port kapalıyken test (Hata döneceği için sağ taraf çalışmaz)
curl http://localhost:8080 && echo "Bağlantı başarılı, Jenkins ayakta!"

# Servisi başlatalım
sudo systemctl start jenkins

# Servis açıldıktan sonra tekrar test (Başarılı çıktı ve sağ taraf tetiklenir)
curl http://localhost:8080 && echo "Bağlantı başarılı, Jenkins ayakta!"

4. Mantıksal VEYA Operatörü ( || ) — "Başarısızsa Devam Et"
|| operatörü, kendisinden sonraki komutu yalnızca kendisinden önceki komut başarısızlıkla (Exit Code != 0) sonuçlandıysa çalıştırır. Genellikle hata yakalama (error handling) ve fallback mekanizmalarında tercih edilir.

Mantıksal Karşılığı (Pseudo-code):

if komut1; then
   true
else
   komut2
fi
Örnek Kullanım:
# Bağlantı başarısız olursa hata mesajını devreye sok
curl --fail http://localhost:9999 || echo "[HATA] Sunucuya erişilemedi, acil durum senaryosu devrede!"

# Matematiksel kontrol başarısız olursa (Yanlışsa) sağ taraf çalışır
((3 < 2)) || echo "İlk koşul yanlış olduğu için ben çalıştım!"

5. Değilleme Operatörü ( ! ) — "Mantıksal Tersi"
Bir komutun veya koşulun başına eklenen ! işareti, dönen çıkış kodunu tersine çevirir. Komut 0 (başarılı) döndüyse bunu 1 (başarısız) yapar; ya da tam tersi.

# Normalde 'test -d klasor' klasör varsa 0 döner. 
# Başındaki '!' sayesinde klasör YOKSA if bloğu tetiklenir.
if ! [ -d "/tmp/data" ]; then
    echo "Veri dizini bulunamadı, oluşturuluyor..."
    mkdir -p /tmp/data
fi

6. Boru Hattı Operatörü ( | ) — "Pipeline"
| (Pipe) operatörü, soldaki komutun standart çıktısını (stdout), sağdaki komuta doğrudan standart girdi (stdin) olarak besler. Veri mühendisliğinde akışları (stream) filtrelemek ve işlemek için en sık kullanılan operatördür.

# Sistemdeki süreçleri listele, içinden sadece 'python' geçenleri filtrele
ps aux | grep python
Operatör,İsmi,Çalışma Şartı,Çalışma Modu
;,Sıralı Çalıştırma,Önceki komutun sonucuna bakmaksızın çalışır,Ön Plan (Foreground)
&,Arka Plan,Komutu arka plana atarak terminali anında serbest bırakır,Arka Plan (Background)
&&,Mantıksal VE,Yalnızca önceki komut başarılıysa (Exit 0) çalışır,Ön Plan (Foreground)
**`,,`**,Mantıksal VEYA
**`,`**,Boru Hattı (Pipe),Soldaki çıktıyı sağdaki komuta girdi olarak aktarır


