# Teknik Sunum — Bakım ve İş Emri Yönetim Sistemi

> ⚠️ **BU BİR TASLAKTIR.** Proje henüz yazılmadı. Aşağıda **kararı verilmiş** olan
> her şey dolu; koda bağlı olan bölümler (`⏳` işaretli) her session bittiğinde
> doldurulacak. Amaç: sunumun en sona bırakılıp aceleye gelmemesi.

**Sunumun amacı iki katmanlı:** teslimden sonra canlı teknik inceleme var ve
sorular gelecek — ama asıl amaç **senin sistemi uçtan uca sahiplenmen.** Bu
dosya kod bilgisi gerektirmeden okunabilecek şekilde yazılıyor.

---

## 0. Sunum akışı — hangi sırayla anlatacaksın

Yaklaşık 25–30 dakikalık bir anlatım için sıra:

| # | Bölüm | Süre | Amacı |
|---|---|---|---|
| 1 | Proje ve karar çerçevesi | 2 dk | "Neye göre karar verdim" — geri kalan her şeyin zemini |
| 2 | Stack çevirisi (.NET → JS) | 4 dk | En çok soru gelecek yer, baştan açıklığa kavuşsun |
| 3 | Mimari ve katmanlar | 4 dk | Bağımlılık yönü ve nasıl **zorlandığı** |
| 4 | Veri modeli | 3 dk | Tablolar, ilişkiler, index kararları |
| 5 | **Bir iş emrinin hayatı** | 6 dk | ⭐ Sunumun kalbi — sistemi baştan sona canlı gösterir |
| 6 | Zor maddeler | 6 dk | Factory, durum makinesi, transaction, arka plan işleri, DI |
| 7 | Test ve teslim | 3 dk | Kalite kapıları ve Docker |
| 8 | Teknik borçlar | 2 dk | Dürüstlük — sorulmadan söylenir |

⭐ **5. bölüm neden kalbi:** Değerlendirmeci "mimariyi anlat" dediğinde çoğu aday
katman isimlerini sayar. Sen bunun yerine **tek bir isteğin yolculuğunu** baştan
sona anlatırsan, mimariyi *anlatmadan göstermiş* olursun.

---

## 0.5 Önce en temeli — bir web uygulaması nelerden oluşur

*Bu bölüm sunumda anlatılmayacak; **senin** her şeyi yerli yerine oturtman için.
Buradaki zihin haritası oturursa geri kalanı kendiliğinden anlaşılır.*

### İki ayrı bilgisayar

Her web uygulamasında iş **iki yerde** yapılır:

| | **İstemci (client)** | **Sunucu (server)** |
|---|---|---|
| Neresi | Kullanıcının tarayıcısı / telefonu | Senin kontrolündeki, kapanmayan bilgisayar |
| Ne yapar | Ekranı çizer, tıklamayı alır | Karar verir, veritabanına gider |
| Güvenilir mi | **Hayır** | Evet |

**"İstemciye güvenilmez" ne demek:** Kullanıcı tarayıcıda geliştirici araçlarını
açıp gönderdiği veriyi değiştirebilir. "Ben yöneticiyim" diye veri gönderebilir.
Bu yüzden *"bu kişi gerçekten yönetici mi"* kararı **her zaman sunucuda** verilir.
Ekrandaki "Sil" düğmesini gizlemek bir **kolaylıktır**, güvenlik değil.

### Bir isteğin yolculuğu

Kullanıcı "İş Emirleri" ekranını açtığında olanlar:

```
1. Tarayıcı              "bana açık iş emirlerini ver"
        ↓ (internet)
2. API sunucusu          "sen kimsin? yetkin var mı?"
        ↓
3. İş kuralları          "bu kullanıcı sadece kendi lokasyonunu görebilir"
        ↓
4. Veritabanı            SELECT ... WHERE ...
        ↓
5. API sunucusu          sonucu JSON'a çevirir, fazla alanları kırpar
        ↓ (internet)
6. Tarayıcı              gelen veriyi tabloya çizer
```

Sunumda anlatacağın her teknoloji, bu altı adımdan **birinde** çalışıyor.
Hangisinde çalıştığını bilmek, o teknolojiyi anlamanın yarısıdır.

### Her teknoloji hangi adımda — tek tabloda

