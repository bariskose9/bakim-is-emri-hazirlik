# 12 — Çalıştırma, İzleme, Büyütme ve Bakım

## Çalışma zamanı
- Uygulama durumsuzdur; kalıcı veri yalnızca veritabanı ve blob depolamadadır.
- Sağlık ucu: `GET /api/health` → uygulama + veritabanı durumunu döner.
- Zaman aşımı ve yeniden deneme: dış API çağrılarında timeout zorunlu,
  yeniden deneme üstel geri çekilme ile ve en fazla 2 kez.
- Dış servis çökerse uygulama çökmez: widget hata durumunu gösterir, sayfa ayakta kalır.

## Loglama

**Kütüphane:** ayrı backend varsa `nestjs-pino`, Next tek başınaysa `pino`.
Seçim gerekçesi: JSON'u varsayılan olarak üretir ve kurumsal log toplama
sistemleri (Loki, ELK) **düz metin toplayamaz, JSON toplar.** Kurum projesinde
log biçimi senin tercihin değil, DevOps'un altyapısıyla **sözleşmendir**.

### ⛔ İki tür kayıt karıştırılmaz

| | **Uygulama logu** | **Denetim kaydı (audit log)** |
|---|---|---|
| Ne yazar | "İstek geldi, 45 ms sürdü, 200 döndü" | "Ali, 14:32'de 1042 no'lu kaydı kapattı" |
| Kime lazım | Geliştirici / DevOps | **İş birimi, denetim, hukuk** |
| Nerede durur | stdout → toplama sistemi | **Veritabanında tablo** |
| Ömrü | Günler–haftalar | Yıllar (mevzuat) |
| Nasıl görülür | Log arama ekranı | **Uygulama içinde ekran** |

⛔ **Denetim kaydını uygulama loguna yazmak bir tasarım hatasıdır:** log'lar
döner ve silinir; "bu kaydı kim değiştirdi" sorusu 2 yıl sonra sorulur.
Denetim kaydı **veriyi değiştiren işlemle aynı transaction içinde** tabloya
yazılır — yarısı yazılıp yarısı yazılmasın diye.

- Yapılandırılmış (JSON) log. `console.log` ile hata ayıklama çıktısı bırakılmaz.
- Her log satırında: zaman, seviye, istek kimliği, kullanıcı kimliği (mümkünse anonim), olay.
- **Log'a asla:** şifre, token, kart numarası, TCKN, e-posta gövdesi.
- Seviyeler: `error` (müdahale gerekir) · `warn` (beklenmedik ama tolere edilebilir) ·
  `info` (iş olayı: sipariş oluştu) · `debug` (sadece local).

### ⛔ `no-console` İSTİSNASIZ yasaklanır — tek kapı logger'dır

`no-console` kuralına `warn` ve `error` için istisna tanımak, kuralı geçersiz
kılar. Bir projede ölçüldü: istisna açıkken **35 ayrı düz metin `console.error`**
birikmişti. O satırların hiçbiri JSON değildi, kişisel veri süzgecinden
geçmiyordu ve hata takip aracına ulaşmıyordu.

Kural: `"no-console": "error"` (izin listesi YOK). Muafiyet yalnızca üç yere:
**log katmanının kendisine**, komut satırı betiklerine (tohumlama gibi) ve
`console`'u yalnızca casuslamak için anan testlere.

### ⛔ Kişisel veri süzgeci alan adına DEĞİL, DEĞERİN BİÇİMİNE de bakar

Yalnızca alan adına bakan bir süzgeç (`password`, `email` …) yetmez.
**ORM'ler ve doğrulama kütüphaneleri, hata mesajının İÇİNE argüman nesnesinin
tamamını düz yazı olarak koyuyor** — yani değerler bir alan adının arkasında
değil, cümlenin ortasında duruyor.

İki bağımsız savunma zorunlu:
1. **Alan adına göre** — yapılandırılmış bağlamı kapatır
2. **Değerin biçimine göre** — serbest metnin içindeki kimlik numarası, kart
   numarası, telefon, e-posta ve jetonu kapatır

