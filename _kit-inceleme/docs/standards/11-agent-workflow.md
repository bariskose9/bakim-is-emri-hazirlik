# 11 — Ajanla Çalışma Düzeni

Bu proje **vibecoding** ile geliştirilir: kullanıcı kodu elle yazmaz ve
kodun tamamını okuyup doğrulayamaz. Bu nedenle süreç disiplini kodun yerine geçer.

## Oturum düzeni
**Bir oturum = bir feature.** Birden fazla sayfayı aynı oturumda karıştırma.
Oturum başında: `git status` temiz mi, hangi daldayız, PRD'de bu feature ne diyor.

## Aşamalar

Komut adları **tam yazılır**. Ortamda aynı adı taşıyan başka skill paketleri
olabilir (`/spec`, `/review`, `/plan` birden fazla pakette bulunur); bu projede
kastedilen **her zaman `agent-skills:` önekli olanlardır** (CLAUDE.md §2).

```
/agent-skills:spec   → gereksinimi netleştir (interview-me ile soru sor)  [ONAY]
/agent-skills:plan   → 2-5 dakikalık küçük adımlara böl                   [ONAY]
                     → yeni dal aç
/agent-skills:build  → adım adım kodla, her adımda test yaz
/agent-skills:test   → unit + entegrasyon + E2E yeşil olmalı
                     → güvenlik denetimi (security-auditor)
                     → tarayıcıda fiilen tıklayarak doğrula
/agent-skills:review → code-reviewer ile denetle
                     → commit raporu sun                                  [ONAY]
                     → commit + push + PR
```

**Skill etiketi gerçek olmalı:** CLAUDE.md §2 gereği her cevabın ilk satırında
kullanılan skill bildirilir. Etiketi yazmak yetmez — skill **fiilen yüklenip
uygulanır**. Yüklenmeden yazılan etiket yanlış beyandır.

## Bağlam yönetimi
- Uzun oturumda bağlam kirlenir. Feature bitince oturumu kapat, yeni oturum aç.
- Konu değiştiğinde `/clear` kullan.
- Her oturum başında CLAUDE.md ve ilgili PRD bölümü yeniden okunur.

## Belirsizlikte davranış
Varsayım yapma. Sor. Yanlış varsayımla yazılmış 200 satır,
sorulmuş 1 sorudan pahalıdır.

## Devralınan kaydın ÖNERDİĞİ ÇÖZÜM de bir iddiadır

`06-testing.md` bir kaydın **sebebinin** doğrulanmadan devralınamayacağını
söylüyor. Bir adım ötesi de geçerli: teknik borcun, devir notunun veya ADR'nin
**"şöyle çözülür"** satırı da doğrulanmamış bir iddiadır — ve genellikle o iş
hiç yapılmadan, sebep henüz ölçülmemişken yazılmıştır.

Ölçülmüş üç sapma biçimi:

| Sapma | Ne olur |
|---|---|
| **Çözüm etkisiz** | Kayıt "A yerine B'yi oku" der; belge ikisinin **aynı** değer olduğunu söyler. İş yapılmış görünür, hiçbir şey değişmez |
| **İş zaten yapılmış** | Kayıt iki maddeden söz eder, biri önceki bir adımda çoktan yapılmıştır. Yapılmışı "yaptım" diye raporlamak, denetimi yalancı çıkarır |
| **Risk gerçek değil** | Kaydın anlattığı saldırı, platform veya kütüphane tarafından zaten engelleniyordur |

**Kural:** Bir borcu ödemeye başlarken **üçünü ayrı ayrı ölç** — (1) sorun bugün
hâlâ var mı, (2) sebep doğru mu, (3) önerilen çözüm o sebebi gerçekten
çözüyor mu. Üçü de doğrulanmadan koda dokunma.

Bir kaydı kapatırken, **yanlış çıkan kısmını da yaz.** "Ödendi" demek yetmez;
sonraki oturum aynı yanlış cümleyi yeniden devralır.

## Dış dünya bilgisi: ezberden DEĞİL, güncelinden

Ajanın eğitim verisi eskidir. Üçüncü parti paneller, API'ler, kütüphane sürümleri
ve fiyatlandırma **haftalar içinde** değişir. Ezberden verilen yönlendirme,
kullanıcıyı artık var olmayan bir ekranı aramaya gönderir.

**Kural:** Kullanıcıya bir dış servis hakkında "şuraya gir, şuna bas" demeden
**önce**, o bilgiyi bu oturumda güncel resmî kaynağından **bizzat gör**.