| Teknoloji | Hangi adım | En sade tanımı | Günlük hayattan benzetme |
|---|---|---|---|
| **React** | 6 | Veriye göre ekranı çizen kütüphane | Vitrin düzenleyicisi |
| **Next.js** | 6 (+1) | React'i çalışır siteye dönüştüren framework; sayfaları da sunucuda hazırlar | Vitrini kuran **ve** mağazayı açan ekip |
| **TypeScript** | Hepsi | JavaScript + tip kontrolü. Hatayı çalışmadan **önce** yakalar | Yazım denetimi |
| **NestJS** | 2–5 | Sunucu tarafı uygulama çatısı | Bir ofisin organizasyon şeması |
| **Express** | 2 | Gelen isteği karşılayan temel katman. *Nest zaten bunu kullanır* | Santral görevlisi |
| **Prisma** | 4 | Kod ile veritabanı arasındaki tercüman | Tercüman |
| **PostgreSQL** | 4 | Verinin durduğu yer | Arşiv/dolap |
| **Zod** | 2 ve 5 | "Gelen veri doğru biçimde mi?" kontrolü | Form denetleyicisi |
| **JWT** | 2 | Giriş yapan kullanıcıya verilen dijital kimlik kartı | Otel kartı |
| **argon2** | 2 | Şifreyi geri çevrilemez şekilde karıştırır | Kağıt öğütücü |
| **BullMQ** | — | Sonra/düzenli yapılacak işlerin listesi | Yapılacaklar defteri |
| **Redis** | — | Çok hızlı, geçici hafıza. BullMQ'nun defteri burada durur | Not kağıdı |
| **Docker** | — | Uygulamayı ihtiyaçlarıyla birlikte tek pakete koyar | Hazır yemek kabı |
| **Vitest / Playwright** | — | Testler: "hâlâ çalışıyor mu?" | Kalite kontrol |
| **Swagger** | — | API'nin kullanma kılavuzu, kendiliğinden üretilir | Kullanım kılavuzu |
| **Expo** | 6 | Aynı React bilgisiyle telefon uygulaması yazma aracı | — |

### Sık karıştırılan üç çift

**1. Kütüphane ile framework**
Kütüphaneyi **sen çağırırsın**, framework **seni çağırır.** React bir
kütüphanedir — sen "şunu çiz" dersin. Next.js ve NestJS framework'tür — kuralları
onlar koyar, sen boşlukları doldurursun. Framework daha kısıtlayıcıdır ama
karşılığında düzen verir; on kişilik ekipte bu düzen her şeydir.

**2. Frontend ile backend**
Frontend = kullanıcının **gördüğü**. Backend = kullanıcının **göremediği** ama
işi yapan. Bizde frontend Next.js, backend NestJS.

**3. Veritabanı ile ORM**
Veritabanı (PostgreSQL) verinin durduğu yer. ORM (Prisma) oraya konuşan
tercüman. ORM olmadan da olur — SQL'i elle yazarsın; ama o zaman tip güvenliğini
ve migration yönetimini kendin kurarsın.

### Bir cümlelik özet — sunumda bunu söylersen sistemi anlatmış olursun

> "Kullanıcı **Next.js** ile yazılmış ekranı açar. Ekran, **NestJS** ile
> yazılmış API'ye istek atar. API gelen veriyi **Zod** ile doğrular, kullanıcının
> yetkisini **JWT** ile kontrol eder, iş kurallarını uygular ve **Prisma**
> üzerinden **PostgreSQL**'e gider. Sonuç JSON olarak döner. Kullanıcıyı
> bekletmemesi gereken işler **BullMQ** ile kuyruğa alınır ve ayrı bir worker
> süreci onları yapar. Hepsi **Docker** ile paketlenir, tek komutla ayağa kalkar."

---

## 1. Proje ve karar çerçevesi

### Ne yaptık

Bir kurumun lokasyonlarındaki cihaz, ekipman ve araçların **arıza ve bakım
süreçlerini** yöneten web uygulaması. Talep açılır → iş emrine dönüşür →
teknik personele atanır → durumu takip edilir → SLA süresi işler → süre
yaklaşınca ve aşılınca sistem kendiliğinden uyarır.

### Kararları neye göre verdim — sunumun zemini

Kurum "istediğin stack'i kullan" dedi. Ölçütüm dört başlıktı:

1. **Piyasada yaygın ve aktif bakımda olması** — bu sistemi yıllarca başkaları
   da sürdürecek
2. **Gerçek hayat pratiği** — demo kısayolu değil, gerçek kullanıcısı olan bir
   üründe doğru olan
3. **Devralınabilirlik** — projeyi ilk kez gören biri nereye bakacağını bilmeli
4. **Ödevin istediği her yeteneğin karşılanması** — araç değişse de yetenek düşmez

