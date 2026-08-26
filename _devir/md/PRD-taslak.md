# Bakım ve İş Emri Yönetim Sistemi — Ürün Gereksinim Dokümanı (PRD)

> **Bu bir TASLAKTIR.** Kurulum yapıldığında (`/yeni-proje`) bu dosya
> `docs/project/PRD.md` altına taşınır ve kitin şablon başlıklarıyla birebir
> hizalanır. Taslak burada duruyor çünkü kurulum henüz yapılmadı; plan bir
> makinede üretilip depo üzerinden diğerine geçiyor.
>
> ⛔ **Bu belge yalnızca "sistem NE yapacak" sorusuna bakar.** Mühendislik
> kararları (hangi framework, hangi ORM, hangi kuyruk) burada değil,
> `proje-teknoloji-ve-plan.md` içindedir.

**Son güncelleme:** 2026-08-26
**Kaynak:** `odev.md` (gerçeğin kaynağı `odev.docx`) · `proje-teknoloji-ve-plan.md`
**Durum:** Taslak — ⭐ ve ❓ satırları onay bekliyor

---

## 0. İşaretler — bu belge nasıl okunur

Her karar nereden geldiğine göre işaretli. ⛔ İşaretsiz cümle yok.

| İşaret | Anlamı | Sana ne demek |
|---|---|---|
| ✅ | **Ödevde yazıyor** | Tartışma yok, kaynak `odev.md` içinde. Okumana gerek yok |
| ⭐ | **Ben karar verdim** | Ödev boş bırakmış, ben doldurdum. Gerekçesi yanında. **Bak** |
| ⚠️ | **Varsayım** | Cevabı kurumdan gelmeli ama iş durmasın diye makul bir değer aldım. Cevap gelince düzeltilir |
| ❓ | **Senin cevabın şart** | Cevaplanmadan ilerlemek yanlış varsayımı katmanlara yayar. **Bak** |

⚠️ **Bir uyarı:** ⚠️ ile ❓ farkı önemli. ⚠️ olanlar yanlış çıksa **tek yerde**
düzelir (bir tablo satırı, bir sabit). ❓ olanlar yanlış çıksa **veri modeli veya
iş kuralı** değişir — geri dönüşü pahalıdır.

---

## 0b. ⭐ SABAH BAKILACAKLAR — kararlar ve sorular tek tabloda

### A) ⭐ Verdiğim kararlar

Her satır: **ne karar verdim** · **tek cümlelik gerekçe** · **hangi bölümde
ayrıntısı var**. Katılmadığın satırı söyle, o bölümü yeniden yazarım.

| # | Karar | Neden | Bölüm |
|---|---|---|---|
| ⭐1 | Talep ve iş emri **tek tablo**; talep, iş emrinin `TALEP` durumudur | Ödev §5.4 aynı 13 alanı "talep veya iş emri" diye ikisine birden veriyor; iki tabloda o alanlar tekrarlanır (§9 DRY) ve geçmiş ikiye bölünür | §5.4 |
| ⭐2 | SLA saati **talep açıldığı an** başlar | Rehber E.4'ün kendi tanımı: "sınıflandırma, bekleme ve kontrol de bu sürenin içindedir" — yavaş sınıflandırma SLA'yı yemeli | §5.6 |
| ⭐3 | Personel bilgileri tek `users` tablosunda; **ev adresi toplanmaz** | KVKK m.4/1-ç veri minimizasyonu: hiçbir iş akışı ev adresini kullanmıyor. Tabloyu ikiye bölmek kanunen bir şey kazandırmıyor, her sorguya join ekliyor | §5.1, §5.11 |
| ⭐4 | **Kapatma ve iptal yetkisi** teknik personelde değil, operasyon sorumlusu + yöneticide | Görevler ayrılığı (segregation of duties): işi yapanın kendi işini onaylaması kontrol boşluğudur | §3 |
| ⭐5 | **Her rol talep açabilir** (teknik personel ve yönetici dahil) | Sahada arızayı ilk gören çoğu zaman teknik personelin kendisi; ayrı bir "talep sahibi" rolüne hapsetmek gerçeğe aykırı | §3 |
| ⭐6 | İş emri türü **4 tane**; `KURULUM` ayrı SLA sınıfı almaz, planlı tarih politikasına bağlanır | Ödev §7 "Factory yalnızca göstermelik olmamalıdır" diyor — davranışı aynı olan 4. sınıf açmak sahte çeşitlilik olur | §5.4, §5.6 |
| ⭐7 | Planlı türlerde **planlanan tarih zorunlu**, arızada **yasak** | Planlı bakımın son tarihi "şimdi + 24 saat" değil, planlanan tarihtir; tarih yoksa politika hesap yapamaz | §5.4 |
| ⭐8 | SLA saati **"parça bekliyor" durumunda durur** (duraklatma) | Dış tedarikçiyi bekleyen süreden teknik personel sorumlu tutulamaz; ITIL'de "clock stop" yerleşik pratiktir. ⚠️ Maliyeti ve çıkarma yolu §5.6'da yazılı | §5.6 |
| ⭐9 | Varlık durumu: **kullanım dışı** ve **hurda** iş emrini engeller; **arızalı** ve **bakımda** engellemez | Arızalı varlık için iş emri açılamaması sistemi kilitler — arıza kaydı tam olarak o durumda açılır | §5.3 |
| ⭐10 | Durum geçmişi ve atama geçmişi **tek olay tablosunda** | İkisi de "kim, ne zaman, neyi değiştirdi" kaydı; ayrı tablolar detay ekranında birleştirme kodu doğurur ve yeni olay türü eklemek tablo açmayı gerektirir | §5.7 |
| ⭐11 | Yorumlar **düzenlenemez ve silinemez**; `dahili not` talep sahibine görünmez | Yorum bir denetim izidir; sonradan değiştirilebilen iz, iz sayılmaz | §5.7 |
| ⭐12 | Mükerrer bildirim **tek `dedupe_key` benzersiz index'i** ile engellenir | Uygulamada "var mı diye bak, yoksa ekle" iki iş aynı anda çalışınca kaybeder; yarışı veritabanı çözer | §5.8 |
| ⭐13 | İş emri numarası **tek dizi (sequence)**; talep evresinde de aynı numara taşınır | "Dönüşünce numara değişti" karmaşası olmasın; telefonda söylenen numara değişmemeli | §5.4 |
| ⭐14 | İş emri, varlığın lokasyonunu **kendi satırında da saklar** | Varlık başka binaya taşınırsa geçmiş iş emri yanlış lokasyonda görünmemeli — o iş o binada yapıldı | §5.4 |
| ⭐15 | Varlık türü **enum**, birim **serbest metin** — ikisi için tablo açılmıyor | Hiçbir iş kuralı bunlara dayanmıyor; ekranı olmayan tablo ödev §32'nin "gereksiz abstraction" maddesine girer | §5.3 |
| ⭐16 | Resmî tatiller **veritabanı tablosunda**, yarım gün desteğiyle | Yıl değişince kod değiştirip yeniden yayına çıkmak gerekmesin; tatil listesi veridir, kod değil | §5.6 |
| ⭐17 | Günlük özet **veritabanına yazılır** (log değil), `özet_tarihi` benzersiz | Ödev seçenek bırakıyor; veritabanı seçildi çünkü işin idempotentliği böylece veritabanı seviyesinde kanıtlanıyor ve pano geçmiş günleri gösterebiliyor | §5.9 |
| ⭐18 | SLA **ihlali** gecikmeli iş değil, **tarama** işiyle yakalanır | Sunucu kapalıyken geçen süreleri de yakalar; gecikmeli iş kaçarsa ihlal sessizce hiç işaretlenmez | §5.9 |
| ⭐19 | Dosya eki tablosu **açılmıyor**; tasarımı hazır bekliyor | Kullanılmayan tablo ödev §32'ye takılır. Eklenmesi tek migration + tek modül; alanları veri modeli belgesinde yazılı | §2 |
| ⭐20 | Birincil anahtar **UUID** (v7 tercih), okunabilir kimlik ayrı alanda | Adres çubuğunda `/is-emirleri/145` hem toplam kayıt sayısını sızdırır hem komşu kaydı tahmin ettirir | §5.4 |
| ⭐21 | Yenileme jetonu kaydında **IP ve tarayıcı bilgisi saklanmaz** | Bu projede oturum güvenliği için gereken minimum jeton özeti + süre; fazlası veri minimizasyonuna aykırı | §5.1 |
| ⭐22 | Sahte veri `is_seed_data` bayrağıyla işaretli, e-postalar `@ornek.test`; tohumlama **açık izin olmadan çalışmaz** | Canlıya sızan sahte kayıt fark edilebilmeli; "yanlışlıkla production'a seed atma" kazası yaygındır | §7 |
| ⭐23 | Sayfalama **varsayılan 20, azami 100**; sıralama alanları **beyaz liste** | Sınırsız sayfa boyutu tek istekle veritabanını yorar; serbest sıralama alanı index'siz kolona sıralama yaptırır | §4 |
| ⭐24 | Arşiv adayı: kapatılmasından **180 gün** sonra | Bir mali yılın yarısı; denetim sorularının büyük kısmı bu pencerede gelir. Fiziksel silme yok, yalnızca işaret | §5.9 |
| ⭐25 | Rol/durum/tür değerleri kodda **İngilizce**, ekranda **Türkçe** | Veritabanına bakan DevOps ile kod okuyan geliştirici aynı değeri görür; çeviri tek yerde (arayüz sözlüğü) yaşar | §4 |

### B) ❓ Cevabını senden bekleyen sorular

⭐ **Hiçbiri işi durdurmuyor** — her satırda "cevap gelmezse ne yapacağım"
yazılı ve o varsayımla ilerliyorum. Cevap gelince düzeltilir.