Bu kapsama girenler:
- Sağlayıcı panel gezinmesi (Google Cloud, Vercel, Cloudflare, Neon, Resend…)
- Kütüphane API'si, sürüm numarası, bakım durumu → paket kaydı + resmî doküman
- CLI komutu ve bayrakları
- Ücretsiz katman sınırları, fiyat, kota
- Ortam değişkeni adları ve zorunlulukları

Nasıl doğrulanır: `WebFetch` ile resmî doküman · `npm view` ile paket kaydı ·
`--help` ile CLI · sağlayıcının changelog'u. Ekran görüntüsü gerekiyorsa
`browser-testing-with-devtools` ile fiilen aç.

**Kaynağa ulaşılamıyorsa** "doğrulayamadım, ezberimden söylüyorum, arayüz
değişmiş olabilir" diye **açıkça yaz**. Sessizce tahmin etme.

**Neden bu kadar sert:** doğrulama ajan için saniyeler sürer; yanlış yönlendirme
kullanıcının onlarca dakikasını yakar ve güveni bozar. **Maliyet asimetriktir.**

Tipik ödenen bedeller (gerçek örneklerden):
- Sağlayıcı, ayarı başka bir menünün altına taşımıştır; kullanıcı **var olmayan
  bir sayfayı** arar ve "ben mi beceremiyorum" diye düşünür
- Panelde bir değer girilmiştir ama **kaydetme adımı** tarifte yoktur; hata
  saatlerce **kodda** aranır
- Bir kütüphanenin önerdiği yol değişmiştir; ezberden verilen kurulum,
  projenin yazılı bir kararını sessizce bozan bir paket getirir

## ⛔ GERÇEK PROJE VARSAYILANI — demo çözümü varsayılan olamaz

Bu proje bir **öğrenme projesi** olabilir; ama **öğrenilen şey gerçek üretim
pratiğidir.** İki seçenek arasında kalındığında ölçüt "hangisi daha hızlı
biter" veya "portföyde daha iyi görünür" değil, **"gerçek kullanıcısı ve
gerçek nöbetçisi olan bir üründe hangisi doğru olurdu"**dur.

**Kural:** Her teknik seçimde önce sektörde yerleşik pratiği tespit et ve
**varsayılan olarak onu uygula.** Ondan sapılacaksa sapma bilinçli, yazılı ve
gerekçeli olur — sessizce değil.

### Bu neden bir kural, tercih değil

Demo kısayolu tek başına zararsız görünür; zarar **birikince** çıkar. Kısayolla
yazılan kod, gerçek yüke, gerçek saldırgana ve gerçek nöbetçiye çarptığında
"düzeltilecek bir detay" değil **yeniden yazılacak bir katman** olur. Üstelik
kısayol öğrenilen alışkanlığı da bozar: yanlış refleks bir sonraki projeye
bedava taşınır.

### Sapma nasıl yazılır

Gerçek pratikten sapan her karar şu üçünü söyler:

1. **Yerleşik pratik ne?** (kaynağıyla — resmî doküman, RFC, sağlayıcı kılavuzu)
2. **Biz ne yapıyoruz ve neden?** (somut kısıt: ücretsiz katman sınırı, gerçek
   sağlayıcının olmaması, kapsam dışılık)
3. **Gerçeğine ne zaman ve nasıl geçilir?** (roadmap adımı veya teknik borç no)

Kısıt gerçekse sapma meşrudur. ⛔ **Meşru olmayan tek şey, sapmayı yazmamaktır** —
yazılmayan sapma, sonraki okuyucuya "burada doğru olan buymuş" diye görünür.

⚠️ **"Portföy projesi" bir sapma gerekçesi DEĞİLDİR.** Portföyün değeri tam da
gerçeğine benzemesindedir. Sahte olması gereken tek şey **veridir** (sahte kimlik
servisi, sahte ödeme, uydurma isimler); **mühendislik sahte olmaz.**

### Ölçütü tersinden oku

Bir kararı savunurken şu cümlelerden birini kuruyorsan dur ve yeniden düşün:

- "Nasıl olsa gerçek kullanıcı yok" → yarın var. Kod kalır
- "Bu sadece bir demo" → demo olduğu için değil, **doğru olduğu için** yapılır
- "Şimdilik böyle kalsın" → o hâlde teknik borç numarası nerede?
- "Zaten depo herkese açık" → açık kaynak olmak, saldırı yüzeyini genişletmek
  için gerekçe değildir. İkisi ayrı sorulardır

### ⛔ MÜHENDİSLİK SEÇİMİ KULLANICIYA DEVREDİLMEZ