**Üstün gelen kural:** *Ödev metni de olsa, teknolojiyi yapan şirketin resmî
önerisi de olsa — yaygın ve bakımdaki best practice kazanır. Sapma yasak değil,
**açıklamasız** sapma yasak.*

### Kararların dört kutusu

Anlatırken her kararın hangi kutuda olduğunu söyleyeceğim:

| Kutu | Anlamı | Örnek |
|---|---|---|
| **1** | Ödev istedi, zaten doğrusuydu | PostgreSQL, Docker, Factory Pattern, optimistic concurrency |
| **2** | Ödev istedi, JS eşleniğini kullandım | EF Core→Prisma, Hangfire→BullMQ, FluentValidation→Zod |
| **3** | Ödev istemedi, gerçek hayat gerektirdi | Renovate, mimari testi, correlation ID, `/api/v1` |
| **4** | Ödev istedi, yapmadım — şunu tercih ettim | AutoMapper yerine Zod+Prisma `select` |

⚠️ 4. kutudaki her madde **ölçüyle** desteklenir (indirme sayısı, son yayın
tarihi, sürüm kısıtı) — "bence daha iyi" denmez.

---

## 2. Stack çevirisi — ödev ne istedi, ben ne kullandım

| Ödevdeki zorunlu | Benim seçimim | Kutu | Tek cümlelik gerekçe |
|---|---|---|---|
| .NET + ASP.NET Core Web API | **NestJS 11** | 2 | Node'da DI konteyneri, modül sistemi ve Guard/Interceptor/Pipe/Filter'ı olan tek yaygın çatı |
| C# | **TypeScript (strict)** | 2 | Derleme zamanı tip güvenliği |
| Entity Framework Core | **Prisma 7** | 2 | Şema tek dosyada okunur; migration motoru + tip güvenli projeksiyon |
| PostgreSQL | **PostgreSQL 18** | 1 | Değişmedi |
| FluentValidation | **Zod 4** | 2 | Tek şema hem doğrular hem TS tipini hem OpenAPI'yi üretir |
| Hangfire | **BullMQ + Redis** | 2 | Node'un fiili standardı; gecikmeli + tekrarlayan iş, retry, yatay ölçekleme |
| AutoMapper / Mapster | **Zod şeması + Prisma `select`** | **4** | Ölçüldü: aday kütüphaneler ya bakımsız ya niş — §3.9 |
| Swagger / Scalar | **`@nestjs/swagger` → Swagger UI** | 1 | Zod şemasından OpenAPI üretiliyor, elle senkron yok |
| React + Vite + React Router | **Next.js 16 + React 19** | 2 | Next, Vite ve React Router'ın işini tek üründe topluyor — §3.2 |
| xUnit | **Vitest 4** | 2 | Hızlı, ESM-yerel |
| Integration test | **Testcontainers** | 1 | Gerçek PostgreSQL konteyneri — sahte veritabanı yasak, §6.6 |
| (yoktu) | **dependency-cruiser** | 3 | NetArchTest karşılığı: katman kuralları CI kapısı olur |
| (yoktu) | **Renovate** | 3 | Sürüm takibini bota devreder |

---

## 3. Teknoloji kartları