| # | Soru | Neden gerekli | Cevap gelmezse |
|---|---|---|---|
| ❓1 | Kurumun **veri tabanı isimlendirme kuralları** dokümanı var mı? | Ödev §11 "kurum tarafından **iletilen** kurallara uygun olmalıdır" diyor ama böyle bir ek yok. İlk migration'dan önce bilinmeli | PostgreSQL yaygın pratiği: `snake_case` kolon, çoğul tablo. Prisma `@map` köprüsü sayesinde sonradan değişirse yalnızca şema satırları değişir |
| ❓2 | **Mesai tanımı** doğru mu: hafta içi 08:00–17:00, öğle arası düşülmüyor, Cumartesi kapalı? | SLA'nın "mesai saatinde işleyen" hesabı doğrudan buna dayanıyor — yanlışsa her süre kayar | Yukarıdaki tanımla ilerliyorum (§5.6'da yazılı) |
| ❓3 | **5 günlük teslim** kesin mi? Kesinse kapsamdan neyi çıkarmamı istersin? | Tahminim 10 iş günü. Kapsam kısmak hızı zorlamaktan güvenli — kısılacak yerler §2'de listeli | Kapsam olduğu gibi kalır, önce ödevin zorunlu maddeleri bitirilir; ⭐8 (SLA duraklatma) ilk feda edilecek madde olarak işaretli |
| ❓4 | Kurum **gerçek lokasyon/varlık listesi** verecek mi? | Verirse tohumlama o listeyle yapılır ve sunum daha inandırıcı olur | Uydurma ama gerçekçi liste üretilir (§7). ⛔ Gerçek **kişisel** veri hiçbir koşulda kullanılmaz — ödev §5.1 yasaklıyor |
| ❓5 | **Kapatma yetkisi** operasyon sorumlusunda olsun mu, yoksa talebi açan kişi mi onaylasın? | ⭐4 kararımı doğrudan etkiliyor. İkisi de savunulabilir; ben görevler ayrılığını seçtim | Operasyon sorumlusu + yönetici kapatır; talep sahibi yalnızca görür |
| ❓6 | Kurumun **KVKK süreci** nasıl işliyor — personel verisi silme/aktarma talebi kime gidiyor? | §5.11 (zorunlu bölüm) buna göre yazılacak | Sistem tarafı hazır yazılır (yönetici ekranından dışa aktarma + pasife alma), kurumsal süreç "kurumun KVKK birimi" olarak bırakılır |
| ❓7 | **2026 dini bayram tarihleri** için resmî kaynak (Diyanet takvimi) elimizde var mı? | Tatil tablosu tohumlanacak; yanlış tarih SLA'yı sessizce kaydırır | Sabit tatiller (1 Ocak, 23 Nisan, 1 Mayıs, 19 Mayıs, 15 Temmuz, 30 Ağustos, 28–29 Ekim) yazılır, dini bayramlar **boş bırakılıp** belgede işaretlenir — yanlış tarih yazmaktan iyidir |
| ❓8 | Teslim **GitHub'a mı, kurumun GitLab'ına mı** yapılacak? | Ödev §30 PR/MR istiyor; hangi platformun birincil olacağı | İkisinin de CI dosyası yazılır, GitHub birincil kabul edilir (`KURUMDAN-OGRENECEKLERIM.md` §1.2'deki karar) |

---

## 1. Amaç

Kurumun farklı lokasyonlarındaki cihaz, ekipman, araç ve diğer varlıkların
**arıza, bakım ve kontrol süreçlerini** tek yerden yöneten bir web
uygulaması. ✅ *(odev.md §2)*

- **Kimin için:** İzmir Büyükşehir Belediyesi personeli — dört rol: yönetici,
  operasyon sorumlusu, teknik personel, talep sahibi ✅ *(§4)*
- **Hangi problem:** Bugün arıza bildirimi telefon, mesaj ve sözlü iletişimle
  yürüyor. ⚠️ *(Bu cümle varsayım — ödevde "bugün nasıl yapılıyor" yazmıyor.
  Ama sistemin çözdüğü şey ödevden okunuyor:)* Bu yolla **hangi işin kimde
  olduğu**, **ne kadar beklediği** ve **süresinde bitip bitmediği**
  ölçülemiyor; kayıt tutulmadığı için geçmişe dönük denetim yapılamıyor.
- **Başarı ölçütü:** Bir arıza bildiriminden kapanışına kadar geçen her adım
  (kim açtı, kim atadı, kim çalıştı, ne zaman çözüldü) sistemde **kayıtlı ve
  sorgulanabilir**; SLA süresi yaklaşan ve aşan işler kimse ekranı açmasa bile
  sistem tarafından tespit edilip ilgili kişiye bildiriliyor. ✅ *(§2, §15)*

> **ℹ️ Bu projenin ikinci bir amacı var — ve o teknik**
>
> Ödev §1: *"Çalışan bir uygulama teslim edilmesi tek başına yeterli
> değildir."* Teslimden sonra **canlı teknik inceleme** yapılacak; rastgele
> seçilen kodun açıklanması, yeni bir SLA politikası eklenmesi, bir servis
> yaşam döngüsünün değiştirilmesi istenebilir (§31).
>
> ⭐ Bu, PRD'ye şöyle yansıyor: **açıklanamayan hiçbir kural yazılmaz.**
> "Neden böyle" sorusunun cevabı olmayan bir madde bu belgeye girmez.

---

## 2. Kapsam dışı

⭐ **Bu bölüm en çok zaman kazandıran bölümdür.** Buraya yazılmayan her şey
"belki yapılır" sanılır ve kapsam sessizce büyür. Aşağıdakilerin **hiçbiri
ödevde istenmiyor** — istenseydi kapsamda olurdu.

| Yapılmayacak | Neden | İstenirse maliyeti |
|---|---|---|
| **Mobil uygulama** ⛔ | Ödevde yok. Mimari hazır: aynı API'yi tüketir (Expo kararı verilmiş) | ~2 gün — ekranlar + mağaza süreci + test |
| **Fotoğraf / dosya eki** ⛔ | Ödev yalnızca **yorum** istiyor (§16, §22). ⭐19: tablo bile açılmıyor, tasarımı `veri-modeli-ve-sahte-veri-plani.md` içinde hazır bekliyor | ~1 gün — depolama + dosya doğrulama + boyut sınırı + KVKK (fotoğrafta insan olabilir) |
| **Gerçek e-posta / SMS bildirimi** ⛔ | Ödev §16 **sistem içi** bildirim istiyor | ~0.5 gün — sağlayıcı hesabı + alan adı doğrulama |
| **Kurumun personel sistemiyle entegrasyon** ⛔ | Ödev §5.1 gerçek kişisel veriyi zaten yasaklıyor | Kurumun servisine erişim + eşleme + KVKK süreci |
| **Çok dillilik** ⛔ | Ödevde yok. Arayüz dili Türkçe | Metin sözlüğü + çeviri; kod tarafı ⭐25 sayesinde hazır |
| **Anlık bildirim (WebSocket / push)** ⛔ | Ödev "bildirimlerini listeleyebilmeli" diyor, **anlık** demiyor. Bildirimler ekran açıldığında ve belirli aralıkla tazelenerek okunur | Ayrı bağlantı yönetimi + ölçekleme derdi |
| **Rapor dışa aktarma (Excel/PDF)** ⛔ | Ödev §12'de "yönetim ekranında istatistik gösterilmesi" isteniyor, dosya çıktısı istenmiyor | ~0.5 gün |
| **Varlık bakım takvimi otomasyonu** (periyodik bakımın kendiliğinden iş emri açması) ⛔ | Ödev §15'te dört iş sayılı, bu beşincisi olurdu | ~0.5 gün — beşinci tekrarlayan iş |
| **Yedek parça / stok yönetimi** ⛔ | Ödevde "parça bekliyor" bir **durum**; stok modülü istenmiyor | Ayrı modül |
| **Maliyet / iş gücü saati takibi** ⛔ | Ödevde yok | Ayrı alanlar + rapor |

⚠️ **❓3'e bağlı:** Teslim 5 gün ise bu listeye ⭐8 (SLA duraklatma) da
eklenebilir — çıkarma yolu §5.6'da yazılı, tek karar noktası.

---

## 2b. Varsayımlar — doğrulanmayı bekleyen kararlar

⛔ **Varsayım yapmak yasak değil; sessizce yapmak yasak.** Aşağıdakilerin her
biri cevap gelince düzeltilir; düzeltilince bu satır silinir ve gerçek
gereksinim ilgili bölüme yazılır.

| # | Varsayım | Neden böyle varsaydım | Yanlışsa ne değişir | Kim onaylar | Durum |
|---|---|---|---|---|---|
| V1 | Veritabanı isimlendirme: `snake_case` kolon, çoğul tablo adı | Ödevin atıf yaptığı kurum dokümanı ekte yok; PostgreSQL yaygın pratiği bu | Yalnızca Prisma `@map` satırları — kod dokunulmaz | Kurum (❓1) | ⬜ bekliyor |
| V2 | Mesai: hafta içi 08:00–17:00, öğle arası düşülmez, Cumartesi kapalı | Kamu kurumu standart mesaisi | Mesai takvimi hesaplayıcısının sabitleri + testleri | Kurum (❓2) | ⬜ bekliyor |
| V3 | Resmî tatillerde **hiç** çalışılmıyor; arife günleri 13:00'te bitiyor | Kamu tatil uygulaması | Tatil tablosundaki `yarim_gun` satırları | Kurum (❓2) | ⬜ bekliyor |
| V4 | Bir kullanıcı **tek** role sahip | Ödev §4 rolleri ayrık sayıyor; çoklu rol istenmiyor | Kullanıcı–rol ilişkisi (tek kolon → ara tablo) ve tüm yetki kontrolleri | Kurum | ⬜ bekliyor |
| V5 | Lokasyonlar **tek seviye** — bina/kat/oda hiyerarşisi yok | Ödev "kurumun farklı lokasyonları" diyor, hiyerarşi demiyor | Lokasyon tablosuna kendine referans + ağaç sorguları + ekran | Kurum | ⬜ bekliyor |
| V6 | Teknik personelin **uzmanlık alanı** yok; her teknisyene her iş atanabilir | Ödevin tek atama kuralı: "aktif ve teknik personel" (§6) | Atama kuralına ikinci koşul + varlık türü–uzmanlık eşlemesi | Kurum | ⬜ bekliyor |
| V7 | Sistem **tek kurum** için; kiracı (tenant) ayrımı yok | Ödevde tek kurum anlatılıyor | Her tabloya kurum kimliği + her sorguya filtre — pahalı | Kurum | ⬜ bekliyor |
| V8 | Arayüz dili yalnızca **Türkçe**, saat dilimi `Europe/Istanbul` | Kurum ve kullanıcılar Türkiye'de | Sözlük katmanı + kullanıcı başına saat dilimi | Kurum | ⬜ bekliyor |
| V9 | Talep sahibi kendi talebini **yalnızca `TALEP` durumundayken** iptal edebilir | İşe başlanmış bir işi talep sahibinin kapatması operasyonu bozar | Durum makinesindeki tek koşul satırı | Kurum (❓5) | ⬜ bekliyor |

---

## 3. Kullanıcı rolleri

✅ Dört rol ödevde sayılı (§4). ⭐ Ödev *"Rol isimleri değiştirilebilir"* diyor;
Türkçe adları koruyorum, kodda İngilizce karşılıkları kullanılıyor (⭐25).

| Ekranda görünen | Kodda | Tek cümlelik tanımı |
|---|---|---|
| Yönetici | `ADMIN` | Sistemin tamamını yönetir; kullanıcı, lokasyon ve varlık tanımları onda |
| Operasyon Sorumlusu | `OPS_MANAGER` | İş akışının sahibi: talebi iş emrine dönüştürür, atar, önceliklendirir, kapatır |
| Teknik Personel | `TECHNICIAN` | Kendisine atanan işi yapar, ilerlemesini kaydeder, çözüm açıklaması yazar |
| Talep Sahibi | `REQUESTER` | Arıza/bakım talebi açar, kendi talebinin gidişatını izler |

### Yetki matrisi

⛔ **Bu tablo sunucuda uygulanır.** Ekranda düğme gizlemek kolaylıktır,
güvenlik değildir — yetkisi olmayan biri isteği elle de gönderebilir.

| İşlem | Talep Sahibi | Teknik Personel | Operasyon Sorumlusu | Yönetici |
|---|:---:|:---:|:---:|:---:|
| Talep açmak ⭐5 | ✅ | ✅ | ✅ | ✅ |
| Kendi taleplerini görmek ✅ | ✅ | ✅ | ✅ | ✅ |
| **Tüm** iş emirlerini görmek | ⛔ | ⛔ *(kendine atanan + kendi talepleri)* | ✅ | ✅ |
| Talebi iş emrine dönüştürmek ⭐1 | ⛔ | ⛔ | ✅ | ✅ |
| Doğrudan iş emri açmak (talep evresi olmadan) ✅ | ⛔ | ⛔ | ✅ | ✅ |
| Teknik personele atamak / görevden almak ✅ | ⛔ | ⛔ | ✅ | ✅ |
| Öncelik değiştirmek ✅ | ⛔ | ⛔ | ✅ | ✅ |
| İşe başlamak · beklemeye almak · çözüldüye almak ✅ | ⛔ | ✅ *(yalnızca kendine atanan)* | ⛔ | ✅ |
| **Kapatmak** ⭐4 | ⛔ | ⛔ | ✅ | ✅ |
| **İptal etmek** ⭐4 | ✅ *(yalnızca kendi talebi, `TALEP` durumundayken — V9)* | ⛔ | ✅ | ✅ |
| Yorum eklemek ✅ | ✅ *(kendi talebine)* | ✅ *(atandığı işe)* | ✅ | ✅ |
| **Dahili not** yazmak/görmek ⭐11 | ⛔ | ✅ | ✅ | ✅ |
| Lokasyon ve varlık yönetmek ✅ | ⛔ | ⛔ | ⛔ *(yalnızca okur)* | ✅ |
| Kullanıcı yönetmek ✅ | ⛔ | ⛔ | ⛔ | ✅ |
| Yönetim panosunu görmek ⭐ | ⛔ | ⛔ | ✅ | ✅ |
| Kendi bildirimlerini görmek ✅ | ✅ | ✅ | ✅ | ✅ |

> **ℹ️ ⭐4'ün gerekçesi: görevler ayrılığı (segregation of duties)**
>
> **Gerçek hayat:** Bir kurumda ödeme talebini yapan kişiyle onaylayan kişi
> aynı olmaz. Kasten kötü niyet aranmaz — tek kişilik zincirde **hata da**
> yakalanmaz.
>
> **Bu projede:** İşi yapan teknik personel işi `ÇÖZÜLDÜ` yapar; `KAPATILDI`
> durumuna alan **başka** biridir (operasyon sorumlusu). Böylece "çözdüm" ile
> "gerçekten çözülmüş" arasında bir kontrol noktası kalır.
>
> ⚠️ İki savunulabilir alternatif var: (a) talebi açan kişinin onayı,
> (b) belirli süre sonra kendiliğinden kapanma. ❓5'te sana soruyorum; ikisi de
> tek koşul satırı, sonradan değişmesi ucuz.

---

## 4. Ortak özellikler

Birden fazla modülde tekrar eden davranışlar. ⛔ Tek yerde tanımlanır ki her
modülde yeniden tartışılmasın (ödev §9 DRY).

| Konu | Karar | Kaynak |
|---|---|---|
| **Listeleme** | Filtreleme, sıralama ve sayfalama **sunucuda** yapılır; hiçbir liste tüm kayıtları belleğe almaz | ✅ §17 |
| **Sayfa boyutu** | Varsayılan **20**, azami **100**. Sınırın üstü istenirse istek reddedilir | ⭐23 |
| **Sıralama** | Yalnızca beyaz listedeki alanlar: oluşturulma, güncellenme, SLA bitişi, öncelik, durum, numara. Liste dışı alan → 400 | ⭐23 |
| **Cevap zarfı** | Kayıtlar + mevcut sayfa + sayfa boyutu + toplam kayıt + toplam sayfa + sonraki/önceki bilgisi | ✅ §17 |
| **Metin araması** | Başlık, açıklama ve iş emri numarasında; PostgreSQL `pg_trgm` + GIN index ile | ✅ §17 · rehber E.10 |
| **Filtrelerin adres çubuğuyla senkronu** | Filtreli liste adresi paylaşıldığında aynı sonucu açar | ✅ §22 |
| **Ekran durumları** | Her listede dört hâl ayrı ayrı tasarlanır: yükleniyor · boş · hata · dolu | ✅ §22 |
| **Hata biçimi** | Tüm uçlarda tek biçim: HTTP kodu + başlık + açıklama + uygulama hata kodu + iz kimliği (correlation ID) + alan bazlı hatalar | ✅ §19 |
| **Audit alanları** | Her ana kayıtta: kim oluşturdu/ne zaman, kim güncelledi/ne zaman. ⛔ Elle doldurulmaz, merkezî katman doldurur | ✅ §21 |
| **Saat** | Veritabanında UTC saklanır, ekranda `Europe/Istanbul` gösterilir. Sistem saati doğrudan okunmaz, `Clock` servisinden alınır | ✅ §8 · rehber E.9 |
| **Dil ve değerler** | Kodda ve veritabanında İngilizce enum (`OPEN`, `TECHNICIAN`), ekranda Türkçe etiket. Çeviri tek sözlükte | ⭐25 |
| **Yetki reddi** | Kimliksiz istek **401**, kimliği olup yetkisi olmayan **403** | ✅ §5.1 |
| **Silinen kayıt** | Yumuşak silinen kayıt hiçbir listede görünmez; filtre merkezî uygulanır | ✅ §11 |

---

## 5. Modüller

### 5.1 Kimlik doğrulama ve yetkilendirme ✅ *(§5.1)*

**Kullanıcı ne yapar:** E-posta ve şifreyle giriş yapar. Oturumu süresi
dolduğunda arka planda sessizce yenilenir. Çıkış yaptığında oturumu kapanır.

| Kural | Karar | Kaynak |
|---|---|---|
| Giriş | Kurum e-postası + şifre | ✅ |
| Şifre saklama | argon2id ile özetlenir; düz metin hiçbir yerde durmaz | ✅ §5.1 |
| Erişim jetonu ömrü | ⚠️ **15 dakika** — yapılandırmadan (`.env`) yönetilir, kodda sabit yok | ✅ §5.1 (süre ⚠️) |
| Yenileme jetonu ömrü | ⚠️ **7 gün**; her kullanımda **döndürülür**, eskisi iptal edilir | ✅ §5.1 (süre ⚠️) |
| Yeniden kullanım tespiti | İptal edilmiş bir yenileme jetonu tekrar gelirse o kullanıcının **tüm** oturumları kapatılır | ⭐ *(ödev istemiyor, gerçek sistemlerde standart)* |
| Pasif kullanıcı | Giriş yapamaz; **açık oturumu varsa** ilk istekte reddedilir | ✅ §5.1 |
| Jeton nerede durur | Web'de `httpOnly` çerez, mobil/Swagger'da `Bearer` başlığı | rehber C.13 |
| Kayıtta saklanan | Yalnızca jeton **özeti**, süresi ve iptal zamanı. ⛔ IP ve tarayıcı bilgisi saklanmaz | ⭐21 |
| Şifre kuralı | ⚠️ En az 10 karakter; yaygın şifre listesi kontrolü yok (kapsam dışı) | ⚠️ |
| Şifre sıfırlama | ⛔ **Kapsam dışı** — e-posta gönderimi kapsam dışı olduğu için. Şifreyi yönetici sıfırlar | ⭐ |
| Kayıt olma ekranı | ⛔ **Yok.** Kullanıcıları yönetici açar — kurum içi sistem | ⭐ |

**Hata durumunda:** Yanlış şifre → 401 ve ⛔ *"e-posta mı şifre mi yanlış"*
söylenmez (var olan e-postayı ele vermemek için). Pasif kullanıcı → 403 ve
sebep açıkça yazılır.

### 5.2 Lokasyon yönetimi ✅ *(§5.2)*

**Kullanıcı ne yapar:** Yönetici lokasyon listeler, detay görür, yeni açar,
günceller, aktif/pasif yapar.

| Alan | Zorunlu | Not |
|---|:---:|---|
| Kod | ✅ | Kurum içi kısa kod, **benzersiz** (örn. `KONAK-HZM-01`) ⭐ |
| Ad | ✅ | En fazla 200 karakter |
| Tür | ⭐ | Bina · Depo · Tesis · Saha · Diğer *(enum, ⭐15 gerekçesi)* |
| Adres | ⛔ | Kurumun adresi — ⛔ kişisel veri değil, toplanabilir |
| Açıklama | ⛔ | En fazla 1000 karakter |
| Aktif mi | ✅ | Varsayılan aktif |

**İş kuralları:**

1. ✅ **Pasif lokasyonda yeni varlık açılamaz.**
2. ✅ **Pasif lokasyonda yeni iş emri/talep açılamaz.**
3. ⭐ Pasif lokasyondaki **mevcut** iş emirleri normal akışına devam eder —
   pasifleştirme geleceği kapatır, geçmişi dondurmaz. *(Aksi hâlde bina
   kapanınca devam eden tamiratlar kilitlenirdi.)*
4. ⭐ Lokasyon **silinmez**, pasife alınır; kaldırılması gerekiyorsa yumuşak
   silme uygulanır ve geçmiş iş emirleri lokasyon adını görmeye devam eder.
5. ⭐ Kod benzersizliği **silinmiş kayıtları da kapsar** — aynı kod tekrar
   kullanılamaz, yoksa geçmişte iki farklı bina aynı kodu taşır.

### 5.3 Varlık yönetimi ✅ *(§5.3)*

**Kullanıcı ne yapar:** Yönetici varlık listeler, filtreler, detay görür, açar,
günceller, operasyonel durumunu değiştirir.

| Alan | Zorunlu | Not |
|---|:---:|---|
| Lokasyon | ✅ | Bağlı olduğu lokasyon — ✅ §5.3 |
| Varlık türü | ✅ | ⭐15 **enum**: Jeneratör · Klima/İklimlendirme · Asansör · Araç · Pompa · Elektrik panosu · Bilgisayar/Donanım · Diğer |
| Kod | ✅ | Kurum içi benzersiz tanımlayıcı (örn. `JEN-0042`) ⭐ |
| Ad | ✅ | Okunabilir ad |
| Marka · Model · Seri no | ⛔ | "Kurum içerisindeki tanımlayıcı bilgileri" ✅ §5.3 |
| **Kritiklik seviyesi** | ✅ | Düşük · Normal · Yüksek · Kritik — ⭐ **SLA çarpanını bu belirliyor** (§5.6) |
| **Operasyonel durum** | ✅ | Çalışıyor · Arızalı · Bakımda · Kullanım dışı · Hurdaya ayrıldı |
| Kurulum tarihi · Garanti bitişi | ⛔ | "Bakım bilgileri" ✅ §5.3 |
| Son bakım · Sonraki bakım · Bakım periyodu (gün) | ⛔ | Aynı madde |

**İş kuralları:**

1. ⭐9 **Yeni iş emri açılamayan durumlar: `Kullanım dışı` ve `Hurdaya
   ayrıldı`.** Ödev §5.3 bunu "bir iş kuralı olarak ele alınmalıdır" diyor,
   kararı bize bırakıyor.
2. ⭐9 **`Arızalı` ve `Bakımda` engellemez.** ⛔ Aksi sistemi kilitler: arıza
   kaydı tam olarak varlık arızalıyken açılır. Bakımdaki varlığa ikinci bir
   bakım işi açılması da meşrudur.
3. ⭐ `Hurdaya ayrıldı` **son duraktır** — geri döndürülemez; yanlış işaretlenen
   varlık için yönetici kaydı düzeltir ve bu düzeltme audit'e düşer.
4. ✅ Varlık, olmayan bir lokasyona bağlanamaz (veritabanı yabancı anahtarıyla
   korunur — uygulama katmanı atlansa bile).
5. ⭐ Varlık lokasyonu **değiştirilebilir** (taşınma); ⛔ geçmiş iş emirleri
   eski lokasyonu görmeye devam eder (⭐14).
6. ⭐ Kritiklik değişirse **açık iş emirlerinin SLA'sı yeniden hesaplanmaz.**
   Sebep: SLA, iş emri açıldığı andaki taahhüttür; sonradan değişen bir çarpan
   geçmişe dönük süre kaydırmamalı. *(Yeni açılanlar yeni değeri kullanır.)*

### 5.4 Talep ve iş emri yönetimi ✅ *(§5.4)*

⭐1 **Talep ve iş emri aynı kayıttır.** Talep, iş emrinin `TALEP`
durumundaki hâlidir; "iş emrine dönüştürme" bir **durum geçişidir**.

> **ℹ️ Neden tek tablo — üç somut sebep**
>
> 1. **Ödev zaten öyle diyor.** §5.4 başlığı *"Talep **veya** iş emrinde en az
>    aşağıdaki bilgiler bulunmalıdır"* — aynı 13 alanı ikisine birden veriyor.
> 2. **DRY (§9).** İki tabloda o 13 alan tekrarlanır, validasyonu iki yerde
>    yazılır, ve talep→iş emri kopyalama kodu yazılır. Ödev §9 tam olarak bu
>    tekrarları sayıyor.
> 3. **Geçmiş bölünmez.** *"Bu iş emri ne zaman talep edildi, ne zaman
>    dönüştürüldü, kim dönüştürdü"* soruları tek zaman çizelgesinden okunur.
>
> ⚠️ **Bedeli dürüstçe:** Rehber E.5'teki geçiş tablosuna bir durum ekleniyor
> ve "henüz iş emri sayılmayan kayıtlar" listelerden ayrıştırılmalı. İkisi de
> tek satırlık koşul.

**Zorunlu alanlar** ✅ *(§5.4 — 13 madde tek tek sayılı)*

| Alan | Not |
|---|---|
| İlgili lokasyon | ⭐14 iş emri satırında **ayrıca** saklanır |
| İlgili varlık | ✅ Zorunlu |
| Talebi oluşturan kullanıcı | ✅ |
| İş emri türü | ⭐6 Arıza · Planlı bakım · Periyodik kontrol · Kurulum |
| Başlık | ⚠️ 5–200 karakter |
| Açıklama | ⚠️ 10–4000 karakter |
| Öncelik | ✅ Düşük · Normal · Yüksek · Kritik |
| Mevcut durum | ✅ §6'daki durumlardan biri |
| Atanan teknik personel | ✅ Boş olabilir (henüz atanmamış) |
| Oluşturulma zamanı | ✅ |
| Son işlem zamanı | ✅ |
| **SLA bitiş zamanı** | ✅ §5.6'da hesaplanıyor |
| Çözüm bilgisi | ✅ `ÇÖZÜLDÜ` durumuna geçerken zorunlu |

**Ek alanlar** ⭐ *(ödevde sayılmıyor ama iş kuralları gerektiriyor)*

| Alan | Neden gerekli |
|---|---|
| İş emri numarası | ✅ §5.4 "benzersiz ve okunabilir numara" ister |
| Planlanan tarih | ⭐7 planlı türlerde zorunlu — SLA'yı bu belirliyor |
| SLA başlangıcı · hatırlatma · escalation zamanı | §5.6'daki üç zaman ✅ §7 |
| SLA ihlal bayrağı ve zamanı | ✅ §15 "iş emrini SLA ihlali olarak işaretlemelidir" |
| Hangi SLA politikası hesapladı | ⭐ *"Bu iş emrinin süresi neden 90 dakika"* sorusunun cevabı; incelemede en çok sorulacak yer |
| Duraklatma sayaçları | ⭐8 "parça bekliyor" süresini biriktirir |
| İptal gerekçesi | ✅ §6 dolaylı olarak istiyor (her değişiklik izlenebilir olmalı) |
| Arşiv adayı bayrağı | ✅ §15 dördüncü iş |
| Sürüm (`version`) | ✅ §20 iyimser eşzamanlılık |

**İş emri numarası** ⭐13

- **Biçim:** `IE-2026-000148` → sabit ön ek + yıl + altı hane sıfır dolgulu sıra
- **Neden bu biçim:** Telefonda söylenebilir, gözle sıralanabilir, yılı okunur
- **Nasıl üretiliyor:** PostgreSQL **dizisi (sequence)** ile. ⛔ "Son numarayı
  bul, bir artır" yapılmıyor — iki istek aynı anda gelirse ikisi de aynı
  numarayı üretir (yarış koşulu / race condition)
- ⭐ Numara **talep açılırken** verilir ve dönüşümde **değişmez**
- ⚠️ Numara **boşluklu olabilir** (iptal edilen işlem dizinin sayacını geri
  almaz) ama **asla tekrarlanmaz**. Bu bilinçli bir takas: benzersizlik
  boşluksuzluktan önemlidir

**Ekranlar** ✅ *(§22 — en az bu 12 ekran)*

| # | Ekran | Not |
|---|---|---|
| 1 | Giriş | |
| 2 | Dashboard (yönetim panosu) | §5.10 |
| 3 | İş emri listesi | Sunucu tarafı filtre/sıralama/sayfalama, adres çubuğu senkronu |
| 4 | Yeni iş emri / talep oluşturma | Rol'e göre `TALEP` veya `AÇIK` başlar |
| 5 | İş emri detayı | Durum geçmişi · atama geçmişi · yorumlar · SLA bilgisi |
| 6 | İş emri düzenleme | Kapalı ve iptal edilmiş kayıtta kapalı |
| 7 | Lokasyon listesi | |
| 8 | Varlık listesi | Filtreli |
| 9 | Varlık detayı | Varlığın iş emri geçmişi burada görünür ⭐ |
| 10 | Bildirimler | Okundu işaretleme, toplu okundu |
| 11 | Yetkisiz erişim (403) | |
| 12 | Bulunamadı (404) | |

### 5.5 Durum makinesi — iş kuralları ✅ *(§6)*

⛔ **Geçersiz geçişler sunucuda engellenir** ve kural **tek tabloda** yaşar;
uç noktalara dağılmış `if` blokları yasak (ödev §6 son cümlesi).

| Ekranda | Kodda | Anlamı |
|---|---|---|
| Talep ⭐1 | `REQUESTED` | Açılmış ama henüz iş emrine dönüştürülmemiş |
| Açık | `OPEN` | İş emri; henüz kimseye atanmadı |
| Atandı | `ASSIGNED` | Teknik personele atandı, çalışma başlamadı |
| İşlemde | `IN_PROGRESS` | Teknik personel üzerinde çalışıyor |
| Parça bekliyor | `WAITING_PART` | Parça veya dış işlem bekleniyor ⭐8 SLA saati **durur** |
| Çözüldü | `RESOLVED` | Teknik personel işi bitirdi, onay bekliyor |
| Kapatıldı | `CLOSED` | ⛔ Son durak |
| İptal edildi | `CANCELLED` | ⛔ Son durak |

**İzin verilen geçişler:**

| Bu durumdan | Şu durumlara geçilebilir |
|---|---|
| `TALEP` | `AÇIK` *(dönüştürme)* · `İPTAL` |
| `AÇIK` | `ATANDI` · `İPTAL` |
| `ATANDI` | `İŞLEMDE` · `AÇIK` *(görevden alma)* · `İPTAL` |
| `İŞLEMDE` | `PARÇA BEKLİYOR` · `ÇÖZÜLDÜ` · `İPTAL` |
| `PARÇA BEKLİYOR` | `İŞLEMDE` · `İPTAL` |
| `ÇÖZÜLDÜ` | `KAPATILDI` · `İŞLEMDE` *(yeniden açma — sorun tekrarladı)* |
| `KAPATILDI` | ⛔ *(yok)* |
| `İPTAL` | ⛔ *(yok)* |

**Geçişe eklenen koşullar** ✅ *(§6)*

| Geçiş | Ek koşul |
|---|---|
| `TALEP → AÇIK` | Lokasyon aktif · varlık iş emri kabul eden durumda ⭐9 |
| `AÇIK → ATANDI` | Atanan kişi **aktif** ve rolü **teknik personel** ✅ |
| `ATANDI → İŞLEMDE` | İş emri atanmış olmalı — ✅ "atanmamış bir iş emri işleme alınamaz" |
| `İŞLEMDE → ÇÖZÜLDÜ` | **Çözüm açıklaması zorunlu** ✅ (⚠️ en az 10 karakter) |
| herhangi → `İPTAL` | **İptal gerekçesi zorunlu** ⭐ |
| `ÇÖZÜLDÜ → İŞLEMDE` | ⭐ Yeniden açma gerekçesi zorunlu; SLA saati **yeniden başlamaz**, kalan süreden devam eder |

**Değişmez kurallar:**

1. ✅ **Her durum değişikliği geçmiş kaydı üretir** — istisnasız.
2. ✅ **Atama ve görevden alma da geçmişe yazılır.**
3. ✅ **Durum değişikliği ile geçmiş kaydı aynı işlem bütünlüğünde** (tek
   transaction) yazılır — biri olup diğeri olmaz.
4. ✅ **Kapatılmış iş emri normal güncelleme ile değiştirilemez.**
5. ✅ **İptal edilmiş iş emri üzerinde işlem yapılamaz.** *(Yorum dahil ⭐)*
6. ✅ İki kişi aynı iş emrini aynı anda değiştirirse **biri 409 alır**
   (iyimser eşzamanlılık, §20).

### 5.6 SLA kuralları ✅ *(§7 — "tarafınızdan belirlemeniz beklenmektedir")*

⛔ Ödev §7 bu kuralların **dokümante edilmesini zorunlu tutuyor.** Bu bölüm o
dokümantasyondur; teslim paketine `docs/sla-rules.md` olarak da girer.

#### SLA saati neyi ölçüyor

⛔ **Geliştiricinin çalıştığı süre değil**, biletin **açılışından sahibine
iade edilişine** kadar geçen toplam süre. Sınıflandırma ve kontrol de içindedir.

⭐2 **Saat, talep açıldığı anda başlar** — iş emrine dönüştüğünde değil.
Böylece yavaş sınıflandırma ölçüme dahil olur.

⭐ **Saat yalnızca `KAPATILDI` ve `İPTAL` durumlarında durur** — `ÇÖZÜLDÜ`de
değil. Sebep: SLA, talep sahibine verilen **sözdür**; iş "çözüldü" işaretlenip
üç gün onay beklediyse o üç gün de sözün içindedir. ⭐ Bu, kapatma yetkisinin
operasyonda olmasını (⭐4) anlamlı kılıyor: kapatmayı geciktiren taraf SLA'yı da
yakıyor, yani ölçüm doğru yeri işaret ediyor.

⭐8 **Tek istisna `PARÇA BEKLİYOR`** — saat orada durur (aşağıda 6. madde).

#### 1) Öncelikten temel süre

| Öncelik | Çözüm süresi | Ne demek |
|---|---|---|
| **Kritik** | **3 saat** | Hizmet tamamen durmuş, alternatifi yok |
| **Yüksek** | **8 saat** | İş aksıyor ama geçici çözüm var |
| **Normal** | **24 saat** | Rahatsız edici, engelleyici değil |
| **Düşük** | **72 saat** | Planlanabilir, aciliyeti yok |

Oran 1 : 2.7 : 8 : 24 — ITIL'deki yaygın P1–P4 merdiveniyle aynı biçimde artıyor.

#### 2) Varlığın kritikliği çarpan uygular ✅ *(§7 şart koşuyor)*

| Varlık kritikliği | Çarpan | Örnek |
|---|---|---|
| **Kritik** | **×0.5** | Su pompası, jeneratör, sunucu odası kliması |
| Yüksek | ×0.75 | Hizmet binası asansörü |
| Normal | ×1 | Ofis bilgisayarı |
| Düşük | ×1.5 | Depodaki yedek ekipman |

#### 3) Hatırlatma ve escalation — sabit saat değil, yüzde

| An | Ne zaman | Ne oluyor |
|---|---|---|
| İlk hatırlatma | Sürenin **%50**'si | Atanan teknik personele bildirim |
| Escalation | Sürenin **%80**'i | Operasyon sorumlusuna + yöneticiye bildirim |
| İhlal | %100 | İş emri "SLA aşıldı" işaretlenir, panoya düşer |

⭐ **Yüzde kullanmanın sebebi mimari:** yeni bir öncelik eklendiğinde yalnızca
**süresi** yazılır; hatırlatma ve escalation kendiliğinden doğru hesaplanır.
Ödev §7'nin *"yeni politika eklenince mevcut kod mümkün olduğunca az
değişmeli"* şartı (Open/Closed) tam olarak budur.

**Hesaplanan değerler** (varlık çarpanı ×1 iken):

| Öncelik | Çözüm | İlk hatırlatma | Escalation |
|---|---|---|---|
| Kritik | 3 sa | 1 sa 30 dk | 2 sa 24 dk |
| Yüksek | 8 sa | 4 sa | 6 sa 24 dk |
| Normal | 24 sa | 12 sa | 19 sa 12 dk |
| Düşük | 72 sa | 36 sa | 57 sa 36 dk |

⚠️ Hesaplanan zamanlar **dakikaya yuvarlanır** (aşağı) — saniye tutmak
bildirim zamanlamasında hiçbir şey kazandırmaz.

#### 4) İş emri türü — Factory'nin gerçekten farklı sınıf üretmesi ✅ *(§7)*

⛔ **Planlı bakım ve periyodik kontrol için "X saat içinde bitir" mantığı
yanlıştır.** Onların bir **planlanan tarihi** vardır.

| Tür | SLA nasıl hesaplanıyor | Politika |
|---|---|---|
| **Arıza** | `talep açılışı + (öncelik süresi × varlık çarpanı)`, takvimli | Süre temelli |
| **Planlı bakım** | Planlanan tarih **son tarihtir**; hatırlatma 1 gün önce | Planlı tarih temelli |
| **Kurulum** ⭐6 | Planlı bakımla **aynı politika** — kurulumun da randevusu vardır | Planlı tarih temelli |
| **Periyodik kontrol** | Planlanan tarih ± tolerans penceresi (⚠️ varsayılan 3 gün) | Pencere temelli |

⭐6 **Neden `Kurulum` için ayrı sınıf açılmadı:** Davranışı planlı bakımla
birebir aynı olurdu. Ödev §7 *"Factory yalnızca göstermelik bir sınıf
olmamalıdır"* diye uyarıyor — aynı işi yapan iki sınıf tam olarak o uyarının
hedefidir. Politikalar **türe göre değil, hesap biçimine göre** ayrılıyor.

⭐7 **Zorunluluk kuralı:** Planlı bakım, periyodik kontrol ve kurulumda
**planlanan tarih zorunlu**; arızada **girilemez**. *(Tarih yoksa politika
hesap yapamaz; arızada tarih vermek anlamsızdır.)*

#### 5) Takvim — karma model

**Problem:** 17:00'de açılan Yüksek öncelikli (8 saat) bir bilet, 7/24 sayımda
gece 01:00'de ihlal olur — kimse çalışmıyorken.

**Karar:** Takvim, **önceliğin bir özelliğidir** — genel bir ayar değil.

| Öncelik | Takvim | Gerekçe |
|---|---|---|
| **Kritik** | **7/24 kesintisiz** | Nöbet vardır; su pompası gece de patlar |
| Yüksek · Normal · Düşük | **Mesai** ⚠️V2 (hafta içi 08:00–17:00, tatiller hariç) | Bu işler için gece müdahale yok |

**Örnek — aynı bilet, iki öncelik:**

```
Bilet Cuma 16:00'da açıldı

KRİTİK  (3 sa, 7/24)     → son tarih Cuma 19:00
                            (hatırlatma 17:30 · escalation 18:24)

YÜKSEK  (8 sa, mesai)    → Cuma'da 1 saat işledi, kalan 7 saat
                            Pazartesi 08:00'den sayılır
                          → son tarih Pazartesi 15:00
```

⭐16 **Resmî tatiller veritabanı tablosunda** tutulur (tarih · ad · yarım gün
mü). ⚠️V3 Arife günleri 13:00'te biter. ⛔ Kod içine gömülmez: yıl değişince
yeniden yayına çıkmak gerekmesin.

#### 6) ⭐8 SLA duraklatma — "parça bekliyor" saati durdurur

**Gerçek hayat:** Servise bıraktığın araç, yedek parça yurt dışından gelene
kadar bekliyorsa geçen süre servisin performansı sayılmaz.

**Kural:**

| An | Ne oluyor |
|---|---|
| `İŞLEMDE → PARÇA BEKLİYOR` | Duraklatma başlangıcı yazılır; bekleyen hatırlatma/escalation işleri **iptal edilir** |
| `PARÇA BEKLİYOR → İŞLEMDE` | Duraklama süresi toplama eklenir; **kalan süre üzerinden** yeni son tarih hesaplanır ve işler **yeniden planlanır** |

⭐ **Bu kural mimariyi güçlendiriyor:** ödev §15 zaten *"SLA zamanı
değiştiğinde ileri bir tarih için job planlanmalıdır"* diyor — duraklatma bu
yolun ikinci kez, gerçek bir senaryoyla kullanılmasını sağlıyor.

⚠️ **Bedeli dürüstçe:** İki alan (`duraklatma_başlangıcı`,
`toplam_duraklatma_dakikası`), bir yeniden hesaplama akışı ve ~4 test.
❓3'te süre darsa **çıkarılacak ilk madde budur**: çıkarılırsa "parça bekliyor"
süresi SLA'ya dahil olur ve bu **bilinen teknik borç** olarak yazılır.

#### 7) SLA yeniden hesaplanma tetikleyicileri ⭐

| Değişen | SLA yeniden hesaplanır mı |
|---|---|
| Öncelik | ✅ Evet — süre buna dayanıyor |
| Tür veya planlanan tarih | ✅ Evet |
| Duraklatmadan dönüş ⭐8 | ✅ Evet |
| Atanan personel | ⛔ Hayır — süre kişiye değil işe bağlı |
| Varlığın kritikliği sonradan değişti | ⛔ Hayır — §5.3 kural 6'daki gerekçe |

⛔ **Her yeniden hesaplamada** eski hatırlatma/escalation işleri iptal edilir,
yenileri planlanır ve olay tablosuna kayıt düşer.

### 5.7 Yorumlar ve geçmiş ✅ *(§5.4, §6, §22)*

**Yorumlar** ⭐11

| Kural | Karar |
|---|---|
| Kim yazabilir | Talep sahibi (kendi talebine) · atanan teknik personel · operasyon · yönetici |
| Uzunluk | ⚠️ 1–2000 karakter |
| **Dahili not** | Teknik personel/operasyon/yönetici işaretleyebilir; ⛔ talep sahibi **göremez** |
| Düzenleme / silme | ⛔ **Yok.** Yanlış yazılan yorum yeni yorumla düzeltilir |
| Kapalı/iptal iş emri | ⛔ Yorum eklenemez |
| Bildirim | Yorum eklenince ilgili taraflara bildirim gider ✅ §16 |

> **ℹ️ Neden yorum düzenlenemiyor**
>
> Yorum bir **denetim izidir**. Sonradan değiştirilebilen iz, iz sayılmaz:
> *"personel bana şunu yazmıştı"* iddiası kanıtlanamaz hâle gelir. Kurumsal
> sistemlerde standart yaklaşım budur — düzeltme, yeni kayıt olarak eklenir.

**Geçmiş (olay kaydı)** ⭐10

Durum değişikliği, atama, görevden alma, öncelik değişikliği, SLA yeniden
hesaplaması, SLA ihlali ve duraklatma **tek olay tablosuna** yazılır.

| Olay | Ne kaydeder |
|---|---|
| Oluşturuldu | Kim, ne zaman, hangi durumda başladı |
| İş emrine dönüştürüldü | Kim dönüştürdü |
| Durum değişti | Nereden nereye, kim, gerekçe/açıklama |
| Atandı / görevden alındı | Kimden kime |
| Öncelik değişti | Nereden nereye |
| SLA yeniden hesaplandı | Yeni son tarih, sebebi |
| SLA ihlal edildi | İhlal anı |
| Duraklatıldı / devam etti ⭐8 | Süre |

⛔ **Olaylar değiştirilemez ve silinemez.** ⛔ Uygulama loguna yazılmaz —
loglar haftalar içinde silinir, oysa *"bu kaydı kim değiştirdi"* sorusu iki yıl
sonra sorulur.

⭐ Detay ekranında ödev §22'nin istediği "durum geçmişi" ve "atama geçmişi"
bölümleri bu tablodan **olay türüne göre filtrelenerek** üretilir.

### 5.8 Bildirimler ✅ *(§16)*

**Hangi olaylarda üretilir:**

| Olay | Kime gider |
|---|---|
| Yeni iş emri atandı ✅ | Atanan teknik personele |
| İş emri durumu değişti ✅ | Talep sahibine + atanan personele *(değişikliği yapan hariç ⭐)* |
| SLA hatırlatma zamanı geldi ✅ | Atanan teknik personele |
| SLA escalation ⭐ | Operasyon sorumlusu + yönetici |
| SLA ihlali oluştu ✅ | Atanan personel + operasyon sorumlusu + yönetici |
| İş emrine yorum eklendi ✅ | İlgili taraflara *(dahili notta talep sahibi hariç ⭐11)* |

⭐ **Değişikliği yapan kişiye kendi işleminin bildirimi gitmez** — kendi
yaptığını zaten biliyor; gitseydi bildirim listesi gürültüye boğulur ve insanlar
bakmayı bırakırdı.

**Kullanıcı ne yapar** ✅ *(§16)*: bildirimlerini listeler · okunmamışları
görür · birini okundu işaretler · tümünü okundu işaretler.

**Mükerrer engelleme** ⭐12: Her bildirimin bir **tekilleştirme anahtarı**
(`dedupe_key`) var ve bu alan **benzersiz index**'li. Örnek anahtarlar:
`is-emri:{id}:sla-hatirlatma` · `is-emri:{id}:sla-ihlal` ·
`is-emri:{id}:olay:{olay_id}`.

⛔ Uygulamada *"var mı diye bak, yoksa ekle"* yeterli değil: iki iş aynı anda
çalışırsa ikisi de "yok" görür ve ikisi de ekler. Yarışı **veritabanı** çözer.

⚠️ **Kapsam dışı:** bildirim tercihleri (kullanıcının hangi bildirimi
alacağını seçmesi) — ödevde yok.

### 5.9 Arka plan işleri ✅ *(§15 — dört iş)*

⛔ **Hepsi idempotent:** aynı iş iki kez çalışsa da sonuç değişmez.
⛔ **Hepsi yalnızca kimlik numarası alır**, nesnenin tamamını değil — kuyrukta
beklerken veri eskir.

| # | İş | Ne zaman | Ne yapar |
|---|---|---|---|
| 1 | **SLA hatırlatma** | Gecikmeli — hesaplanan hatırlatma anında | İş emri hâlâ aktifse bildirim üretir; ✅ kapanmışsa **hiçbir şey yapmaz** |
| 1b | **SLA escalation** ⭐ | Gecikmeli — %80 anında | Operasyon + yöneticiye bildirim |
| 2 | **SLA ihlal taraması** | ⚠️ Her **5 dakikada** bir | Süresi geçmiş ve hâlâ açık iş emirlerini bulur, ihlal işaretler, bildirim üretir, sonucu loglar |
| 3 | **Günlük operasyon özeti** | ⚠️ Her gün **07:00** (`Europe/Istanbul`) | Açık iş emri · SLA ihlali · kritik iş · gün içinde tamamlanan sayısı + teknik personel bazında aktif iş yükü |
| 4 | **Arşiv adayı belirleme** | ⚠️ Haftalık — Pazar 03:00 | ⭐24 Kapatılmasından **180 gün** geçmiş iş emirlerini arşiv adayı işaretler. ⛔ Fiziksel silme yok |

⭐18 **Neden ihlal gecikmeli iş değil de tarama:** Gecikmeli iş kaçarsa (sunucu
kapalıydı, kuyruk temizlendi) ihlal **sessizce hiç işaretlenmez** — ve bunu
kimse fark etmez. Tarama, geçmişte kaçan her şeyi de yakalar. ⚠️ Bedeli: ihlal
işareti en fazla 5 dakika gecikir; kritik SLA 3 saat olduğu için bu kabul
edilebilir.

⭐17 **Günlük özet veritabanına yazılır**, yapılandırılmış log olarak değil.
Ödev seçenek bırakıyor; veritabanı seçildi çünkü (a) işin idempotentliği
`özet_tarihi` benzersiz kısıtıyla **veritabanı seviyesinde kanıtlanıyor**,
(b) pano geçmiş günleri gösterebiliyor, (c) log saklama süresi dolunca veri
kaybolmuyor.

**Diğer kurallar** ✅ *(§15)*: yeniden deneme davranışı tanımlı · aynı işin eş
zamanlı iki kopyası engellenir · iş hataları loglanır · iş içinde HTTP istek
bağlamına güvenilmez · yönetim paneli yetkisiz erişime kapalı · saat dilimi
davranışı dokümante edilir.

### 5.10 Yönetim panosu ✅ *(§2 madde 12, §15)*

| Gösterge | Not |
|---|---|
| Açık iş emri sayısı | Durum bazında kırılım ⭐ |
| SLA ihlali olan iş emri sayısı | |
| Kritik öncelikli açık iş sayısı | |
| Bugün tamamlanan iş sayısı | |
| Teknik personel bazında aktif iş yükü | ✅ §15 |
| ⭐ SLA'sı bugün dolacak işler | Operasyonun en çok baktığı liste |

⛔ **Sayılar veritabanında hesaplanır**, tüm kayıtlar belleğe çekilip
sayılmaz ✅ *(§17'nin ilkesi)*. ⭐ Günlük özet zaten önceden hesaplanıp
saklandığı için (⭐17) geçmiş günler ağır sorgu gerektirmez.

### 5.11 Hesap yönetimi ve veri hakları — zorunlu bölüm

> ⛔ Kit kuralı: kullanıcı hesabı olan her projede bu bölüm bulunur ve kapsam
> dışı bırakılamaz (KVKK m.11 ilgili kişinin hakkı).

⚠️ **Bu proje kurum içi bir sistemdir** — kullanıcı kendi kendine kayıt olmuyor,
hesabı yönetici açıyor. Bu yüzden "hesabımı sil" **self-servis bir düğme
değildir**; KVKK m.13 uyarınca talep **veri sorumlusuna** (kurumun KVKK birimi)
yapılır. Sistemin sağladığı şey o talebin **karşılanabilmesidir**. ❓6

- **Verimi indir:** Yönetici ekranından bir personelin kaydı dışa aktarılabilir:
  kimlik ve iletişim alanları, açtığı talepler, üzerine atanmış iş emirleri,
  yazdığı yorumlar. ⛔ Şifre özeti, oturum jetonu ve dahili notlar **girmez**.
- **Hesabı kapatma — silinen:** Ad, soyad, kurum e-postası, telefon, sicil no,
  birim → silinir/maskelenir. Oturumların tamamı kapanır, giriş engellenir.
- **Hesabı kapatma — saklanan:** İş emirleri, durum geçmişi, atama geçmişi ve
  yorumlar **silinmez**. ⭐ Gerekçe: bunlar kişisel veri değil **kurumsal
  faaliyet kaydıdır**; silinirse *"bu arıza ne zaman, kim tarafından
  giderildi"* sorusu cevapsız kalır ve denetim izi kopar. Kayıtlardaki kişi
  bağlantısı, kişi kaydı kapatıldıktan sonra **ad yerine kalıcı bir kimlik
  numarasıyla** tutulur.
- **Kabul kriteri:** Kapatılan personelin adı hiçbir ekranda ve hiçbir API
  cevabında görünmez; iş emri geçmişi ise eksiksiz durur ve zaman çizelgesi
  okunabilir kalır.

⭐ **Veri minimizasyonu (KVKK m.4/1-ç) bu projede somut olarak uygulandı:**

| Toplanmayan | Neden |
|---|---|
| **Ev adresi** ⭐3 | Hiçbir iş akışı kullanmıyor |
| T.C. kimlik numarası | Kimlik doğrulama kurum e-postasıyla yapılıyor |
| Doğum tarihi, cinsiyet, medeni hâl | Hiçbir kural bunlara dayanmıyor |
| Oturumda IP ve tarayıcı bilgisi ⭐21 | Jeton özeti + süre yeterli |
| Konum bilgisi | Sistem sahada konum takibi yapmıyor |

⛔ **Loglara asla yazılmayacaklar** ✅ *(§25)*: şifre · erişim jetonu · yenileme
jetonu · çerez içeriği · istek gövdesinin tamamı.

⚠️ **Bu projede tüm veri sahtedir** — ödev §5.1 gerçek kişisel veri
kullanımını yasaklıyor. Yine de tasarım, gerçek veriyle çalışacakmış gibi
yapıldı: sonradan eklemek pahalıdır, baştan doğru kurmak bedavadır.

---

## 6. Uçtan uca akış — bir arızanın hayatı

⭐ Bu akış birden fazla modülü kesiyor; modül içine gömülürse kimse tamamını
göremez.

```
1. Talep açılır            Talep sahibi: "3. kat kliması soğutmuyor"
                           → Numara verilir: IE-2026-000148
                           → Durum: TALEP
                           → SLA saati BAŞLAR ⭐2
                           → SLA hesaplanır: öncelik + varlık kritikliği + tür
                           → Hatırlatma ve escalation işleri kuyruğa bırakılır
        ↓
2. Sınıflandırma           Operasyon sorumlusu bakar, önceliği YÜKSEK yapar
                           → SLA yeniden hesaplanır, eski işler iptal, yeniler planlanır
                           → Durum: AÇIK  (iş emrine dönüştü)
        ↓
3. Atama                   Teknik personele atanır (aktif + teknik personel kontrolü)
                           → Durum: ATANDI
                           → Bildirim: "size yeni iş emri atandı"
        ↓
4. Çalışma                 Teknik personel başlar → Durum: İŞLEMDE
                           → Yorum ekler: "kompresör arızalı, parça sipariş edildi"
        ↓
5. Bekleme                 Durum: PARÇA BEKLİYOR
                           → ⭐8 SLA saati DURUR, bekleyen işler iptal edilir
        ↓
6. Devam                   Parça geldi → Durum: İŞLEMDE
                           → Kalan süreden yeni son tarih, işler yeniden planlanır
        ↓
7. Çözüm                   Çözüm açıklaması yazılır (zorunlu) → Durum: ÇÖZÜLDÜ
        ↓
8. Kapanış                 Operasyon sorumlusu kontrol eder → Durum: KAPATILDI ⭐4
                           → SLA saati DURUR
        ↓
9. Arşiv                   180 gün sonra haftalık iş "arşiv adayı" işaretler ⭐24
```

⛔ **Her okla gösterilen adım bir geçmiş kaydı üretir** ve durum değişikliğiyle
**aynı transaction** içinde yazılır (✅ §6).

⚠️ **Mutlu yol tek başına yeterli değil.** Aynı akışın kırılma noktaları:
2. adımda lokasyon pasifse dönüştürme reddedilir · 3. adımda atanan kişi pasifse
reddedilir · 7. adımda çözüm açıklaması boşsa reddedilir · herhangi bir adımda
iki kişi aynı anda değiştirirse biri 409 alır.

---

## 7. Sahte veri gereksinimi

⛔ **Gerçek kişisel veri kullanılmıyor** ✅ *(§5.1)*. Ayrıntı — tablo tablo
miktar, dağılım ve üretim yöntemi — `veri-modeli-ve-sahte-veri-plani.md`
dosyasında. Burada yalnızca **ne kadar** ve **neden**:

| Ne | Miktar | Neden bu kadar |
|---|---:|---|
| Kullanıcı | 23 | 8 teknik personel → "personel bazında iş yükü" grafiği anlamlı olsun; 23 kayıt sayfalamada ikinci sayfayı doğurur |
| Lokasyon | 24 | 20 aktif + 4 pasif → pasif lokasyon kuralı görünür; 24 kayıt ikinci sayfayı doğurur |
| Varlık | 120 | Her kritiklik ve her operasyonel durum yeterli sayıda temsil edilsin |
| İş emri | 400 | Filtre kombinasyonları boş dönmesin, sayfalama gerçekten sınansın, pano sayıları anlamlı çıksın |
| Yorum | ~600 | Detay ekranında yorum akışı dolu görünsün |
| Olay kaydı | ~1500 | Her iş emrinin durum yolculuğu gerçekçi biçimde üretilir |
| Bildirim | ~200 | %40'ı okunmamış — okundu/okunmadı ayrımı görünsün |

⭐22 Her sahte kayıt işaretli (`is_seed_data`), e-postalar `@ornek.test` alan
adında, tohumlama **açık izin olmadan çalışmıyor**.

---

## 8. Kalite gereksinimleri

⛔ Ölçülebilir yazıldı. *"Hızlı olsun"* bir gereksinim değildir.

| Konu | Hedef | Neden bu değer |
|---|---|---|
| İş emri listesi | ⚠️ p95 < 500 ms (400 kayıtta, filtreli) | Kullanıcının "takılıyor" demediği eşik |
| Detay ekranı | ⚠️ p95 < 400 ms | |
| Yönetim panosu | ⚠️ p95 < 800 ms | Toplama sorguları ağırdır; günlük özet önceden hesaplı ⭐17 |
| Arka plan işi gecikmesi | ⚠️ Hatırlatma planlanan andan en fazla 1 dk sonra | |
| SLA ihlal tespiti | ⚠️ Süre dolduktan sonra en fazla 5 dk | ⭐18'in kabul edilen bedeli |
| Erişilebilirlik | ⚠️ WCAG 2.1 AA hedeflenir; klavye ile tüm akışlar tamamlanabilir | Kamu kurumu için doğru varsayılan |
| Tarayıcı | Güncel Chrome, Edge, Firefox, Safari | ⚠️ Kurum standardı sorulmadı |
| En dar ekran | ⚠️ 1280 px masaüstü birincil; tablet çalışır, telefon **hedef değil** | Ödevde mobil yok; teknik personel sahada tablet kullanır |
| Dil | Türkçe (tek dil) | |
| Kurulabilirlik | ✅ Temiz bir makinede `docker compose up --build` tek komutla ayağa kalkar | §24, §30 |

---

## 9. Açık sorular

⛔ **Bu bölüm boş bırakılmaz.** Cevabı bilinmeyen her şey burada durur;
cevaplanınca "Kapatılan sorular"a **taşınır**, silinmez — sonraki oturum aynı
soruyu tekrar sormasın.

| # | Soru | Kimden | Durum |
|---|---|---|---|
| ❓1 | Kurumun veri tabanı geliştirme ve isimlendirme kuralları dokümanı var mı? | Kurum | Açık — ⚠️V1 ile ilerleniyor |
| ❓2 | Mesai tanımı ve resmî tatil uygulaması (öğle arası, arife, Cumartesi) | Kurum | Açık — ⚠️V2, V3 ile ilerleniyor |
| ❓3 | 5 günlük teslim kesin mi; kapsamdan ne çıkarılsın? | Kullanıcı | Açık — kapsam sabit tutuluyor, ⭐8 feda adayı |
| ❓4 | Gerçek lokasyon/varlık listesi verilecek mi? | Kurum | Açık — uydurma liste üretiliyor |
| ❓5 | Kapatma yetkisi operasyon sorumlusunda mı, talep sahibinin onayı mı? | Kullanıcı/Kurum | Açık — ⭐4 ile ilerleniyor |
| ❓6 | Kurumun KVKK süreci: personel verisi talepleri kime gidiyor? | Kurum | Açık — §5.11 sistem tarafı hazır |
| ❓7 | 2026 dini bayram tarihleri için resmî kaynak | Kurum | Açık — sabit tatiller yazılıyor, dini bayramlar boş |
| ❓8 | Teslim GitHub'a mı, kurumun GitLab'ına mı? | Kurum | Açık — ikisi de hazırlanıyor |
| ❓9 | Migration'ı canlıda kim çalıştırıyor, gizli değerleri kim giriyor, geri almayı kim yapıyor? | Kurum (DevOps) | Açık — `KURUMDAN-OGRENECEKLERIM.md` BÖLÜM 2 |

### Kapatılan sorular

| # | Soru | Cevap | Tarih |
|---|---|---|---|
| K1 | Talep ve iş emri ayrı tablo mu, tek tablo mu? | **Tek tablo**, talep bir durumdur (⭐1) | 2026-08-26 |
| K2 | SLA saati ne zaman başlar? | **Talep açıldığı an** (⭐2) | 2026-08-26 |
| K3 | Personel bilgileri ayrı tabloda mı dursun? | **Tek `users` tablosu**; KVKK tablo ayrımı istemiyor, veri minimizasyonu istiyor → ev adresi toplanmıyor (⭐3) | 2026-08-26 |
| K4 | SLA süreleri ve takvim ne olacak? | Kritik 3sa (7/24) · Yüksek 8sa · Normal 24sa · Düşük 72sa (mesai); %50 hatırlatma, %80 escalation | 2026-08-26 (rehber E.4) |
| K5 | İş emri numarası biçimi? | `IE-2026-000148` — dizi (sequence) ile üretilir | 2026-08-26 (rehber E.9) |

---

## Bu belge nereye bağlanıyor

| Sonraki iş | Neyi buradan alıyor |
|---|---|
| **Veri modeli** (`veri-modeli-ve-sahte-veri-plani.md`) | §5'teki alanlar, iş kuralları ve §7'deki miktarlar |
| **Yol haritası** (rehber → BÖLÜM H) | Adım 3 veri modeli · Adım 6 durum makinesi · Adım 7 SLA |
| **Testler** | §5.5 geçiş tablosu ve §5.6 SLA tabloları doğrudan test senaryosudur |
| **Teslim dokümanı** `docs/sla-rules.md` | §5.6'nın tamamı ✅ §7 dokümantasyonu zorunlu tutuyor |
| **Sunum** | §6'daki uçtan uca akış, canlı incelemede anlatılacak omurga |

---

# Ek A — Her madde **neyle** yapılacak ve o araç **hangi sorunu** çözüyor

> **⚠️ Bu bölüm neden gövdede değil, ekte**
>
> PRD'nin gövdesi *"sistem ne yapacak"* sorusuna aittir; *"neyle yapacağız"*
> mühendislik kararıdır ve `proje-teknoloji-ve-plan.md` içinde gerekçeleriyle
> duruyor. ⛔ İkisini karıştırmak, iş kuralını teknolojiye bağımlı hâle getirir:
> aracı değiştirince gereksinim de değişmiş gibi görünür.
>
> ⭐ Ama ikisi arasında **köprü** olmadan PRD okunduğunda *"peki bunu ne
> yapacak"* sorusu havada kalıyor. Bu ek o köprüdür: her PRD maddesi →
> hangi araç → o araç burada **tam olarak neyi** çözüyor → rehberde nerede
> anlatılıyor → hangi yapım adımında kodlanıyor.

## A.1 Modül modül: araç ve çözdüğü sorun

| PRD maddesi | Araç | ⭐ Burada hangi sorunu çözüyor | Rehber | Adım |
|---|---|---|---|---|
| **§5.1** Giriş, oturum yenileme | `@nestjs/jwt` | Her istekte veritabanına "bu kim" diye sormamak için kimliği **imzalı bir kartta** taşır; imza bozulursa sunucu anlar | C.13 | 4 |
| **§5.1** Şifre saklama | **argon2id** | Veritabanı sızarsa şifreler ele geçmesin: özet **geri çevrilemez** ve kasten yavaştır — saldırgan saniyede milyon deneme yapamaz | C.13 | 4 |
| **§3** Yetki matrisi | **NestJS Guard** | Yetki kontrolünü uç noktaların içine dağıtmamak için: kapıda tek yerde sorulur, 50+ uç noktaya kopyalanmaz (§9 DRY) | C.1 | 4 |
| **§4** Audit alanları | **Prisma eklentisi + `nestjs-cls`** | "Kim oluşturdu/güncelledi" alanlarını her serviste elle doldurmak er geç unutulur; merkezî katman doldurunca **unutma ihtimali kalkar**. `nestjs-cls` aktif kullanıcıyı katmanlara parametre geçmeden taşır (⛔ global değişken kullanılsaydı iki kullanıcının verisi karışırdı) | C.16 · E.9 | 4 |
| **§5.2 · §5.3** Alan kuralları (uzunluk, zorunluluk, enum) | **Zod** (`packages/contracts`) | Aynı kuralı hem tarayıcıda hem sunucuda yazmamak için: **tek şema**, iki yerde çalışır. Ayrıca TypeScript tipi ve Swagger dokümanı aynı şemadan üretilir | C.4 | 5 |
| **§5.2** Pasif lokasyon kuralı · **§5.3** kullanım dışı varlık kuralı | **Kurallar katmanı** (saf TypeScript) | Bunlar biçim değil **iş kuralı**: "başlık boş mu" Zod'un işi, "bu varlığa iş emri açılabilir mi" domain'in işi. Ayrı katmanda olduğu için mobilden veya arka plan işinden gelen istekte de çalışır | E.1 · B.3 | 5–6 |
| **§5.2 · §5.3** İlişkiler (varlık → lokasyon) | **PostgreSQL foreign key** | Uygulama katmanı atlansa bile olmayan lokasyona varlık bağlanamaz — koruma **tek katmanda değil** | C.5 | 3 |
| **§5.4** İş emri numarası | **PostgreSQL sequence** | İki istek aynı anda gelirse "son numarayı bul, bir artır" **aynı numarayı** üretir (yarış koşulu). Dizi bunu yapısal olarak imkânsız kılar | E.9 | 6 |
| **§5.5** Durum + geçmiş birlikte yazılsın | **`prisma.$transaction`** | "Ya ikisi birden ya hiçbiri". Durum değişip geçmiş yazılmazsa sistem doğru görünür ama **izi olmaz** — denetimde kaydın hiç olmamasından kötüdür | E.8 · C.3 | 6 |
| **§5.5** İki kişi aynı anda değiştirirse | **`version` kolonu** (iyimser eşzamanlılık) | Kayıp güncelleme: ikisi de eski hâli okur, ikincisi birincinin yazdığını sessizce ezer. Sürüm eşleşmezse güncelleme **hiç yapılmaz** ve 409 döner | E.8 · F | 6 |
| **§5.5** Geçiş kuralları | **Durum makinesi tablosu** (saf TypeScript, `packages/domain`) | Ödev §6: kurallar controller'a dağılmasın. Tablo hâlinde tutulunca yeni durum eklemek **satır eklemektir**, kontrol kodu değişmez; testi veritabanı gerektirmez | E.5 | 6 |
| **§5.5** Katman kuralı fiilen korunsun | **dependency-cruiser** | "Domain, Prisma'yı bilmesin" bir yorum satırı olarak kalırsa altı ay sonra çiğnenir. Bu araç kuralı **CI'da build'i durdurarak** zorlar | C.8 | 2 · 13 |
| **§5.6** SLA politikası seçimi | **Factory Pattern + NestJS çoklu sağlayıcı** | Yeni SLA kuralı gelince çalışan koda dokunmamak için: her kural ayrı sınıf, factory listeden ilk uyanı seçer. ⛔ Factory hiçbir yerden kendisi arama yapmaz — ödevin yasakladığı Service Locator budur | E.4 · E.2 | 7 |
| **§5.6** "Şu an" bilgisi | **`Clock` soyutlaması** | Sistem saati doğrudan okunursa "SLA doldu mu" testi yazılamaz. Saat servisten gelince testte **sahte saat** verilebilir (ödev §8 bunu şart koşuyor) | E.4 | 7 |
| **§5.6** Mesai takvimi ve tatiller | **Takvim hesaplayıcı + `holidays` tablosu** | 17:00'de açılan 8 saatlik bilet gece 01:00'de ihlal olmasın. ⭐ Tatil **veri** olduğu için tabloda: yıl değişince kod değiştirip yeniden yayına çıkmak gerekmez | E.4 | 7 |
| **§5.8** Mükerrer bildirim | **Benzersiz index (`dedupe_key`)** | Uygulamada "var mı diye bak, yoksa ekle" iki iş aynı anda çalışınca kaybeder — ikisi de "yok" görür. Yarışı **veritabanı** çözer | C.5 · E.9 | 9 |
| **§5.9** Dört arka plan işi | **BullMQ + Redis + ayrı worker süreci** | Kimse ekranı açmasa da çalışması gereken işler var (gece 3'te SLA ihlali). Kuyruk "şunu 2 saat sonra yap" ve "şunu 5 dakikada bir yap" demeyi sağlar; ayrı süreç API'yi yavaşlatmaz | C.6 | 8 |
| **§5.9** İşler iki kez çalışsa da bozulmasın | **İş anahtarı + benzersiz index + kimlikle çalışma** | Sunucu yeniden başlar veya iş yeniden denenirse aynı bildirim tekrar üretilebilir. Üç katman: kuyrukta benzersiz iş anahtarı, veritabanında benzersiz index, ve iş **yalnızca kimlik alır** → durumu kendisi okur, iş emri kapanmışsa hiçbir şey yapmaz | C.6 | 8 |
| **§4** Sunucu tarafı filtre/sıralama/sayfalama | **Prisma `where`/`skip`/`take`** | Bütün kayıtları belleğe alıp filtrelemek 10 bin kayıtta saniyeler kaybettirir (ödev §17 açıkça yasaklıyor) | E.10 | 5 · 11 |
| **§4** Metin araması | **`pg_trgm` + GIN index** | `LIKE '%metin%'` index kullanamaz, tüm tabloyu tarar. Trigram index kısmi eşleşmeyi **index üzerinden** bulur | C.5 | 11 |
| **§5.10** Pano sayıları | **Prisma toplama sorguları + günlük özet tablosu** | Sayım **veritabanında** yapılır; ayrıca dünün özeti önceden hesaplanıp saklandığı için pano her açılışta ağır sorgu koşturmaz | E.10 | 9 |
| **§5.4 · §22** Ekranlar | **Next.js App Router** | Yönlendirme, sayfa üretimi ve paketleme tek çatıda; ödevin istediği React + Vite + React Router üçlüsünün yaptığı işi tek araç yapıyor | C.2 · G.1 | 10–12 |
| **§4** "Hiçbir ekran doğrudan istek atmaz" | **TanStack Query + ortak API katmanı** | Aynı istek kodunun her ekrana kopyalanmasını önler; ayrıca önbellek, yeniden deneme, "işlem başarılı → listeyi tazele" davranışını tek yerden verir (ödev §22) | C.19 · G.2 | 10 |
| **§5.4** Formlar | **React Hook Form + Zod resolver** | Form doğrulaması backend ile **aynı şemayı** kullanır → kural iki kez yazılmaz, ikisi ayrışamaz | C.20 | 12 |
| **§4** Hata biçimi | **NestJS Exception Filter** | Her uç noktada `try/catch` tekrarını kaldırır; hata türü → HTTP kodu dönüşümü **tek yerde** yaşar (ödev §19) | E.7 | 5 |
| **§8** Loglama | **`nestjs-pino`** | JSON log + iz kimliği (correlation ID): bir kullanıcının şikâyet ettiği isteği binlerce satır arasından tek sorguyla bulmak için. ⛔ Şifre/jeton maskelenir | C.15 | 2 |
| **§8** Sağlık kontrolü | **`@nestjs/terminus`** | `/health/live` "süreç ayakta mı", `/health/ready` "veritabanına bağlanabiliyor mu" — Docker'ın konteyneri ne zaman trafiğe açacağını bilmesi için | C.17 | 2 |
| **Testler** | **Vitest · Testcontainers · Playwright** | Birim testleri kuralları saniyenin altında doğrular; entegrasyon testleri **gerçek PostgreSQL konteynerinde** koşar (sahte veritabanı benzersizlik ve foreign key uygulamaz → testler yeşil yanar, canlı patlar); Playwright ekranı gerçek tarayıcıda tıklar | C.7 · C.11 · C.12 | Her adım · 13 |
| **§11 teslim** DBML şeması | **`@dbml/cli`** | `docs/database.dbml` elle yazılırsa ilk şema değişikliğinde eskir. Migration'ın ürettiği **gerçek şemadan** üretilir, CI farkı görürse build kırmızı yanar | C.21 | 3 |
| **Teslim** | **Docker Compose** | Değerlendirmeci tek komutla dört servisi ayağa kaldırabilsin (ödev §24) | C.10 | 2 · 15 |

## A.2 Bir isteğin yolculuğu — hangi PRD kuralı **nerede** uygulanıyor

⭐ Aşağıdaki hat, §6'daki akışın **teknik karşılığıdır**. Soldaki her durak bir
yazılım katmanı, sağdaki her not o durakta uygulanan **PRD kuralı**.

```
 TARAYICI  ─ Next.js + React Hook Form
    │        · form doğrulaması: packages/contracts'taki AYNI Zod şeması
    │        · rol'e göre düğme gizleme  → §3  (⚠️ bu güvenlik DEĞİL, kolaylık)
    ▼
 API KAPISI ─ NestJS controller
    │        · Guard: jeton geçerli mi, rol yetiyor mu   → §3 yetki matrisi
    │        · Guard: kullanıcı pasif mi                 → §5.1
    │        · Zod: alan biçimi, uzunluk, enum           → §5.4 alan tablosu
    │        ⛔ İŞ KURALI YOK — ödev §8: "controller'da iş kuralı bulunmamalıdır"
    ▼
 UYGULAMA SERVİSİ ─ use case
    │        · lokasyon aktif mi                          → §5.2 kural 1–2
    │        · varlık iş emri kabul ediyor mu             → §5.3 kural 1–2
    │        · atanan kişi aktif ve teknik personel mi    → §5.5 koşul tablosu
    │        · SLA Factory → hangi politika, üç zaman     → §5.6
    ▼
 DOMAIN ─ packages/domain (saf TypeScript, Prisma'yı TANIMAZ)
    │        · durum geçişi izinli mi                     → §5.5 geçiş tablosu
    │        · çözüm açıklaması / iptal gerekçesi zorunlu → §5.5 koşullar
    ▼
 TRANSACTION ─ prisma.$transaction   ⭐ "ya hepsi ya hiçbiri"
    │        · iş emri satırı  +  olay satırı BİRLİKTE    → §5.5 kural 3
    │        · version eşleşmiyorsa güncelleme YOK → 409  → §5.5 kural 6
    ▼
 POSTGRESQL
    │        · sequence → iş emri numarası                → §5.4
    │        · foreign key → olmayan varlığa bağlanamaz   → §5.3 kural 4
    │        · unique index → mükerrer bildirim olamaz    → §5.8
    ▼
 KUYRUK ─ BullMQ (Redis)
    │        · hatırlatma (%50) ve escalation (%80) işleri → §5.6
    │        · iş yalnızca KİMLİK taşır, durumu kendi okur → §5.9
    ▼
 WORKER ─ ayrı süreç
             · iş emri kapanmışsa hiçbir şey yapmaz        → §5.9 iş 1
             · bildirim satırı yazılır (dedupe_key ile)    → §5.8
```

⛔ **Bu hattın tersi yasak:** domain katmanı yukarıdaki hiçbir kutuya
bağımlı olamaz — bağımlılık **hep aşağıdan yukarı** okunur. Kural
`dependency-cruiser` ile CI'da zorlanıyor; yorum satırı değil, **kapı**.

---

# Ek B — Hangi sırada yapılacak ve **neden o sırada**

> ⭐ Sıranın tamamı ve dört kuralı `proje-teknoloji-ve-plan.md` → **BÖLÜM H**
> içinde. Buradaki tablo o planın **PRD karşılığıdır**: her adımda bu belgenin
> hangi maddesi hayata geçiyor ve o adım ne zaman "bitti" sayılıyor.

**Sıra neden bu:** ① bir şey, dayandığı şeyden **sonra** gelir · ② her modülü
kesip geçen işler (kimlik, hata, sayfalama, audit) **erken** yapılır, yoksa
yazılmış her modüle geri dönülür · ③ en **riskli** parça (durum makinesi, SLA)
erkene alınır, geç kalırsa geri dönüş pahalıdır · ④ tekrarlanacak kalıp en
**basit** modülde oturtulur.

| Adım | Ne kuruluyor | PRD'nin hangi maddesi | Bitti sayılır |
|---|---|---|---|
| **0** | Ortam (Node, pnpm, Docker, Git) | — | Sürüm komutları cevap veriyor |
| **1** | **PRD** — bu belge | Tamamı | ❓ satırları kapandı |
| **2** | İskelet: boş ama çalışan dört servis | §8 kurulabilirlik | `docker compose up --build` dördünü kaldırıyor · `/health/ready` yeşil |
| **3** | **Veri modeli** + tohumlama | §5.2–§5.10 alan tabloları · §7 | Migration boş veritabanına uygulanıyor · DBML kontrolü yeşil |
| **4** | Kimlik doğrulama, roller, audit | §5.1 · §3 · §4 | Yanlış şifre 401 · yetkisiz 403 · pasif kullanıcı giremiyor · jeton yenileme eskisini iptal ediyor |
| **5** | Lokasyon ve varlık modülleri | §5.2 · §5.3 | Pasif lokasyonda varlık açılamıyor · kalıplar (sayfalama, hata filtresi, şema) oturdu |
| **6** | **İş emri çekirdeği + durum makinesi** | §5.4 · §5.5 · §5.7 | Geçersiz geçiş reddediliyor · kapalı iş emri değişmiyor · eşzamanlı değişiklikte biri 409 alıyor |
| **7** | **SLA + Factory Pattern** | §5.6 | Her politika ayrı test edilmiş · factory'nin seçimi test edilmiş · yeni politika eklemek mevcut kodu değiştirmiyor |
| **8** | Dört arka plan işi | §5.9 | İş iki kez çalışsa tek bildirim · kapalı iş emrinde hiçbir şey yapmıyor |
| **9** | Bildirimler + pano sayıları | §5.8 · §5.10 | Aynı olay iki kez işlense tek bildirim · sayılar veritabanında hesaplanıyor |
| **10** | Arayüz temeli: giriş, korumalı sayfalar, ortak API katmanı | §5.1 · §3 · §4 | Girişsiz kullanıcı korumalı sayfaya giremiyor · jeton sessizce yenileniyor |
| **11** | İş emri listesi | §4 · §5.4 | Filtreli liste adresi paylaşılınca aynı sonucu açıyor · azami sayfa boyutu çalışıyor |
| **12** | Detay, düzenleme, yorum, bildirim ekranları | §5.4 ekran tablosu · §5.7 | 12 ekranın hepsi çalışıyor · backend hataları kullanıcıya görünüyor |
| **13** | Test tamamlama | §5.5 · §5.6 doğrudan test senaryosu | Ödev §23'teki senaryolar test edilmiş · koruma testleri **kaldırılıp kırmızıya döndüğü görülerek** kanıtlanmış |
| **14** | Dokümantasyon, ADR'ler, `AI_USAGE.md` | §5.6 → `docs/sla-rules.md` | Sekiz doküman var · en az üç ADR yazılmış · bilinen eksikler listeli |
| **15** | Teslim paketi | §8 | Temiz makinede tek komutla ayağa kalkıyor |
| **16** | Öğrenilenlerin kite geri yazılması | — | Kurallar kite işlendi |

⭐ **Oturum ritmi:** bir roadmap adımı = bir oturum. Plan → onay → kod → test →
commit → PR → `/clear`. ⛔ Tek oturumda bitirmeye çalışmak bağlamı doldurur ve
kaliteyi düşürür.

⚠️ **Testler 13. adımda *tamamlanıyor*, 13. adımda *başlamıyor*.** Her adımın
testi o adımda yazılır; sona bırakılan test çoktan çalışan kodu onaylamaktan
ibaret kalır, hata bulmaz.

---

# Ek C — Ödev maddesi → bu PRD'de nerede karşılandı

⭐ Teslimden önce bu tablo **kontrol listesi** olarak kullanılır: ödevin hiçbir
maddesi cevapsız kalmasın.

| Ödev | Ne istiyor | PRD karşılığı |
|---|---|---|
| §4 | Dört rol, yetkiler ayrılmış | §3 yetki matrisi |
| §5.1 | JWT, yenileme jetonu, rol bazlı yetki, pasif kullanıcı engeli | §5.1 |
| §5.2 | Lokasyon CRUD + aktif/pasif + pasif lokasyon kuralı | §5.2 |
| §5.3 | Varlık CRUD + filtre + kritiklik + operasyonel durum + kullanım dışı kuralı | §5.3 |
| §5.4 | 13 zorunlu alan + okunabilir benzersiz numara | §5.4 |
| §6 | Yedi durum + geçiş kuralları + geçmiş + atomiklik | §5.5 |
| §7 | Factory Pattern + üç zaman + öncelik/kritiklik/tür + dokümantasyon | §5.6 |
| §11 | Veri modeli tasarımı (bize bırakılmış) | `veri-modeli-ve-sahte-veri-plani.md` |
| §15 | Dört arka plan işi, idempotent | §5.9 |
| §16 | Beş olayda bildirim + okundu yönetimi + mükerrer engeli | §5.8 |
| §17 | On filtre + sunucu tarafı sayfalama + cevap zarfı | §4 |
| §18 | Biçim doğrulaması + iş kuralı doğrulaması ayrımı | §4 · §5.2 · §5.3 · §5.5 |
| §19 | Merkezî hata yönetimi, sekiz hata türü | §4 hata biçimi |
| §20 | Transaction sınırları + iyimser eşzamanlılık | §5.5 kural 3 ve 6 |
| §21 | Audit alanları merkezî | §4 audit satırı |
| §22 | 12 ekran + liste/detay davranışları | §5.4 ekran tablosu |
| §23 | Unit + integration + architecture testleri | Ek B adım 13 |
| §24 | Docker Compose ile dört servis | §8 kurulabilirlik |
| §25 | İki sağlık ucu + yapılandırılmış log | §8 · Ek A |
| §29 | Sekiz doküman + en az üç ADR | Ek B adım 14 |
| §31 | Canlı incelemede her kararın savunulması | Bu belgedeki her ⭐ satırının gerekçesi |