⚠️ Rakam desenlerinde `\b` GÜVENİLMEZ: iki rakam arasında sınır görmediği için
uzun bir sayının ortasından kesip yanlış eşleşme üretir. Lookaround kullan
(`(?<!\d)` / `(?!\d)`).

⛔ **Süzgeci hata takip olayının TAMAMINA uygulama.** Derinlik ve uzunluk
sınırları yığın izini yok eder; hatayı bulunamaz hâle getiren bir gizlilik
önlemi, gözlemlenebilirliği kazandırmaz, kaybettirir. Yalnızca kişisel veri
taşıyabilen alanları hedefle ve yığın izinin bozulmadığını bir testle koru.

### Süzgeç TEK yerde yaşar
Sunucu log'u ve hata takip aracı **aynı** süzgeç fonksiyonundan geçer. İki ayrı
kopya olsaydı biri güncellenir, diğeri geride kalırdı — ve geride kalan taraf,
veriyi üçüncü bir servise gönderen taraf olurdu.

## İzleme (observability)
- Hata takibi: Sentry (ücretsiz katman) — üretimdeki her istisna yakalanır.
- ⛔ **YAKALANMIŞ hatalar da iletilir.** Hata takip SDK'ları kendiliğinden
  yalnızca **yakalanmamış** istisnaları görür. Oysa en önemli arızaların çoğu
  bilinçli olarak yakalanır (planlı görevin düşmesi, e-posta gönderilememesi,
  dış servisin cevap vermemesi). Log katmanının `error`/`warn` çağrıları bir
  kanal üzerinden hata takibine de iletilmezse, tam da izlenmek istenen şeyler
  görünmez kalır.
- ⛔ **Gözlemlenebilirlik katmanı, gözlediği uygulamayı DÜŞÜREMEZ.** Log ve hata
  takibi dosyaları, açılışta fırlatan bir yapılandırma doğrulayıcısını içe
  aktarmaz; ortam değişkenini doğrudan okur. Yapılandırma hatasını raporlaması
  beklenen modülün o hata yüzünden açılamaması, sessiz bir körlük üretir.
- ⛔ **Hata takibi olayları kendi alan adımızdan geçirilir** (tünel/proxy).
  İki kazanç: güvenlik başlığına (CSP) üçüncü bir dış kaynak eklemek gerekmez
  ve reklam engelleyiciler olayları düşüremez. **Engellenen bir hata takibi,
  hiç kurulmamış bir hata takibiyle aynı şeydir.**
- Performans: Vercel Analytics — Core Web Vitals izlenir (LCP < 2.5s, INP < 200ms, CLS < 0.1).
- Çalışma süresi izleme: sağlık ucuna dışarıdan periyodik ping.
- Uyarı eşikleri tanımlıdır: hata oranı %1'i geçerse, yanıt süresi 2 katına çıkarsa haber ver.

## Yedekleme ve kurtarma
- Veritabanı otomatik günlük yedek (Neon point-in-time recovery).
- Yedekten geri dönüş **denenmiş** olmalı; denenmemiş yedek yedek değildir.
- Blob depolamadaki kullanıcı dosyaları da yedek kapsamındadır.

## Performans ve büyütme sırası
Büyütme kararı **ölçümle** verilir, tahminle değil. Sıra:
1. **Sorgu ve index düzeltmesi** (kazancın çoğu burada)
2. **Önbellek**: dış API yanıtları, nadiren değişen listeler (ISR / route cache)
3. **CDN**: statik içerik ve görseller (Next.js Image ile otomatik boyutlandırma)
4. **Bağlantı havuzu**: sunucusuz ortamda Prisma için pooler kullan
5. **Yatay ölçekleme**: Vercel otomatik; kendi sunucunda kopya sayısı artırılır.
   Uygulama **durumsuz** olduğu sürece sorun çıkmaz — aşağıdaki kurala bak