"Belirsizlikte sor" kuralı **iş gereksinimi** içindir, mühendislik tercihi için
değil. İkisi farklı sorulardır ve karıştırılması kullanıcıya cevaplayamayacağı
bir soru sormak demektir:

| Soru tipi | Örnek | Kim cevaplar |
|---|---|---|
| **İş gereksinimi** | "Randevu iptali kaç saat öncesine kadar serbest?" · "Bu alan zorunlu mu?" | **Kullanıcı** — cevabı yalnızca o bilir |
| **Dış dünya** | "Bu panelde hangi anahtar tanımlı?" · "Bu maliyeti ödemek ister misin?" | **Kullanıcı** — sonucunu o üstlenir |
| **Mühendislik tercihi** | "Yol segmenti mi, başlık tabanlı sürümleme mi?" · "Hangi index?" · "Cursor mı offset mi?" | ⛔ **AJAN** — yerleşik pratikten türetilir |

**Kural:** Bir mühendislik sorusunda seçenekleri kullanıcıya menü olarak sunup
kararı ona bırakma. **Kararı sen ver**, ölçüt şudur:

> *Bu ürünü gerçekten kullanan bir kurum (bu projede: bir büyükşehir belediyesi)
> ve onun nöbetçi ekibi için, sektörde yerleşik pratik hangisini söylüyor?*

Sonra kararı **bildir** — gerekçesi, elenen alternatifi ve kaynağıyla. Bildirmek
onay istemek değildir; kullanıcı itiraz ederse karar değişir, itiraz etmezse
karar zaten yürürlüktedir.

⛔ **"ADR gerektirir" = "kullanıcıya sor" DEĞİLDİR.** ADR, kararın **yazıya
dökülmesini** şart koşar; kimin verdiğini değil. Bir kararı ADR'ye yazmak için
önce o kararı vermiş olman gerekir.

⚠️ **Ayırt edici test:** Soruyu cevaplamak için kullanıcının **kod okuması veya
sektör pratiğini bilmesi** gerekiyorsa, o soru ona sorulmamalıydı. Kullanıcı
"hangisi doğruysa o" diyorsa bu bir cevap değil, **sorunun yanlış sorulduğunun
kanıtıdır.**

## ⛔ AYNI BİLGİ İKİ YERDE YAZILMAZ — biri diğerine İŞARET EDER

Bir olgu (kural, gerekçe, sürüm, komut, port) **tek bir dosyada yaşar.** Başka
yerler onu tekrar anlatmaz; **oraya işaret eder.**

### Bu neden bir kural, üslup tercihi değil

Aynı bilgi iki yerde yazıldığında ikisi bir süre aynı kalır. Sonra biri
güncellenir, diğeri güncellenmez — ve **eskiyen kopya, tazesinden ayırt
edilemez.** Okuyan hangisinin doğru olduğunu bilemez; genellikle **önce
rastladığına** inanır.

Yani sorun "fazla yazı" değil, **sessizce yalan söyleyen bir belge üretmektir.**
Hiç yazılmamış olması, yanlış yazılmış olmasından iyidir — çünkü eksik bilgi
sorulur, yanlış bilgi sorulmaz.

Bir projede yaşandı: API sürümleme kuralı hem standartlara, hem plan dosyasına,
hem de anlatım dokümanına ayrı ayrı yazıldı. Standart güncellendiğinde diğer
ikisi geride kaldı ve sonraki oturum eski gerekçeyi savunmaya başladı.

### Uygulama

1. **Yazmadan önce ara.** Bir kavramı açıklamak üzereyken önce `grep` ile
   projede/kitte geçip geçmediğine bak. Geçiyorsa açıklamayı **oraya** yaz veya
   oradaki açıklamaya işaret et.
2. **Ev sahibi dosyayı seç:** konuyu **en dar kapsamda sahiplenen** dosya.
   Sürümler `00-stack.md`, API sözleşmesi `03-api-guidelines.md`, ortam
   değişkenleri `13-environments.md` gibi.
3. **İşaret biçimi belirli olsun:** `03-api-guidelines.md` → "Sözleşme ömrü".
   Sadece dosya adı vermek yetmez; **hangi başlık** olduğu yazılır, yoksa
   okuyan aramak zorunda kalır ve aramaz.
4. **Kopyalanmasına izin verilen tek şey: bir satırlık özet + işaret.**
   Tablo satırı olabilir; ama **gerekçe** tek yerde durur. Gerekçe kopyalanırsa
   ikisi ayrışır.

### Sınır — bu kural neyi YASAKLAMAZ