Her teknoloji altı sabit başlıkla. *(Aşağıda örnek olarak dolduruldu; kalanlar
aynı şablonla — liste §3.12'de.)*

### 3.1 NestJS

**Nedir:** Node.js için sunucu tarafı uygulama çatısı. Express'in **üstünde**
çalışır.
**Ne işe yarar:** API uçlarını, iş kurallarını ve yetkilendirmeyi düzenli bir
yapıda barındırır. Modül sistemi, bağımlılık enjeksiyonu (DI), Guard (yetki),
Interceptor (log/denetim), Pipe (doğrulama), Filter (merkezî hata) getirir.
**Bu projede nerede:** Tüm API (`apps/api`) ve arka plan işçisi (`apps/worker`).
**Neden tercih ettim:** Ödevin §14'ü servis yaşam döngüsü tablosu istiyor
(Transient/Scoped/Singleton). Node tarafında bu soruya cevap verebilen tek
yaygın çatı Nest. Çıplak Express'te lifetime kavramı **yoktur**; disipline kalır.
**Alternatifi neden değil:** Çıplak Express — 5–10 uçlu tek amaçlı serviste
doğru, ama 50+ uçlu kurumsal sistemde her geliştirici kendi düzenini icat eder
ve kod tabanı 1–2 yılda dağılır. *Not: Nest zaten Express kullanır, ikisi rakip
değil.*
**Ödevdeki karşılığı:** ASP.NET Core Web API. Modül≈Area, Guard≈`[Authorize]`,
Filter≈`ExceptionFilter`, Pipe≈model binding + validation. **Kutu 2.**

### 3.2 Next.js

**Nedir:** React'in framework'ü.
**Ne işe yarar:** React tek başına bir web sitesi değildir; "veriye göre ekranı
çiz" işini yapan bir kütüphanedir. Ayakta durması için **paketleyici** (kodu
tarayıcının anlayacağı hâle getiren) ve **yönlendirici** (adres→ekran eşlemesi)
gerekir. Next ikisini de içinde barındırır.
**Bu projede nerede:** Tüm arayüz (`apps/web`) — 12 ekran.
**Neden tercih ettim:** Ödev üç ayrı ürün istemişti (React + Vite + React
Router); Next üçünün işini tek üründe topluyor ve **sürüm uyumunu üretici
garanti ediyor.** Üç paketin uyumunu yıllarca elle takip etmek, devralan
geliştiriciyi zorlar.
**Alternatifi neden değil:** Vite + React Router — küçük, tamamen istemci
taraflı bir panelde daha hafif ve öğrenmesi kolay. Bu proje uzun ömürlü ve
sunucu tarafı işleri var.
**Ödevdeki karşılığı:** React + Vite + React Router. **React yine
kullanılıyor** — Next onun framework'ü, yani "React ile frontend" şartı
doğrudan karşılanıyor. **Kutu 2.**

### 3.3 Prisma

**Nedir:** ORM — veritabanıyla konuşmayı yapan katman.
**Ne işe yarar:** Üç iş: (1) şema tanımı, (2) o şemadan SQL üretip veritabanına
uygulamak (migration), (3) kod içinden tip güvenli sorgu.
**Bu projede nerede:** Yalnızca Nest tarafında. Next Prisma'yı **hiç görmez** —
veritabanına tek kapıdan girilir.
**Neden tercih ettim:** Şema tek dosyada okunur. EF Core'da veri modelini görmek
için 30 sınıf gezersin; Prisma'da `schema.prisma` açarsın, tüm model önündedir.
Devralınabilirlik açısından belirleyici. Ayrıca 13.9M indirme/hafta.
**Alternatifi neden değil:** TypeORM (3.2M/hafta) — EF Core'a daha çok benzer
(sınıf + dekoratör + repository), .NET'ten geçen için tanıdık. Ama şema
sınıflara dağılır ve `synchronize` özelliği üretimde veri kaybettirdiği için
kötü üne sahip.
**Ödevdeki karşılığı:** Entity Framework Core. `prisma migrate`≈`Add-Migration`,
`findMany`≈`DbSet.Where`, `select`≈`ProjectTo`. **Kutu 2.**

### 3.4 PostgreSQL

**Nedir:** İlişkisel veritabanı.
**Ne işe yarar:** Veriyi tablolarda tutar, ilişkileri ve tutarlılığı **kendisi**
garanti eder (foreign key, unique constraint, transaction).
**Bu projede nerede:** Tüm veri. Ayrıca metin araması `pg_trgm` + GIN index ile
veritabanının içinde çözülüyor — ayrı arama motoru gerekmiyor.
**Neden tercih ettim:** Ödev zaten istiyordu; serbest bırakılsa da seçerdim —
ücretsiz, açık kaynak, JS dünyasında fiilen standart.
**Alternatifi neden değil:** MSSQL/Oracle aynı işi yapar (SQL %90 aynı) ama
lisanslı ve pahalı.
**Ödevdeki karşılığı:** Aynı. **Kutu 1.**

### 3.5 Zod