6. **Kuyruk**: e-posta, bildirim, rapor gibi işler istek döngüsünden çıkarılır
7. **Okuma replikası / veri bölme**: ancak gerçekten gerekirse, ADR ile

Erken optimizasyon yapılmaz. Önce ölç, sonra düzelt, sonra tekrar ölç.

### Ayrı backend varsa: bağımsız ölçeklenebilirlik BAŞTAN kurulur

Web, API ve worker **ayrı ayrı kopyalanabilmelidir.** Bu sonradan eklenen bir
özellik değil, baştan korunan bir kısıttır — bozulursa fark edilmez, yük
geldiğinde anlaşılır.

Neden önemli: yük her katmana eşit binmez. Sabah mesai başında yük **API'ye**
biner (kimlik doğrulama, sorgu), web tarafı yalnızca hazır sayfa gönderir.
Ayrıysa API 6 kopyaya çıkar, web 1 kalır. Tek parçaysa gereksiz olan da 6 kez
çalışır ve 6 kat kaynak ödenir.

**Bunu mümkün kılan üç kural — ihlali ölçeklemeyi sessizce kırar:**

1. **Süreçler durumsuzdur.** Oturum, sayaç, kilit, geçici dosya süreç belleğinde
   tutulmaz — ikinci kopya açıldığında o veriyi göremez. Paylaşılan durum
   Postgres veya Redis'te yaşar.
2. **Worker ayrı süreçtir**, API'nin içinde `setInterval` ile çalışmaz. Aksi
   halde API'yi 6 kopyaya çıkardığında zamanlanmış iş **6 kez** çalışır.
3. **Zamanlanmış işler tekil çalışacak şekilde korunur** (kuyruk motorunun
   kilidi veya benzersiz iş anahtarı). Kopya sayısı arttığında mükerrer işlem
   ve mükerrer bildirim üretilmemelidir.

⚠️ **Test:** her servisten **iki kopya** çalıştırıp uygulamayı bir kez baştan
sona kullan. Oturum düşüyorsa, sayaç sıfırlanıyorsa veya bildirim iki kez
geliyorsa durumsuzluk kuralı çiğnenmiş demektir.

## Bakım
- Haftalık: hata panosu gözden geçirilir, `npm audit` çalıştırılır.
- Aylık: bağımlılık güncellemeleri ayrı PR ile; major yükseltmeler tek tek.
- Sürekli: teknik borç `docs/project/roadmap.md` içinde açıkça listelenir, gizlenmez.
- Kullanılmayan özellik, tablo ve bağımlılık silinir (bkz. deprecation süreci).

## Olay (incident) müdahalesi
1. Durdur: gerekiyorsa önceki dağıtıma dön (rollback)
2. Etkiyi ölç: kaç kullanıcı, hangi akış
3. Düzelt: önce hatayı yakalayan test, sonra düzeltme
4. Yaz: `docs/project/decisions/` altında kısa olay notu — ne oldu, neden, tekrarı nasıl önlenir
Suçlu aranmaz, süreç düzeltilir.

## Hizmet hedefleri (SLO)
Ölçülebilir hedef yoksa "yavaş" tartışması bitmez. Bu proje için:
- Kullanılabilirlik: aylık %99
- API p95 yanıt süresi: < 500ms
- Hata oranı: < %1
Hedef aşılırsa performans işi, yeni özellik işinin önüne geçer.

## Planlı görevler (cron)
- Süresi dolan koltuk rezervasyonlarını serbest bırakma
- Süresi dolan verileri temizleme (saklama politikası)
- Üyelik bitiş hatırlatması
Her planlı görev **idempotent** olur: iki kez çalışırsa veri bozulmaz.
Çalıştığı ve sonucu loglanır; sessizce başarısız olmasına izin verilmez.

## Bakım penceresi ve duyuru
Kesinti gerektiren işlem varsa kullanıcıya önceden bildirilir ve
bakım sayfası gösterilir; boş beyaz ekran bırakılmaz.