- **Aynı olgunun farklı okuyucuya farklı derinlikte anlatılması** yasak değildir.
  Standart dosyası kuralı koyar; anlatım/sunum dokümanı aynı kuralı kavram
  bilmeyen birine açar. Yasak olan **aynı derinlikte ikinci bir kopya**dır.
- Bu durumda bile **kaynak tektir:** anlatım dokümanı standarda işaret eder,
  standart anlatıma değil. Ok her zaman **kurala** doğru bakar.

⚠️ **Kendi ürettiğin dokümanlar da bu kurala tabidir.** Plan, rehber ve sunum
dosyaları çoğaldıkça aynı gerekçeyi üç kez yazmak en kolay yoldur; altı ay sonra
hangisinin güncel olduğunu kimse bilemez.

## Kapsam kontrolü
İstenmeyen iyileştirme yapma. "Bu arada şunu da düzelttim" yasak —
gördüğün sorunu **bildir**, ayrı iş olarak planla.

## Dışarıya giden doküman — anlatım standardı

Bir doküman kullanıcıdan **başkasına** gidiyorsa (sunum, teslim dosyası,
devir notu, README), aşağıdaki kurallar geçerlidir. Oturum içi anlatım için
"Öğretme yükümlülüğü" bölümü geçerlidir; ikisi karıştırılmaz.

### ⛔ Doküman, okuyucusunun veya yazarının bilgi seviyesini ELE VERMEZ

*"Kod bilgisi gerektirmeden yazıldı"*, *"basitçe anlatalım"*, *"yeni
başlayanlar için"* gibi ifadeler **yazılmaz.** Bu cümleler dokümanı okuyan üçüncü
kişiye, yazarın veya sahibinin ne bildiği hakkında bilgi verir — ve bu bilgi
onun aleyhine kullanılabilir.

Doküman, konuyu bilen biri tarafından yazılmış gibi durur; sadeliği bir
**tercih** olarak görünür, bir **ihtiyaç** olarak değil.

⚠️ Aynı sebeple *"senin için"*, *"anlaman için"* gibi kullanıcıya hitap eden
ifadeler de dışarı giden dokümanda bulunmaz. Doküman kimseye hitap etmez;
konuyu anlatır.

### Her kavram ÜÇ adımda açılır

Bir kavramın yalnızca teknik tanımını yazmak ezber üretir, anlayış üretmez.
Okuyucunun kavramı **kendi zihninde bir yere oturtabilmesi** için üç adım
zorunludur:

1. **Gerçek hayattan karşılığı** — çarpıcı ve akılda kalıcı bir benzetme
2. **Yazılımdaki tanımı** — sektörde kullanılan terimle
3. **BU projede tam olarak nerede** — hangi somut sorunu, hangi ekranda,
   hangi tabloda çözüyor

⛔ Üçüncü adım atlanamaz. *"Katmanlar birbirinden ayrılır"* cümlesi tek başına
hiçbir şey öğretmez; *"Prisma değişse yalnızca infrastructure katmanı etkilenir,
domain ve application dokunulmaz kalır"* öğretir.

### Kod görülmeden anlaşılmayacak her başlıkta kod bulunur

Bir tasarım deseni, bir kural veya bir mekanizma **kod olmadan havada
kalıyorsa**, kısa bir örnek konur (5–15 satır). Amaç kodu öğretmek değil,
iddiayı **gösterilebilir** kılmaktır.

Özellikle etkili olan kalıp: **yanlış hâli → neden yanlış → doğru hâli.**
Yalnızca doğru hâli göstermek, okuyucunun kendi kodundaki hatayı tanımasını
sağlamaz.

⚠️ Kod örneği **yorum satırıyla açıklanmaz**; çevresindeki metin açıklar. Kodun
içine "burada şunu yapıyoruz" yazmak, kodu okuyamayan için de okuyabilen için de
gürültüdür.

### Tekrar, anlaşılırlığı artırıyorsa serbesttir

Bu, "aynı bilgi iki yerde yazılmaz" kuralının istisnası **değildir**; sınırıdır.
Aynı **gerekçe** iki yerde yazılmaz. Ancak aynı kavram farklı bölümlerde farklı
açıdan ele alınabilir — biri tanımlar, diğeri o projedeki uygulamasını gösterir.

Ölçüt şudur: ikinci geçiş okuyucuya **yeni bir şey** katıyor mu? Katmıyorsa
tekrardır ve silinir; katıyorsa kalır ve ilkine işaret eder.

## Öğretme yükümlülüğü
Kullanıcı bu projeyle öğreniyor. Her adımdan sonra kod göstermeden,
Türkçe, en fazla 5 madde: ne yaptın, neden böyle yaptın, alternatifi neydi.