**Nedir:** Şema tanımlama ve doğrulama kütüphanesi.
**Ne işe yarar:** Bir veri şeklini **bir kez** tanımlarsın; o tanımdan hem
çalışma anı doğrulaması, hem TypeScript tipi, hem OpenAPI dokümanı üretilir.
**Bu projede nerede:** Her API girişinde doğrulama · `packages/contracts` içinde
paylaşılan tipler · her API çıkışında fazla alanın kırpılması · frontend form
doğrulaması (aynı şema).
**Neden tercih ettim:** Aynı şemayı **frontend ile paylaşabiliyorum.** 254M
indirme/hafta.
**Alternatifi neden değil:** `class-validator` Nest'in *resmî* yolu — ama
dekoratörleri tarayıcıya taşınamaz, yani şema paylaşılamaz. *(Bu, "resmî öneri
olsa bile yaygın best practice kazanır" kuralının uygulaması.)*
**Ödevdeki karşılığı:** FluentValidation — ama o yalnızca doğrular; tip ve
OpenAPI ayrı iştir. **Kutu 2.**

### 3.6 BullMQ + Redis

**Nedir:** Node için iş kuyruğu; verilerini Redis'te tutar.
**Ne işe yarar:** İki tür iş: **gecikmeli** ("SLA'ya 2 saat kala hatırlat") ve
**tekrarlayan** ("her 15 dakikada bir SLA'sı geçenleri tara"). Hata alırsa
otomatik yeniden dener; worker sayısı artırılarak ölçeklenir.
**Bu projede nerede:** Ödev §15'in dört job'ı — SLA hatırlatma, SLA ihlali
taraması, günlük operasyon özeti, arşiv adayı belirleme.
**Neden tercih ettim:** 7.9M indirme/hafta, resmî Nest entegrasyonu, hazır
yönetim ekranı, yatay ölçekleme.
**Alternatifi neden değil:** `pg-boss` — Redis istemez, işleri Postgres'te
tutar ve ödevin "Hangfire verileri PostgreSQL'de" maddesine daha yakındı. Ama
1.35M/hafta ile niş kalıyor; uzun ömürlü bir kurum sisteminde daha az yaygın
olanı seçmek devralanı zorlar. *(Redis ayrıca hız sınırı ve dağıtık kilit için
de kullanılıyor — tek amaçlı bir bağımlılık değil.)*
**Ödevdeki karşılığı:** Hangfire. Gecikmeli job, recurring job ve dashboard —
üçü de karşılanıyor. **Kutu 2.**

### 3.7 Testcontainers

**Nedir:** Test başlarken gerçek bir veritabanı konteyneri ayağa kaldıran
kütüphane.
**Ne işe yarar:** Testler gerçek PostgreSQL'e karşı koşar; bitince konteyner
silinir. Rastgele port kullandığı için çalışan başka veritabanlarına dokunmaz.
**Bu projede nerede:** Tüm entegrasyon testleri.
**Neden tercih ettim:** Ödev §23 unique constraint ihlali, foreign key ihlali,
transaction ve concurrency conflict testleri istiyor. **Sahte veritabanı bunları
yapısal olarak test edemez** — test yeşil yanar ama hiçbir şey ölçmez.
**Alternatifi neden değil:** Prisma Client'ı mock'lamak — EF Core InMemory
provider'ın aynısı; Microsoft'un kendisi bunu önermiyor.
**Ödevdeki karşılığı:** Ödevin "InMemory provider kullanmayın" maddesinin
olumlu karşılığı. **Kutu 1.**

### 3.8 dependency-cruiser

**Nedir:** Kod içindeki `import` ilişkilerini kurallara göre denetleyen araç.
**Ne işe yarar:** "Domain katmanı Prisma'yı import edemez" gibi kuralları CI'da
**zorlar.** İhlal varsa build kırmızı yanar.
**Bu projede nerede:** Ödev §23'ün architecture testleri.
**Neden tercih ettim:** Katman kuralları yorumda kalırsa 6 ay içinde çiğnenir.
Kural ancak **kapıya** dönüşürse yaşar. 3.2M indirme/hafta.
**Alternatifi neden değil:** `eslint-plugin-boundaries` (1.5M) aynı işi yapıyor,
daha az yaygın.
**Ödevdeki karşılığı:** NetArchTest. **Kutu 3** — ödev architecture testi
istiyordu ama araç önermemişti.

### 3.9 ⚠️ Mapping — neden AutoMapper karşılığı bir kütüphane YOK

**Bu, sunumda en çok soru gelecek karar.** Ölçerek verildi:

| Aday | Yaygınlık | Bakım | Sonuç |
|---|---|---|---|
| `class-transformer` | 12M/hafta ✅ | **son yayın 2022**, hâlâ 0.x ❌ | Elendi |
| `@automapper/core` | 106K/hafta ❌ | Aktif ✅ | Elendi |
| **Zod şeması + Prisma `select`** | Zod 254M ✅ | Aktif ✅ | **Seçildi** |

Ayrıca ikisi de **dekoratörlü sınıf** ister; Prisma düz nesne döndürür. Yani
sadece mapper'ı beslemek için her modeli bir de sınıf olarak yazmam gerekirdi —
ödevin uyardığı *"pattern kullanmış görünmek için eklenen yapı"* tam olarak budur.

**Yerine kurulan mekanizma:** response şekli `packages/contracts` içinde tek bir
Zod şeması → Prisma `select` o şemadan türetiliyor (veritabanı sadece gerekeni
çekiyor) → çıkışta `schema.parse()` şemada olmayan **her alanı kırpıyor** (şifre
hash'i yanlışlıkla `select`'e girse bile response'a çıkamaz).

**Savunma cümlesi:** *"AutoMapper'ın üç işi — projeksiyon, dönüşüm, hassas alan
gizleme — karşılanıyor. Farkı: hatalar çalışma anında değil derleme zamanında
yakalanıyor. AutoMapper C#'ta gerekli çünkü elle class→class mapping çok koddur;
Prisma'da `select` zaten mapping'in kendisidir."* **Kutu 4.**

### 3.10 Neden Express, Fastify değil

**Darboğaz** = zincirin en yavaş halkası; sadece onu hızlandırmanın anlamı var.

| Aşama | Süre |
|---|---|
| HTTP katmanı (Express/Fastify) | ~0.3 ms |
| Doğrulama + yetki | ~0.5 ms |
| **Veritabanı sorgusu** | **20–80 ms** |
| JSON'a çevirme | ~1 ms |

Fastify HTTP katmanında ~2 kat hızlı: 0.3 ms → 0.15 ms. **60 ms'lik istekte
kazanç %0.25** — ölçüm aleti zor görür. Aynı emeği index'e harcamak 80 ms'yi
5 ms'ye indiriyor: **16 kat.**

**Savunma cümlesi:** *"Fastify daha hızlı, doğru. Ama isteğimizin %95'i
veritabanında geçiyor. Emeği index'lere harcadık. Nest'te adaptör tek satır —
ölçüp gerekirse geçeriz."*

### 3.11 Neden REST, GraphQL değil

Yaygın yanılgı: *"REST tüm veriyi getirir, GraphQL sadece isteneni."* Bu, **kötü
tasarlanmış REST'in belirtisi**, kuralı değil. Bizim REST'imiz zaten sadece
gerekeni döndürüyor (liste DTO'su 7 alan, detay DTO'su 25) ve veritabanından da
sadece o alanlar çekiliyor.

| | REST | GraphQL |
|---|---|---|
| HTTP önbelleği | Kendiliğinden çalışır | Kendi cache'ini kurarsın |
| İzleme | Her uç ayrı URL — hangisi yavaş görünür | Tek URL, izleme aracı ayırt edemez |
| Yetkilendirme | Uç bazında | **Alan bazında** — çok daha karmaşık |

GraphQL, *kontrol etmediğin* yüzlerce istemci problemi için doğdu. Buradaki
istemciler: kendi web'imiz ve kendi mobilimiz. **Kutu 1.**

### 3.12 ⏳ Aynı şablonla doldurulacaklar

TypeScript · React · Redis · argon2 · JWT · TanStack Query · Tailwind ·
shadcn/ui · React Hook Form · Vitest · Playwright · Docker · Docker Compose ·
pnpm · Turborepo · Swagger/OpenAPI · `nestjs-pino` · `nestjs-cls` · Terminus ·
Renovate · GitHub Actions / GitLab CI · Expo

---

## 4. Mimari

### 4.1 Modüler monolit + Clean Architecture

**Monolit** = tüm uygulamanın tek program olarak çalışması. Karşıtı mikroservis:
her modül ayrı program, ağ üzerinden konuşur — küçük ekipte erken ve pahalı bir
karar (ağ hataları, dağıtık transaction, ayrı deploy zincirleri).

**Modüler monolit** ara yol: tek program, ama içi net sınırlı modüllere bölünmüş
(lokasyon · varlık · iş emri · bildirim). Modüller birbirinin iç koduna elini
sokmaz. Yarın bir modül ağırlaşırsa sınır zaten çizili olduğu için ayırmak kolay.

**Clean Architecture'ın tek kuralı: bağımlılık oku hep içe bakar.**

```
infrastructure  (Prisma, HTTP, BullMQ, Redis)
      ↓ bilir
application     (use case'ler: "iş emrini ata")
      ↓ bilir
domain          (saf iş kuralları — hiçbir kütüphane bilmez)
```

Somut karşılığı: *"Kapatılmış iş emri güncellenemez"* kuralı `packages/domain`
içinde yaşar; Prisma'yı da Nest'i de HTTP'yi de tanımaz. İki faydası: o kuralı
test etmek için veritabanı gerekmez (test milisaniyede koşar), ve yarın
Prisma'dan başkasına geçilse iş kuralları hiç bozulmaz.

⭐ **Vurgulanacak nokta:** Ödevin §8'i *"Domain katmanı EF Core, Hangfire veya
ASP.NET Core bağımlılıklarını bilmemelidir"* diyor — bu kelimesi kelimesine
Clean Architecture'ın bağımlılık kuralı. Ve biz bunu **yorum olarak değil,
`dependency-cruiser` testiyle** zorunlu kılıyoruz.

### 4.2 Neden ayrı bir API sunucusu

Dört soru sordum; üçü "evet" çıktı:

| Soru | Cevap |
|---|---|
| API'yi başka istemci tüketecek mi? | **Evet** — ödev ayrı React istemcisi istiyor, ileride Expo mobil aynı API'yi kullanacak |
| Kullanıcı istek atmasa da çalışan iş var mı? | **Evet** — dört zamanlanmış job |
| DI yaşam döngüsü gerekiyor mu? | **Evet** — ödev §14 tabloyu zorunlu tutuyor |
| Kurumun kendi sunucusunda mı çalışacak? | **Evet** |

⚠️ **Sık gelen itiraza hazır cevap:** *"Next.js tek başına da API yazabilir,
neden ayırdın?"* → Yazabilir, doğru — Route Handler'lar gerçek API'dir. Ama üç
şeyi veremez: sürekli çalışan arka plan süreci, DI konteyneri ve yaşam
döngüleri, zorlanan katman sınırları. Bu projede üçüne de ihtiyaç var.

### 4.3 Fiziksel yapı

```
apps/web      → Next.js — yalnızca arayüz, Prisma'yı GÖRMEZ
apps/api      → NestJS — HTTP API
apps/worker   → NestJS — arka plan işleri, HTTP dinlemez
packages/contracts → Zod şemaları (web ve api aynı kaynaktan okur)
packages/domain    → saf iş kuralları
```

**Neden tek depo (monorepo):** API'de bir alanın adı değişince frontend
**derlenmiyor** — hata ekranda değil, derleyicide çıkıyor. Ayrı depolarda bu
korumayı kaybederdik.

⚠️ **Monorepo ≠ monolit.** Monorepo *kodun nerede durduğu*, monolit *programın
nasıl çalıştığı* hakkındadır — bağımsız eksenler.

---

## 5. ⭐ Bir iş emrinin hayatı — sistemi baştan sona gösteren anlatım

⏳ *Kod yazıldıkça gerçek dosya adları ve satırlarla doldurulacak.*

Anlatım sırası:

1. **Talep açılıyor** — Kullanıcı formu doldurur → Next form doğrulaması
   (`packages/contracts` şeması) → API'ye gider → **aynı şema** sunucuda tekrar
   doğrular *(istemciye asla güvenilmez)*
2. **İş kuralları** — Lokasyon pasif mi? Varlık kullanım dışı mı? Bu kontroller
   `packages/domain` içinde, veritabanı bilmeden
3. **SLA hesabı** — `SlaPolicyFactory` devreye girer *(§6.1)*
4. **Kayıt** — İş emri + ilk geçmiş kaydı **tek transaction** içinde
5. **Job planlanıyor** — BullMQ'ya gecikmeli hatırlatma bırakılır
6. **Atama** — Yalnızca teknik personele; atama + geçmiş kaydı yine tek transaction
7. **Durum değişikliği** — Durum makinesi geçerli mi diye bakar; geçersizse
   domain hatası → merkezî filtre → **409**
8. **Eş zamanlı güncelleme** — İki kişi aynı anda değiştirirse `version` kolonu
   çakışmayı yakalar → **409 Conflict**
9. **SLA aşımı** — Worker tarar, işaretler, bildirim üretir — **iki kez
   çalışsa da tek bildirim** (unique index + job anahtarı)
10. **Kapanış** — Çözüm açıklaması zorunlu; kapalı kayıt normal güncellemeyle
    değiştirilemez

---

## 6. Zor maddeler

### 6.1 ⏳ Factory Pattern ve SLA

Ödev §7 Factory Pattern'i **zorunlu** tutuyor ve "göstermelik olmasın" diyor.

Tasarım: `SlaPolicyFactory`, politikaları **çoklu sağlayıcı** olarak dizi hâlinde
enjekte alır. Her politika `supports(ctx)` ve `calculate(ctx)` uygular.

- **Yeni politika eklemek** = yeni sınıf + bir satır kayıt. Mevcut kod
  değişmez → **Open/Closed**
- **Service Locator değil** — factory konteynerden arama yapmaz, diziyi
  enjeksiyonla alır
- Politikalar Nest'siz de test edilebilir → unit testleri hızlı

⭐ Canlı incelemede *"yeni bir SLA politikası ekle"* denmesi çok muhtemel —
bu tasarım o anda 2 dakikada gösterilebilir olmalı.

### 6.2 ⏳ Durum makinesi
### 6.3 ⏳ Transaction ve concurrency
### 6.4 ⏳ Arka plan işleri ve idempotency

### 6.5 DI yaşam döngüleri (ödev §14 — zorunlu doküman)

| Nest | .NET | Bu projede |
|---|---|---|
| `DEFAULT` (singleton) | `Singleton` | Yapılandırma, `Clock`, SLA politikaları, factory |
| `REQUEST` | `Scoped` | Aktif kullanıcı, correlation ID |
| `TRANSIENT` | `Transient` | Kullanılmıyor — gerekçesiz kullanılmaz |

⚠️ **Neden önemli:** İstek bazlı veri singleton serviste tutulursa iki
kullanıcının verisi karışır — *Ali'nin isteği Veli'nin bilgisiyle işlenir.* Bu
hata tek kullanıcılı testte **hiç görünmez**, yük altında çıkar ve kurumsal
sistemde yanlış kişinin verisini göstermek demektir.

Çözüm: aktif kullanıcı `nestjs-cls` (AsyncLocalStorage) ile taşınır, sistem
saati `Clock` soyutlaması üzerinden okunur. Arka plan işlerinde HTTP bağlamı
yoktur; iş kendi bağlamını kurar.

### 6.6 Neden sahte veritabanıyla test etmiyoruz

Ödev "EF Core InMemory kullanmayın" diyor — bu kurum kaprisi değil, Microsoft'un
kendi tavsiyesi. InMemory gerçek bir veritabanı değil, RAM'deki bir sözlüktür:
**unique constraint, foreign key, transaction ve SQL semantiği uygulanmaz.**
Ödev tam da bunları test etmeyi istiyor.

Bizim stack'teki aynı tuzak: **Prisma Client'ı mock'lamak.** Yapmıyoruz.

---

## 7. ⏳ Test ve teslim

- Unit (Vitest) · Integration (Testcontainers, gerçek Postgres) ·
  Architecture (dependency-cruiser) · E2E (Playwright)
- **Koruma testi kuralı:** bir korumayı test eden test yazıldığında, koruma
  geçici olarak kaldırılıp testin kırmızıya döndüğü **gözle görülür.** Dönmüyorsa
  test korumayı değil başka bir şeyi ölçüyordur.
- `docker compose up --build` ile tek komutta dört servis
- CI: `pnpm ci:verify` → GitHub Actions ve GitLab CI aynı script'i çağırır

---

## 8. ⏳ Bilinen teknik borçlar

Ödev §31 bunu açıkça soruyor. **Sorulmadan söylenir** — dürüstlük olumlu
değerlendirilir. Proje ilerledikçe doldurulacak.

---

## 9. Muhtemel sorular ve hazır cevaplar

| Soru | Nerede cevabı var |
|---|---|
| "Neden .NET değil?" | §1 — kurum serbest bıraktı; ekip JS'e geçiyor, ödevin her yeteneği karşılandı |
| "Neden Hangfire değil BullMQ?" | §3.6 |
| "AutoMapper nerede?" | §3.9 — ölçümle |
| "Repository Pattern neden yok?" | Prisma zaten repository soyutlaması; üstüne katman `select` yeteneklerini kısıtlar ve ödevin uyardığı generic CRUD tuzağına düşer |
| "Neden Next.js, Vite değil?" | §3.2 |
| "Next tek başına yetmez miydi?" | §4.2 |
| "Domain katmanı gerçekten bağımsız mı?" | §4.1 — `dependency-cruiser` testi, iddia değil kapı |
| "Neden Redis eklendi?" | §3.6 — kuyruk + hız sınırı + dağıtık kilit |
| "GraphQL düşündün mü?" | §3.11 |
| "Fastify daha hızlı değil mi?" | §3.10 |
| "Bu kadar test şart mı?" | Ödev §23 zorunlu tutuyor; ayrıca §6.6 |

---

## 10. Sunum yazım kuralları (kendime not)

- Her başlık **"bu ne demek"** ile başlar, sonra "neden böyle yaptık"a geçer
- Kod okumadan anlaşılmalı
- Her sapma **ölçüyle** desteklenir — "bence daha iyi" yok
- Her session bittiğinde o session'ın kararları **hemen** buraya işlenir;
  en sona bırakılmaz, çünkü gerekçe en iyi kararı verirken hatırlanır
