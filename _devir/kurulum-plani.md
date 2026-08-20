# Bakım ve İş Emri Yönetim Sistemi — Kurulum Planı

## Context — bu plan neden var

İzmir Büyükşehir bir teknik değerlendirme ödevi verdi: **Bakım ve İş Emri Yönetim
Sistemi**. Ödev dosyası (`~/Downloads/Yazılım Geliştirici Teknik Değerlendirme
Çalışması.docx`) 32 bölüm ve zorunlu bir stack listesi içeriyor: .NET / ASP.NET Core /
EF Core / Hangfire / AutoMapper / FluentValidation / React+Vite.

Ancak kurum **"istediğin stack'i kullan"** dedi. Belediyedeki ekip zaten .NET'ten
JS ailesine geçiş yapıyor ve senin eğitimin de o tarafta. Bu yüzden:

1. Ödevdeki her zorunlu teknolojiyi **JS ailesinden best-practice eşleniğiyle**
   değiştiriyoruz — ama **hiçbir yeteneği düşürmeden**. Ödevin sorduğu her şey
   (Factory Pattern, DI lifetime'ları, transaction, optimistic concurrency,
   architecture test, DBML) yeni stack'te de **birebir karşılanacak**.
2. Stack'in omurgası senin `proje-kiti` skill'in (Next.js + TS + Prisma + Postgres
   + Zod + Tailwind/shadcn + Vitest/Playwright). Buna **NestJS** ekleniyor.
3. Her teknoloji için "ne işe yarar / neden seçtik / .NET karşılığı neydi"
   açıklaması bir **sunum dosyasına** yazılacak — teslim sonrası canlı teknik
   inceleme var ve sorular gelecek.

**Ek kısıt:** Bu makinede paralel başka bir Next.js projesi var
(`benim-belediyem`). Kontrol edildi: `benim-belediyem-db` konteyneri **şu anda
5432 portunu tutuyor**. Port ve konteyner adı çakışmaları planın birinci sınıf
konusu.

**Kabul kriteri:** Ödevin 30. bölümündeki teslim listesinin tamamı + değerlendirmecinin
`docker compose up --build` ile tek komutta sistemi ayağa kaldırabilmesi + senin
her kararı savunabilmen.

---

## 1. Stack çeviri tablosu — ödevde ne isteniyordu, biz ne koyuyoruz

Bu tablo sunum dosyasının da omurgası. Sol sütun ödevin zorunlu listesi, sağ sütun
bizim seçimimiz.

| Ödevdeki zorunlu | Bizim seçimimiz | Neden bu eşlenik |
|---|---|---|
| .NET + ASP.NET Core Web API | **NestJS 11** | Node'da DI konteyneri, modül sistemi, Guard/Interceptor/Pipe/Filter'ı olan **tek** framework. Ödevin §14 (Transient/Scoped/Singleton) sorusuna cevap verebilen tek JS seçeneği. Express'in kendisi bunu veremez |
| C# | **TypeScript 6 (strict)** | Derleme zamanı tip güvenliği. TS 7 **kullanılamıyor**: `typescript-eslint` peer'ı `<6.1.0` (doğrulandı) |
| Entity Framework Core | **Prisma 7** | Şema-önce ORM + migration motoru. `prisma migrate` = EF migrations. Tip güvenli `select` ile DB seviyesinde projection |
| PostgreSQL | **PostgreSQL 18** | Değişmiyor. Ödevin zaten istediği |
| FluentValidation | **Zod 4** (+ `nestjs-zod`) | Tek şema hem doğrular hem TS tipini hem OpenAPI'yi üretir. FluentValidation'da bu üçü ayrı iş |
| Hangfire | **BullMQ 6** + Redis (+ `@nestjs/bullmq`) | Node'un fiili standardı: gecikmeli job, cron, retry+backoff, yatay ölçeklenen worker. Bull Board = Hangfire Dashboard'un karşılığı |
| AutoMapper / Mapster | **Zod response şeması + Prisma `select`** | Aday kütüphaneler ölçüldü, ikisi de kriterlere takıldı (§4). Mapping bunun yerine Zod+Prisma üstünde **mekanizma** olarak kuruluyor |
| OpenAPI + Scalar/Swagger | **`@nestjs/swagger` → Swagger UI** (Scalar ikinci arayüz olarak opsiyonel) | Zod şeması → OpenAPI → Swagger UI. Swagger UI `@nestjs/swagger` ile **ekstra bağımlılık olmadan** gelir ve piyasada herkesin bildiği arayüz. Scalar daha şık ama `@scalar/nestjs-api-reference` çok daha az kullanılıyor — omurgayı ona bağlamıyoruz |
| React + Vite + React Router | **Next.js 16 (App Router) + React 19** | Kitin varsayılanı. Router, kod bölme, SSR, görsel optimizasyonu hazır gelir |
| xUnit / NUnit | **Vitest 4** | Kitin standardı; Jest'ten hızlı, ESM-yerel |
| Integration test (PostgreSQL) | **`@testcontainers/postgresql`** | Test başlarken **gerçek bir PostgreSQL konteynerini ayağa kaldırır**, rastgele portta; test bitince siler. Neden şart olduğu §4b'de |
| (Architecture test) | **`dependency-cruiser`** | NetArchTest'in karşılığı: katman bağımlılık kurallarını CI'da kırmızıya çevirir. **Kitinde mimari testi yok** — bu bir şeyin yerine geçmiyor, eksiği kapatıyor. Yaygınlık: 3.2M/hafta (alternatifi `eslint-plugin-boundaries` 1.5M) |
| Docker / Compose / Git | **Aynı** | Değişmiyor |

### "Next.js, Vite ve React Router'ın işini üstleniyor" ne demek

Sunumda birebir bu şekilde anlatılacak — çünkü gelecek soru şu: *"Ödev Vite ve
React Router istemişti, neden yoklar?"*

**React tek başına bir web sitesi değildir.** React sadece "veriye göre ekranı
çiz" işini yapan bir kütüphanedir. Bir React projesinin ayakta durması için iki
şey daha lazım ve ödev bunları ayrı ayrı istemiş:

| Parça | Ne işi yapar | Onsuz ne olur |
|---|---|---|
| **Vite** | *Build aracı ve geliştirme sunucusu.* İki iş: (1) sen dosyayı kaydedince tarayıcıyı anında güncelleyen dev sunucusu, (2) TypeScript+React kodunu tarayıcının anlayacağı, sıkıştırılmış ve parçalara bölünmüş JS'e çeviren paketleyici | Tarayıcı senin TS ve JSX kodunu anlamaz |
| **React Router** | *Adres → ekran eşlemesi.* React'in URL diye bir kavramı yoktur. `/is-emirleri/42` adresine hangi bileşenin karşılık geldiğini, sayfa yenilenmeden geçişi, iç içe yerleşimleri ve korumalı rotaları o yönetir | Uygulaman tek ekrandan ibaret kalır |

**Next.js bu ikisini de içinde barındırır:**

- Vite'ın yerine **kendi paketleyicisi (Turbopack)** — dev sunucusu ve production
  build aynı araçtan gelir.
- React Router'ın yerine **dosya sistemi tabanlı yönlendirme (App Router)** —
  klasör yapısının kendisi rotadır. `app/is-emirleri/[id]/page.tsx` dosyası
  doğrudan `/is-emirleri/42` adresine karşılık gelir; ayrıca rota tablosu yazmazsın.

Üstüne ikisinin de vermediklerini verir: sunucuda render (ilk açılış hızı),
sunucu bileşenleri, görsel optimizasyonu, yerleşik önbellekleme.

**Yani ödevin listesindeki üç parça (React + Vite + React Router) birbirine elle
bağlanan üç ayrı üründür; Next.js bunların üçünü de kapsayan tek üründür ve
sürüm uyumunu üretici garanti eder.** React yine kullanılıyor — Next.js React'in
framework'ü, o yüzden ödevin "React ile frontend" şartı doğrudan karşılanıyor.

**Dürüst tarafı:** Küçük, tamamen istemci taraflı bir panel için Vite + React
Router daha hafif ve öğrenmesi daha kolaydır. Next.js daha kuralcı ve daha
ağırdır. Bu projede Next'i seçtik çünkü uzun ömürlü olacak, kimlik doğrulama ve
sunucu tarafı işleri var, ve **üç ayrı paketin sürüm uyumunu yıllarca elle takip
etmek istemiyoruz** — Next'te bu uyumu üretici garanti eder. → `ADR-007`

⚠️ Bu "elle takip etmek istemiyoruz" cümlesi bir niyet beyanı olarak kalamaz;
mekanizmaya çevriliyor → §4d.

### Paket seçim kriteri — "kolay eskimesin"

Her seçimde şu sıraya bakıldı: **(1)** piyasada yaygın mı, **(2)** aktif bakımda mı,
**(3)** bir üstündeki katmanın *resmî* çözümü mü. Niş ama zarif olanı, yaygın ve
sıkıcı olana tercih etmedik — çünkü bu projeyi yıllarca sen sürdüreceksin ve
belediyeye başka geliştiriciler de girip çıkacak.

Bu yüzden **reddedilen** iki cazip seçenek:

- **pg-boss** (Redis istemeyen, job'ları Postgres'te tutan kuyruk). Ödevin
  "Hangfire verileri PostgreSQL'de" maddesine daha iyi uyuyordu, ama BullMQ'nun
  yanında niş kalıyor. BullMQ, Node dünyasının fiili standardı — resmî Nest
  entegrasyonu, hazır dashboard, yatay ölçekleme, çok daha geniş topluluk.
- **Scalar'ı tek API arayüzü yapmak.** Güzel ama az kullanılıyor; Swagger UI
  varsayılan kaldı.

### Üstün gelen kural

**Ödev metni de olsa, teknolojiyi yapan şirketin "resmî önerisi" de olsa —
piyasada yaygın olan ve aktif bakımdaki best practice kazanır.** Sapma yasak
değil, *açıklamasız* sapma yasak: her sapma bir ADR'ye ve sunum dosyasına
gerekçesiyle yazılır.

Bu kuralın ilk uygulaması: Nest'in *resmî* doğrulama yolu `class-validator`'dır,
biz **Zod** kullanıyoruz. Sebep: aynı şemayı `packages/contracts` üzerinden React
formlarıyla **paylaşabiliyoruz** — `class-validator` decorator'ları tarayıcıya
taşınamaz. Rakamlar da bunu destekliyor: Zod 254M/hafta, aktif bakımda.
→ `ADR-003`

### Ödevde adı geçmeyen ama gereken parçalar

| Parça | Seçim | Ne işe yarar / .NET karşılığı |
|---|---|---|
| Şifre özetleme | `argon2` (argon2id) | Kit zorunlu tutuyor. .NET'te `Identity` `PasswordHasher` |
| Kimlik doğrulama | `@nestjs/jwt` + `@nestjs/passport` | Elle JWT + refresh rotation. `Microsoft.AspNetCore.Authentication.JwtBearer` |
| Request context | `nestjs-cls` (AsyncLocalStorage) | Aktif kullanıcı + correlation ID'yi katmanlara parametre geçmeden taşır. `IHttpContextAccessor` karşılığı; §21'in "statik yapılardan alma" şartını çözer |
| Structured logging | `nestjs-pino` | JSON log + correlation ID. `Serilog` karşılığı |
| Health check | `@nestjs/terminus` | `/health/live`, `/health/ready`. `AspNetCore.HealthChecks` karşılığı |
| Hız sınırı | `@nestjs/throttler` (Redis deposu) | Brute-force koruması. `AspNetCoreRateLimit` karşılığı |
| DBML üretimi | `@dbml/cli` (`sql2dbml`) | **`prisma-dbml-generator` ölü (son yayın 2024-02)**. Bunun yerine migration'ın ürettiği gerçek şemadan DBML üretiyoruz — uyumluluk iddia değil, mekanizma olur |
| Sunucu durumu (FE) | TanStack Query 5 | Cache, retry, invalidation. Kit standardı |
| Form (FE) | React Hook Form + Zod resolver | Backend'le **aynı** Zod şeması. DRY'ın somut kanıtı |
| Monorepo | pnpm workspaces + Turborepo | Tek `install`, tek CI, paylaşılan kontrat paketi |
| Bağımlılık güncelleme | **Renovate** | Sürüm takibini bota devreder (§4d). GitHub *ve* GitLab'da çalışır — Dependabot sadece GitHub'da |

---

## 2. Mimari

**Modüler monolit + Clean Architecture katmanları** (ödev §10 buna izin veriyor).

### Bu iki terim ne demek (sunum dosyasına da bu haliyle girecek)

**Monolit**, tüm uygulamanın tek bir program olarak çalışması demek. Karşıtı
**mikroservis**: her modül ayrı bir program, birbirleriyle ağ üzerinden konuşur.
Mikroservis küçük ekipte erken ve pahalı bir karardır — ağ hataları, dağıtık
transaction, her modül için ayrı deploy zinciri gelir.

**Modüler monolit** ara yoldur: *tek program, ama içi net sınırlı modüllere
bölünmüş* (lokasyon · varlık · iş emri · bildirim). Modüller birbirinin iç
koduna elini sokmaz, yalnızca ilan edilmiş arayüzünü çağırır. Yarın bir modül
gerçekten ağırlaşırsa sınır zaten çizili olduğu için ayırmak kolaydır.

**Clean Architecture**'ın tek kuralı var: **bağımlılık oku hep içe bakar.**

```
        infrastructure  (Prisma, HTTP, BullMQ, Redis)
              ↓ bilir
        application     (use case'ler: "iş emrini ata")
              ↓ bilir
        domain          (saf iş kuralları — hiçbir kütüphane bilmez)
```

İç katman dışarıyı **bilmez**. Somut karşılığı: "Kapatılmış iş emri
güncellenemez" kuralı `packages/domain` içinde yaşar ve Prisma'yı da Nest'i de
HTTP'yi de tanımaz. İki faydası: (1) o kuralı test etmek için veritabanı
gerekmez, test milisaniyede koşar; (2) yarın Prisma'dan başka bir şeye geçsen iş
kuralları hiç bozulmaz. Ödevin §8'indeki *"Domain katmanı EF Core, Hangfire veya
ASP.NET Core bağımlılıklarını bilmemelidir"* maddesi kelimesi kelimesine bu
kuraldır — ve biz bunu yorum olarak değil `dependency-cruiser` testiyle
zorunlu kılıyoruz.

**Gerçek hayat gerekçesi:** Belediye sistemlerinde modül sayısı zamanla artar
(stok, bütçe, personel...). Mikroservise bölmek bu boyutta erken karar olurdu;
tek parça yazmak da 2 yıl sonra dokunulamaz hale gelirdi. Modüler monolit,
büyümeye açık ama bugün taşınabilir olanı.

### Fiziksel yapı

İki uygulama, tek repo, tek veri modeli:

```
apps/web      Next.js 16 — sadece arayüz. İş kuralı YOK, Prisma YOK
apps/api      NestJS — HTTP API. Domain + Application + Infrastructure katmanları
apps/worker   NestJS standalone — BullMQ worker'ı. apps/api ile aynı kodu paylaşır
packages/contracts   Zod şemaları + türetilen TS tipleri. web ve api ikisi de buradan okur
packages/domain      Saf iş kuralları. Prisma/Nest/HTTP BİLMEZ (§8'in son maddesi)
```

Bağımlılık yönü tek: `web → contracts`, `api → domain → (hiçbir şey)`.
**Bu kural yorum değil, `dependency-cruiser` ile CI'da test edilir** (§23).

### Neden ayrı Nest API? (Kitin kuralını bilerek deliyoruz)

Kitin `00-stack.md` dosyasında "Ayrı Express/Nest backend kurulmaz" yazıyor.
Bu proje için **ADR ile geçersiz kılıyoruz**, sebepleri:

1. Ödev, React istemcisi tarafından tüketilen **bağımsız bir Web API** istiyor (§3).
2. Ödev **ayrı bir Worker konteyneri** istiyor (§24). Next.js'in sunucusuz modeli
   sürekli çalışan recurring job barındıramaz.
3. Ödevin §14'ü servis yaşam döngüsü tablosu istiyor — Next Route Handler'ların
   DI konteyneri yok, bu soruya cevap veremez.

Bu karar `docs/decisions/ADR-002-neden-nestjs.md` içine yazılır ve projenin
`00-stack.md` dosyasına **senkron sınırının altına** işlenir.

#### Önce temel: "sunucu çalıştırmak" ne demek (sunuma girecek)

Uygulaman iki ayrı yerde çalışır: **kullanıcının tarayıcısında** ve **senin
kontrolündeki bir bilgisayarda — sunucuda.** Tarayıcıya güvenilmez; kullanıcı
geliştirici araçlarını açıp gönderdiği veriyi değiştirebilir. Bu yüzden
*"bu kişi yönetici mi", "bu iş emri kapalı mı", "şifre doğru mu"* kararları
**sunucuda** verilir. "Sunucu çalıştırmak" da şu demektir: bir program açık bir
portta durur, HTTP isteklerini karşılar ve veritabanıyla konuşur.

Fark burada:

| | Arayüz üretir | Sunucu çalıştırabilir |
|---|---|---|
| **Next.js** | ✅ | ✅ (Route Handler'lar — `app/api/.../route.ts`) |
| **NestJS** | ❌ | ✅ (sadece bu) |

Yani "sunucuyu Next'ten mi Nest'ten mi çalıştıralım" sorusu aslında şudur:
**API kodunu Next'in içine mi yazacağız, yoksa ayrı bir programa mı?**

**Best practice cevabı duruma göre değişir — ama zevke göre değil, §11'deki üç
somut koşula göre.** Tek istemcin kendi web arayüzünse ve sürekli çalışan arka
plan işin yoksa Next tek başına doğrudur (kitin varsayılanı; küçük/orta projede
haklı). Bu projede üç koşul da sağlandığı için ayrı API doğrudur.

⚠️ **Sık yapılan hata — ödev bunu yasaklamadı, TAM TERSİNİ istedi.** Ödev §3
"ASP.NET Core Web API" diyor, yani zaten ayrı bir backend sunucusu şart koşuyor.
Ayrı backend'i yasaklayan **yalnızca kit**. Yani burada ödev ile best practice
aynı tarafta; istisnaya giren kitin varsayılanı. Sunumdaki cümle bu yüzden
*"ödev öyle istedi diye"* değil, **"gerçek hayat projesi bunu gerektirdiği
için — ödev de zaten aynı şeyi istiyordu"** olacak.

#### Kitteki yasak aslında ne diyor (sunuma da girecek — yanlış anlaşılıyor)

Kitteki *"Ayrı Express/Nest backend kurulmaz"* maddesi **"Nest ile Express bir
arada kullanılmaz"** demek DEĞİL. İki yanlış anlamayı birden temizleyelim:

- **Nest zaten Express'in üstünde çalışır.** NestJS bir HTTP sunucusu değil,
  Express'i (varsayılan olarak) sarmalayan bir çatıdır. Yani "Nest + Express"
  bir çakışma değil, Nest'in normal çalışma biçimidir. Ortada best-practice
  ihlali yok.
- **Yasak teknoloji hakkında değil, mimari hakkında.** Kitin varsayılanı
  "Next.js her şeyi yapar: hem arayüz hem API, **tek deploy hedefi**". Yasağın
  anlamı: *"Next.js'in yanına İKİNCİ bir ayrı sunucu koyma."* Çünkü koyarsan
  iki sunucu, iki deploy, iki kimlik doğrulama kurulumu, CORS ayarı ve iki
  yerde tekrarlanan tipler gelir. Küçük bir proje için bunların hepsi bedava
  değil, saf maliyettir.

**Peki biz neden deliyoruz?** Bu projede o maliyetin karşılığı var (yukarıdaki
üç madde: bağımsız Web API, sürekli çalışan worker, DI yaşam döngüsü). Yani
kural yanlış değildi — bu proje kuralın istisna koşullarına giriyor.

**"Gerekçesiz delinmemeli" ne demek:** Kite yazarken yasağı silmiyoruz,
**koşula** çeviriyoruz (§11). Böylece bir sonraki projede biri canı istedi diye
ikinci bir sunucu ekleyemez; üç koşuldan birini karşıladığını gösterip ADR
yazmak zorunda kalır. Kuralın amacı yasaklamak değil, **bedeli bilerek ödetmek**.

### Ödevin zor maddelerinin karşılığı

| Ödev maddesi | Nasıl çözülür |
|---|---|
| §7 Factory Pattern, Open/Closed | `SlaPolicyFactory`, politikaları **multi-provider token** ile dizi olarak enjekte eder. Her politika `supports(ctx)` + `calculate(ctx)` uygular. Yeni politika = yeni sınıf + provider satırı, mevcut kod değişmez. Konteynerden arama yapmadığı için Service Locator değil |
| §6 Durum makinesi | `packages/domain` içinde geçiş tablosu. Controller'da `if` yok. Geçersiz geçiş domain hatası fırlatır |
| §20 Transaction | `prisma.$transaction` (interactive). Durum değişikliği + geçmiş kaydı + bildirim **tek sınırda** |
| §20 Optimistic concurrency | Tabloda `version` kolonu; `update where { id, version }` → etkilenen satır 0 ise **409 Conflict** |
| §21 Audit | Prisma Client Extension, `createdBy/updatedBy/createdAt/updatedAt` alanlarını CLS'teki kullanıcı ve `Clock` servisinden **merkezî olarak** doldurur. Servislerde tek satır audit kodu olmaz |
| §16 Duplicate bildirim | `notification` tablosunda `(userId, eventKey)` **unique index** + BullMQ `jobId` deduplication. İki katmanlı koruma |
| §15 Idempotent job | Job'lar sadece **ID** alır, nesne almaz; işe başlarken durumu DB'den okur; kapalı iş emrinde çıkar |
| §19 Hata yönetimi | Global `ExceptionFilter` → **RFC 9457 Problem Details** formatı. Controller'da try/catch yok |
| §17 Sunucu tarafı filtreleme | Prisma `where` + `skip/take`. Metin araması Postgres `GIN` + `pg_trgm` index'i ile — sunumda açıklanacak |
| §11 DBML | `pg_dump --schema-only` → `sql2dbml` → `docs/database.dbml`. **CI, dosya güncel değilse kırmızı yanar** |

---

## 3. Çakışma önleme — paralel `benim-belediyem` projesi

Doğrulandı: `benim-belediyem-db` konteyneri **şu anda 5432'de**.

**`benim-belediyem` projesine dokunmuyoruz** — ona ayrı bir prompt hazırlamaya
gerek yok. Sebebi şu: Docker'da **konteynerin içindeki port her zaman standart
kalır** (3000 / 5432 / 6379). Çakışan tek şey *host eşlemesi*, o da koda değil
**config'e** ait. Bu yüzden portları env'den okutuyoruz:

```yaml
ports: ["${WEB_PORT:-3100}:3000"]     # içerisi hep 3000
```

Belediyedeki makinede tek başına çalışacaksa `.env` içine `WEB_PORT=3000`
yazarsın — kodda tek satır değişmez, standart portlara oturur. (12-factor'ün
"config ortamdan gelir" kuralı; bu da sunuma bir madde.)

**Bu tablo Docker ekranından yönetilmiyor — `.env` dosyasından yönetiliyor.**
İki sütun olmasının sebebi iki ayrı şeyin varlığı:

- **Konteyner içi port** (3000 / 4000 / 5432 / 6379): programın kendi içinde
  dinlediği port. **Sabit, hiç değişmez.** Her konteynerin kendi ağı olduğu için
  orada çakışma imkânsızdır.
- **Host portu** (3100 / 4100 / 55432 / 6479): senin Mac'inde açılıp konteynere
  yönlendiren kapı. **Çakışma yalnızca burada olur.**

`docker-compose.yml` satırı: `ports: ["${WEB_PORT:-3100}:3000"]` → solu host,
sağı konteyner içi. Değer `.env`'den gelir. Docker Desktop ekranı bu eşlemeyi
**gösterir** ama oradan değiştirmek kalıcı değildir; `docker compose up` yeniden
çalıştığında compose dosyası ne diyorsa o geçerli olur. **Tek doğru kaynak
`.env` + `docker-compose.yml`.**

Docker'sız çalıştırdığında (`pnpm dev`) da **aynı `.env` değişkeni** okunur —
yani tek yer yönetir. Belediyede standart porta almak için `.env`'de
`WEB_PORT=3000`, başka hiçbir dosyaya dokunmadan.

| Servis | Bu makinede varsayılan | Konteyner içi | Konteyner adı |
|---|---|---|---|
| Next.js web | `WEB_PORT` = **3100** | 3000 | `bakim-web` |
| NestJS API | `API_PORT` = **4100** | 4000 | `bakim-api` |
| Worker | port açmaz | — | `bakim-worker` |
| PostgreSQL | `DB_PORT` = **55432** | 5432 | `bakim-postgres` |
| Redis | `REDIS_PORT` = **6479** | 6379 | `bakim-redis` |

Kalan tedbirler:

1. `docker-compose.yml` başına `name: bakim` → ağ ve volume adları `bakim-` ön
   ekli olur, diğer projenin `default` ağıyla çakışmaz. Volume: `bakim-pgdata`.
2. `apps/web/package.json` → `"dev": "next dev -p ${WEB_PORT:-3100}"`.
   **Varsayılana asla güvenilmez** — 3000 boşsa Next sessizce oraya oturur ve
   diğer projeyi açtığında hangisine baktığını anlamazsın.
3. `src/config/env.ts` içinde tüm portlar Zod ile doğrulanır; eksik/geçersizse
   uygulama **açılışta** net hata verir, çalışma anında gizemli hata vermez.
4. Playwright `baseURL` env'den okur.
5. Testcontainers **rastgele port** kullanır — çalışan Postgres'lerin hiçbirine
   dokunmaz, `benim-belediyem-db` etkilenmez.
6. `docs/PORTLAR.md` — bu tablo + "belediyede standart porta nasıl alınır" notu.

**Ayrıca:** Node **v25.6.0** kurulu, bu LTS değil. Kit `.nvmrc` ile LTS şart
koşuyor → `.nvmrc` = `24`, CI de 24 kullanır.

---

## 4. Sunumda soru gelecek kararlar

### 4a. Mapping — neden AutoMapper karşılığı bir kütüphane yok

Bu karar **zevkle değil, ölçerek** verildi. Adaylar (haftalık indirme / son yayın):

| Aday | Yaygınlık | Bakım | Sonuç |
|---|---|---|---|
| `class-transformer` | 12M/hafta ✅ | **son yayın 2022-12**, hâlâ 0.x ❌ | "Kolay eskimesin" kriterine takıldı |
| `@automapper/core` | 106K/hafta ❌ | 2026-07 ✅ | "Yaygın olsun" kriterine takıldı |
| `Zod + Prisma select` | Zod 254M/hafta ✅ | ikisi de aktif ✅ | **Seçildi** |

Ayrıca ikisi de **dekoratörlü sınıf** ister; Prisma düz nesne döndürür. Yani
sadece mapper'ı beslemek için her modeli bir de sınıf olarak yazmamız gerekirdi —
ödevin uyardığı "pattern kullanmış görünmek için eklenen yapı" tam olarak budur.

**Bunun yerine kurulan mekanizma** (ADR-004'e yazılır):

1. Her response şekli `packages/contracts` içinde **tek bir Zod şeması**.
2. Prisma `select` nesnesi o şemadan **türetilir** → veritabanı sadece response'un
   ihtiyacı olan kolonları çeker (§13'ün "bütün tabloyu belleğe alma" şartı,
   AutoMapper'ın `ProjectTo`'sunun karşılığı).
3. Controller çıkışında `schema.parse()` → şemada olmayan **her alan kırpılır**.
   Şifre hash'i yanlışlıkla `select`'e girse bile response'a çıkamaz.
4. Şema round-trip testleri = §13'ün istediği "mapping konfigürasyonu testi".

Yani AutoMapper'ın yaptığı üç iş (projection, dönüşüm, hassas alan gizleme)
karşılanıyor; farkı, hataların **çalışma zamanında değil derleme zamanında**
yakalanması. AutoMapper C#'ta gerekli çünkü elle class→class mapping çok koddur;
Prisma'da `select` zaten mapping'in kendisidir.

### 4b. Neden entegrasyon testinde sahte veritabanı yok

Ödev "EF Core InMemory provider kullanmayın" diyor — bu belediye kaprisi değil,
Microsoft'un kendi tavsiyesi. InMemory gerçek bir veritabanı değil, RAM'deki bir
sözlüktür: **unique constraint, foreign key, transaction, concurrency ve SQL
semantiği uygulanmaz.** Ödev §23 tam da bunları test etmeni istiyor — sahte
veritabanıyla o testler yeşil yanar ama hiçbir şey ölçmez.

Bizim stack'teki aynı tuzak: **Prisma Client'ı mock'lamak.** Yapmıyoruz.
Testcontainers her test koşusunda gerçek bir PostgreSQL 18 konteynerini rastgele
portta ayağa kaldırır, migration'ları uygular, bitince siler. Bu yüzden çalışan
Postgres'lerin (senin `benim-belediyem-db`'n dahil) hiçbirine dokunmaz.

### 4d. Devralınabilirlik — "projeyi gören başka yazılımcı nereye bakacak"

Senin koyduğun ölçüt: **projeyi devralan biri "burada neyi kontrol edeceğimi
bilemedim, baştan yazayım" dememeli.** Bu, dokümantasyonla değil **mekanizmayla**
sağlanır — çünkü doküman okunmayabilir, CI okunmak zorundadır.

Kural: **her "şuna dikkat edilmeli" cümlesi, karşılığında bir kapı doğurur.**

| "Dikkat edilmeli" | Karşılığındaki kapı (unutulursa CI kırmızı) |
|---|---|
| Katman bağımlılıkları korunmalı | `dependency-cruiser` — domain'e Prisma import edilirse build düşer |
| DBML migration ile uyumlu olmalı | `pnpm dbml:check` — şema değişip DBML güncellenmezse düşer |
| Ortam değişkeni eksik olmamalı | Zod ile `env.ts` — uygulama **açılışta** net hata verir |
| Bağımlılıklar güncel kalmalı | **Renovate** — haftalık otomatik PR/MR açar |
| Hassas alan response'a sızmamalı | `schema.parse()` çıkışta kırpar + test |
| Portlar çakışmamalı | Host portları env'den; varsayılan yok |

**Renovate neden Dependabot değil:** Dependabot yalnızca GitHub'da çalışır;
Renovate GitHub *ve* GitLab'da çalışır. Belediye GitLab'a geçince bot da taşınır.
Bu, "üç paketin sürüm uyumunu elle takip etmeyelim" cümlesinin mekanizması.

**Devralan geliştiricinin okuma sırası** (README'nin en başına yazılır):

1. `README.md` → tek komutla ayağa kaldır
2. `REPO-YAPISI.md` → hangi klasör ne işe yarar
3. `docs/architecture.md` → katmanlar ve bağımlılık yönleri
4. `docs/decisions/` → **neden** böyle yapıldığı (ADR'ler)
5. `docs/sunum/teknoloji-kararlari.md` → her teknoloji tek tek

Ve en önemlisi: bu sıradaki hiçbir dosyayı okumasa bile **yanlış yaptığında CI
ona söyler.** Sürdürülebilirliğin tanımı bu.

### 4c. Diğer kararlar

- **Repository Pattern yok** (ödev §12 zorunlu tutmuyor). Prisma zaten repository
  soyutlaması; üstüne bir katman daha koymak `select`/`include` yeteneklerini
  kısıtlar ve ödevin uyardığı "her entity için aynı generic CRUD" tuzağına düşer.
  Servis katmanı Prisma'yı doğrudan kullanır, domain kuralları `packages/domain`'de
  saf kalır. → `ADR-005`
- **Redis neden var?** Sadece job için değil: hız sınırı sayacı, dağıtık kilit ve
  ileride cache. Kitin "Postgres sayaç tablosu" kuralı sunucusuz ortam içindi;
  burada kalıcı sunucumuz var. → `ADR-006`

---

## 5. Git barındırma — GitHub mı GitLab mı

### Önce temel: bu üçü neden ayrı ayrı var (sunuma girecek)

| | Nedir | Nerede çalışır |
|---|---|---|
| **git** | Sürüm kontrol **programı**. Değişiklikleri kaydeder (commit), dal açar, birleştirir | Senin bilgisayarında. **İnternet de şirket de gerekmez** — tek başına eksiksiz çalışır |
| **GitHub / GitLab** | git deposunun kopyasını tutan **barındırma servisleri** | İnternette veya kurumun kendi sunucusunda |

GitHub/GitLab, git'in vermediklerini ekler: kod inceleme ekranı, Pull/Merge
Request, issue takibi, yetki yönetimi, CI çalıştırıcıları.

**Benzetme:** git = **Word** (belgeyi yazan ve sürümlerini tutan program).
GitHub/GitLab = **Google Drive** (sakladığın, paylaştığın, yorum aldığın yer).
Word'süz Drive'a bir şey yazamazsın; Drive'sız Word yine çalışır ama kimseyle
paylaşamazsın.

**İkisinin farkı teknik üstünlük değil:** GitHub daha büyük, açık kaynak
dünyasının merkezi. GitLab **kurumun kendi sunucusuna kurulabildiği** için
kamu ve şirket tarafında yaygın — kod kurum dışına hiç çıkmaz. Belediyenin
tercih sebebi büyük ihtimalle tam olarak budur.

**Ne zaman hangisi:** git **her zaman**. GitHub mı GitLab mı — ekibin kodu
nerede tuttuğuna bağlı, senin seçimin değil.

### Bu projedeki durum

**Kontrol edildi:** `glab` (GitLab CLI) kurulu değil, ssh yapılandırmanda GitLab
yok, `benim-belediyem`'in remote'u GitHub. Yani **şu an GitLab hesabın yok.**

Netleştirilmesi gerekenler:

- **GitHub'a push etmek GitLab'a push etmek değildir.** İkisi ayrı barındırma
  servisi. Ortak olan `git`'in kendisi.
- **İş akışın birebir aynı kalıyor:** dal aç → çalış → uzağa gönder → ana dala
  birleştir. Sadece isimler değişir:

  | GitHub | GitLab |
  |---|---|
  | Pull Request (PR) | **Merge Request (MR)** |
  | GitHub Actions (`.github/workflows/`) | **GitLab CI** (`.gitlab-ci.yml`) |
  | `gh` CLI | `glab` CLI |

- **Bir repo birden fazla remote alabilir.** Aynı projeyi hem GitHub'a hem
  GitLab'a gönderebilirsin; "taşıma" diye ayrı bir iş yok.
- Belediyenin muhtemelen **kendi sunucusunda kurulu (self-hosted) GitLab'ı**
  var — gizlilik gerekçesi tam olarak bu, kod kurum dışına çıkmaz. Hesap ve
  adres onlardan gelir; sen şimdiden bir şey kurmak zorunda değilsin.

**Plan kararı — CI'ı platforma bağımlı yazmıyoruz:**

CI'ın gerçek adımları `package.json` script'lerinde yaşar (`pnpm ci:verify`).
`.github/workflows/ci.yml` ve `.gitlab-ci.yml` ikisi de o script'i çağıran
~30 satırlık **ince sarmalayıcı** olur. Kazancı üç yönlü:

1. Aynı kapıyı kendi makinende de tek komutla koşturabilirsin.
2. Hangi platforma gidersen git CI çalışır; mantık iki yerde kopyalanmaz (DRY).
3. Ödevin §27'sini **iki platformda birden** karşılamış olursun.

Geliştirmeyi GitHub'da sürdürürüz (hesabın ve `gh` hazır). Belediye GitLab
adresini verdiğinde ikinci remote eklenir, `glab` kurulur, `.gitlab-ci.yml`
zaten hazır bekliyor olur.

## 6. Kimlik doğrulama kararı

Kitin `05-auth-security.md` kuralı: **Web = httpOnly cookie, Mobil = Bearer JWT**.
Ödevin §5.1'i JWT + refresh + rol + pasif kullanıcı engeli istiyor. İkisi birlikte:

- Access token **httpOnly + Secure + SameSite=Lax** cookie'de (XSS'te JS okuyamaz).
  Aynı JWT stratejisi `Authorization: Bearer` başlığını da kabul eder → Swagger UI
  ve ileride Expo aynı API'yi kullanabilir.
- Refresh token **rotasyonlu**: her yenilemede eskisi iptal; **yeniden kullanım
  tespiti** varsa o kullanıcının tüm oturumları düşer. Token'ın kendisi DB'de
  **hash'lenmiş** durur.
- Token süreleri `.env` üzerinden (ödev §5.1 açıkça istiyor).
- Guard sırası: `JwtAuthGuard` → `RolesGuard`. Pasif kullanıcı `401`, yetkisiz rol
  `403`. Şifre `argon2id`.
- Refresh + eski token iptali **tek transaction** (§20).

---

## 7. Uygulama sırası — session planı

Her session: **plan → onay → kod → test → commit → PR → `/clear`**. Bir sonraki
session `docs/project/sonraki-adim-prompt.md`'den devralır (kitin `15-oturum-devri`
protokolü). Bu, context'in dolmasını engeller.

| # | Session | Çıktı |
|---|---|---|
| 0 | **Plugin güncelleme + kurulum** | `proje-kiti` **1.15.0**'a çıkar (aşağıya bak), `/yeni-proje` Adım 0–2 |
| 1 | PRD görüşmesi | `docs/project/PRD.md` — roller, iş kuralları, **KVKK §5.x**, kapsam dışı |
| 2 | Yol haritası + ilk ADR'ler | `roadmap.md`, ADR-001…006, `PORTLAR.md` |
| 3 | Monorepo iskeleti | pnpm+Turbo, Nest+Next ayağa kalkar, Compose, **iki CI dosyası** (GitHub + GitLab), `/health`, Swagger UI |
| 4 | Veri modeli | Prisma şeması, migration, seed, **DBML pipeline + CI kapısı** |
| 5 | Auth | JWT, refresh rotation, roller, guard'lar, argon2, testler |
| 6 | Lokasyon + Varlık | CRUD, filtre, Zod doğrulama, global hata filtresi |
| 7 | İş emri çekirdeği | Domain durum makinesi, transaction, geçmiş, concurrency |
| 8 | SLA Factory | Politikalar + factory + **unit testler** (§7'nin kalbi) |
| 9 | Job'lar | BullMQ worker, 4 job türü, idempotency, Bull Board (korumalı) |
| 10 | Bildirim + dashboard | Duplicate koruması, istatistik uçları |
| 11 | Frontend temeli | Auth akışı, protected route, ortak API katmanı, rol bazlı görünürlük |
| 12 | İş emri listesi | Sunucu tarafı filtre/sıralama/sayfalama + **URL senkronu** |
| 13 | İş emri detayı + kalan ekranlar | Durum/atama/yorum + 12 ekranın tamamı |
| 14 | Test tamamlama | Testcontainers integration, dependency-cruiser, Playwright E2E |
| 15 | Dokümantasyon + **sunum** | 8 zorunlu doküman, `AI_USAGE.md`, sunum dosyasının derlenmesi (her session'da parça parça yazılmış olacak) |
| 16 | Canlıya çıkış | Neon + hosting + son duman testi, canlı link |
| 17 | **Kite geri yazma** | `/kit-senkron` — bu projede kanıtlanan stack'ten bağımsız kuralları `proje-kiti`'ne kalıcı standart olarak işle (§11) |

**Süre tahmini (soruna cevap):** ~16–18 session, session başına 2–4 saat gerçek
zaman (kod üretimi hızlı, **inceleme ve düzeltme** yavaş) → toplam kabaca
**45–65 saat**. Günde 2 session yaparsan ~8–9 iş günü. Tek oturumda bitmez;
zaten bitirmeye çalışmak context dolduğu için kalite kaybettirir.

**Tarih baskısı yok** (senin kararın): ölçüt hız değil, projenin düzgün çıkması
ve açıklamaların anlamlı olması. Bu yüzden hiçbir session "yetişsin diye"
kısılmayacak. Bunun iki pratik sonucu:

- **Her session sonunda o session'ın kararları sunum dosyasına işlenir** — en
  sona bırakılmaz, çünkü gerekçe en iyi kararı verirken hatırlanır.
- Bir şey anlaşılmadıysa **o session kapanmaz.** Sunumun amacı teslim değil,
  senin canlı incelemede kodu sahiplenebilmen.

---

## 8. Sunum dosyası (senin özel isteğin)

İki biçimde, aynı içerik:

1. **`docs/sunum/teknoloji-kararlari.md`** — repoda, teslimin parçası.
2. **Artifact olarak yayınlanmış HTML sunum** — sana link olarak verilir,
   canlı incelemede açıp anlatırsın.

### Her kararın anlatım kalıbı

Kararlar üç kutuya ayrılıyor ve **hangi kutuda olduğu açıkça yazılıyor** —
canlı incelemede en çok işine yarayacak şey bu:

1. **"Ödev istedi, gerçek hayatta da doğrusu buydu."**
   Örn. PostgreSQL, Docker Compose, optimistic concurrency, Factory Pattern.
   → *"Ödevde zorunluydu; zaten serbest bırakılsa da aynısını seçerdim, çünkü…"*

2. **"Ödev şunu istedi, ben .js ailesindeki eşleniğini kullandım."**
   Örn. EF Core → Prisma, Hangfire → BullMQ, FluentValidation → Zod,
   xUnit → Vitest, React+Vite+Router → Next.js.
   → *"İstenen yeteneğin tamamı karşılanıyor, sadece araç JS tarafındaki
   karşılığı. Şu sebeple: …"*

3. **"Ödev istemedi ama gerçek hayat projesi bunu gerektirir."**
   Örn. Renovate, Testcontainers'ın rastgele portu, env'den gelen portlar,
   mimari testi, correlation ID.
   → *"Ödevde geçmiyordu; ama bu sistem yıllarca yaşayacak ve devredilecek,
   o yüzden ekledim. Çözdüğü somut problem şu: …"*

4. **"Ödev istedi ama yapmadım — şunu tercih ettim, şu sebeple."**
   Örn. AutoMapper yerine Zod şeması + Prisma `select`; Repository Pattern'i
   eklememek; Scalar yerine Swagger UI.
   → *"Serbest olsaydım da böyle yapardım / burada ödevin istediği araç bu
   stack'te şu somut soruna yol açıyordu: …"*
   ⚠️ Bu kutudaki her madde **ölçüyle** desteklenir (indirme sayısı, son yayın
   tarihi, peer kısıtı) — "bence daha iyi" yazmıyoruz.

### Her teknoloji için sabit şablon

Sunumda **kullanılan her teknoloji** — büyük küçük fark etmeksizin — aynı altı
başlıkla anlatılır. Örnek doldurulmuş hali (BullMQ):

> **BullMQ**
>
> **Nedir:** Node.js için iş kuyruğu (job queue) kütüphanesi. Verilerini
> Redis'te tutar.
>
> **Ne işe yarar:** Kullanıcıyı bekletmemesi gereken veya *ileri bir tarihte*
> çalışması gereken işleri sıraya alır. İki tür iş yapar: **gecikmeli iş**
> ("bu iş emrinin SLA'sına 2 saat kala hatırlatma üret") ve **tekrarlayan iş**
> ("her 15 dakikada bir SLA'sı geçmiş iş emirlerini tara"). İş hata alırsa
> otomatik yeniden dener, birden fazla worker'a dağıtılabilir.
>
> **Bu projede nerede kullanılıyor:** SLA hatırlatma, SLA ihlali taraması,
> günlük operasyon özeti, arşiv adayı belirleme (ödev §15'in dört job'ı).
>
> **Neden tercih ettik:** Node dünyasının fiili standardı (7.9M indirme/hafta),
> resmî NestJS entegrasyonu var (`@nestjs/bullmq`), Bull Board ile hazır
> yönetim ekranı geliyor ve worker sayısını artırarak yatay ölçekleniyor —
> yük arttığında mimariyi değiştirmeden büyür.
>
> **Alternatifi neydi, neden o değil:** `pg-boss` — Redis istemiyor, işleri
> Postgres'te tutuyor ve ödevin "Hangfire verileri PostgreSQL'de olsun"
> maddesine daha yakındı. Ama 1.35M indirme/hafta ile BullMQ'nun yanında niş
> kalıyor; uzun ömürlü bir belediye sisteminde daha az yaygın olanı seçmek
> devralacak geliştiriciyi zorlar.
>
> **Ödevdeki karşılığı:** Hangfire. Hangfire'ın yaptığı üç iş (gecikmeli job,
> recurring job, dashboard) burada birebir karşılanıyor. **Kutu 2** — istenen
> yetenek aynı, araç JS tarafındaki eşleniği.

Aynı şablon şunlar için de doldurulur: NestJS · Next.js · React · TypeScript ·
Prisma · PostgreSQL · Redis · Zod · argon2 · JWT · TanStack Query · Tailwind ·
shadcn/ui · React Hook Form · Vitest · Testcontainers · dependency-cruiser ·
Playwright · Docker · Docker Compose · pnpm · Turborepo · Swagger/OpenAPI ·
pino · nestjs-cls · Terminus · Renovate · GitHub Actions / GitLab CI.

⚠️ **§1'deki "Ödevde adı geçmeyen ama gereken parçalar" tablosundaki her satır da
bu şablonla açılır.** O tablodaki tek cümlelik notlar sadece plan içindir; sunumda
her biri altı başlıkla anlatılır. Örnek — Renovate şöyle yazılır:

> **Renovate** — **Nedir:** bağımlılıkları tarayıp güncel sürümler için otomatik
> PR/MR açan bot. **Ne işe yarar:** paket sürümlerini elle takip etme işini
> ortadan kaldırır; güvenlik yaması çıktığında haberin olur. **Bu projede:**
> haftalık çalışır, güncellemeyi PR olarak açar, CI zinciri o PR'da koştuğu için
> güncelleme bir şeyi bozuyorsa **merge etmeden önce** görürsün. **Neden tercih
> ettik:** "sürüm uyumunu elle takip etmeyelim" cümlesini mekanizmaya çevirmek
> için; niyet olarak bırakılan bakım işi yapılmaz. **Alternatifi:** Dependabot —
> daha basit ama **yalnızca GitHub'da** çalışır; belediye GitLab kullandığı için
> taşınamazdı. Renovate ikisinde de çalışır. **Ödevdeki karşılığı:** yok (ödev
> istememişti; sürdürülebilirlik için biz ekledik — **Kutu 1'in dışı**, "ödev
> istemedi ama gerçek hayat projesi bunu gerektirir" başlığı altına girer).

### İçerik listesi

- Yukarıdaki şablon, kullanılan **her** teknoloji için doldurulur — "küçük
  paket, geçiveririm" yok; canlı incelemede rastgele biri sorulabilir
- Ayrı slayt olacak sorular:
  "Neden Hangfire değil BullMQ?" · "Neden AutoMapper yok?" ·
  "Neden Repository Pattern yok?" · "Neden Redis eklendi?" ·
  "Neden Next.js — Vite ve React Router nerede?" (§1'deki açıklamanın tamamı) ·
  "Neden sahte veritabanıyla test etmiyoruz?" (§4b) ·
  "Modüler monolit ve Clean Architecture ne demek?" (§2'deki açıklamanın tamamı) ·
  "Neden konteyner portları standart ama host portları farklı?" (§3)
- **DI yaşam döngüsü tablosu**: Nest'te `DEFAULT` (singleton) / `REQUEST` /
  `TRANSIENT`, .NET'in `Singleton` / `Scoped` / `Transient` karşılığı, her servis
  için gerekçe (ödev §14 bunu **zorunlu doküman** olarak istiyor →
  `docs/lifecycle.md`)
- Mimari diyagramı: katmanlar ve bağımlılık yönleri
- SLA hesaplama kuralları tablosu (§7 dokümante edilmesini şart koşuyor)
- Bilinen teknik borçlar (§31 bunu açıkça soruyor)

**Yazım kuralı:** Sunum, kod okumayı gerektirmeden anlaşılmalı. Her başlık
"bu ne demek" ile başlar, sonra "neden böyle yaptık"a geçer. Amaç teslim etmek
kadar **senin öğrenmen** — mimar/proje yöneticisi seviyesinde uçtan uca hakimiyet.

### ⛔ "Sunumda açıklanacak" diye geçiştirme yok — her karar ÜÇ yerde yaşar

Planda *"sunumda açıklanacak"* diye bıraktığım her madde, aslında üç ayrı yere
yazılacak. Amaç: koda bakan biri **neden** öyle olduğunu kodu terk etmeden
görebilsin.

| Nerede | Ne yazar | Kime |
|---|---|---|
| `docs/sunum/teknoloji-kararlari.md` | Altı başlıklı tam anlatım (nedir · ne işe yarar · nerede kullandık · neden seçtik · alternatifi neden değil · ödevdeki karşılığı) | Sana ve değerlendirmeciye |
| İlgili `docs/*.md` | Teknik detay (ör. index kararları `database-decisions.md`) | Devralan geliştiriciye |
| **Kodun içinde kısa yorum** | Sadece **"neden"** — "ne"yi kodun kendisi anlatır | Koda bakan herkese |

Kod yorumunda kitin `02-coding-standards.md` kuralı geçerli:

- **"Ne" değil "neden" yazılır.**
- ⛔ **Bir "neden" yazmadan önce iddia ölçülür.** Yorum bir davranış iddiası
  içeriyorsa ("bu index aramayı hızlandırıyor", "bu sıra önemli"), iddia geçici
  olarak **bozulur** ve testin kırmızıya döndüğü **gözle görülür**. Dönmüyorsa
  iddia yanlıştır. *Yanlış bir gerekçe, yorumsuz bırakmaktan kötüdür* — sonraki
  geliştirici onu doğru sanıp üstüne karar kurar.

**Örnek — planda "sunumda açıklanacak" diye geçen metin araması maddesi
şu düzeyde yazılacak:**

> **Problem:** İş emri listesinde metin araması var. `WHERE title LIKE '%pompa%'`
> yazarsan Postgres **tüm tabloyu satır satır tarar**. 100 kayıtta fark etmez,
> 500 bin kayıtta ekran donar.
> **Index nedir:** Kitabın arkasındaki dizin. Postgres'in varsayılan index'i
> (B-tree) "şununla *başlayanları*" bulmakta hızlıdır ama **ortasında geçenleri**
> bulamaz — yani `%pompa%` aramasında hiç işe yaramaz.
> **pg_trgm:** Postgres eklentisi; metni üçlü harf parçalarına böler
> ("pompa" → "pom", "omp", "mpa"). Böylece "ortada geçiyor mu" sorusu
> "şu parçaları içeriyor mu" sorusuna dönüşür.
> **GIN:** Bu parçaları aranabilir hale getiren index türü.
> **Neden bunu seçtik:** Postgres'in içinde çalışır; Elasticsearch gibi ayrı bir
> sunucu gerektirmez. `unaccent` ile "Pompa/pompa" farkını da çözer.
> **Ne zaman yetmez:** Alaka sıralaması ve çok dillilik gerektiren gerçek tam
> metin aramada Postgres FTS veya ayrı arama motoru gerekir — bu proje için
> gereksiz karmaşıklık.
> **Kodun içindeki yorum ise tek satır olur:**
> `// GIN + pg_trgm: '%kelime%' araması B-tree ile tam tablo taraması yapardı`
> — ve bu iddia, index düşürülüp sorgu planına bakılarak **ölçülür**.

---

## 9. Session 0'da yapılacak ilk iş — plugin güncellemesi

Tespit: `installed_plugins.json` **1.2.0**'ı işaret ediyor ama önbellekte **1.15.0**
duruyor ve 1.15.0, GitHub'daki `bariskose-skills` HEAD ile **birebir aynı**
(fark yok — doğrulandı).

`/yeni-proje` skill'i kit dosyalarını önce `$CLAUDE_PLUGIN_ROOT` altında arıyor;
bu da 1.2.0'a çözülür. Yani güncellemeden başlarsak **eski standartlarla** kurarız.

→ **Sen `/plugin` → Manage Plugins → `proje-kiti@bariskose-skills` → Update**
yapacaksın (bu ekran interaktif, ben senin yerine tıklayamam). Sonra Claude'u
yeniden başlat, `/yeni-proje` ile devam ederiz.

1.2.0 → 1.15.0 farkının özeti: `00-stack.md`'ye **senkron sınırı** eklenmiş,
PRD şablonuna **KVKK m.11 hesap silme/veri indirme** zorunluluğu girmiş,
ayrıca 02/03/04/05/06/08/09/11/12/14/15/16 standartları ve roadmap+data-model
şablonları güncellenmiş. KVKK maddesi bu proje için doğrudan geçerli.

Diğer iki plugin (`agent-skills@addy-agent-skills`, `chrome-devtools-mcp`)
**zaten kurulu** — dokunulmayacak.

---

## 10. Doğrulama — bittiğini nasıl anlarız

Her session sonunda:

```bash
pnpm lint && pnpm typecheck && pnpm test        # birim testler
pnpm test:integration                            # Testcontainers, gerçek Postgres
pnpm test:arch                                   # dependency-cruiser katman kuralları
pnpm --filter web build && pnpm --filter api build
```

Proje bütünü için:

```bash
docker compose up --build          # tek komut, 4 servis
curl localhost:4100/health/ready   # Postgres bağlantısı dahil yeşil
open http://localhost:3100         # arayüz
open http://localhost:4100/docs    # Swagger UI (OpenAPI)
```

- **Port çakışması kontrolü:** diğer projeyi de aynı anda ayağa kaldır, ikisi
  birlikte çalışıyor mu bak (`docker ps` + iki tarayıcı sekmesi).
- **DBML kapısı:** şemayı elle boz, `pnpm dbml:check` kırmızı yanmalı.
- **Koruma testleri:** kitin `06-testing.md` kuralı gereği — durum geçişi
  koruması, pasif lokasyon kuralı ve concurrency kilidi için yazılan testler,
  **koruma geçici olarak kaldırılıp kırmızıya döndüğü gözle görülerek** kanıtlanır.
- **Tarayıcı doğrulaması:** `chrome-devtools-mcp` ile ekranlar fiilen tıklanır
  (konsol hatası, ağ isteği, boş liste/hata/yükleniyor durumları).
- **CI:** `pnpm ci:verify` zinciri (lint → typecheck → unit → integration → arch
  → build → docker build) **kendi makinende** yeşil; aynı script'i çağıran
  GitHub Actions da yeşil. `.gitlab-ci.yml` aynı script'i çağırdığı için
  GitLab'a taşındığında ayrıca uğraşılmayacak.

---

## 11. Kite geri yazılacaklar — `proje-kiti` bu projeden ne öğrenecek

Senin kuralın: **kit her projede standart yapı olacak; sadece stack değişecek,
geri kalan her şey kitten beslenecek.** O halde bu projede ortaya çıkan
**stack'ten bağımsız** eksikler kitte kalıcı hale gelmeli — yoksa bir sonraki
projede aynı eksiği yeniden keşfedersin.

Session 17'de `/kit-senkron` ile şunlar kite yazılacak (araç adı değil **kural**
yazılır; kural her stack'te geçerli olsun diye):

| Kite girecek kural | Hangi dosyaya | Neden kitte eksikti |
|---|---|---|
| **Mimari/katman testi zorunlu** — katman bağımlılıkları yorum değil, CI kapısı olur | `06-testing.md` | Kitte mimari testi **hiç yok** (doğrulandı). Test piramidinde de yer almıyor |
| **Entegrasyon testinde sahte veritabanı yasak** — gerçek DB konteyneri, rastgele portta | `06-testing.md` | Kitte "gerçek test veritabanı" yazıyor ama *mekanizma* ve *yasak* yazmıyor |
| **CI platforma bağımlı yazılmaz** — adımlar script'te, CI dosyası ince sarmalayıcı; GitHub/GitLab bölümü | `09-ci-cd-deploy.md` | Kit yalnızca GitHub Actions varsayıyor. Belediye GitLab kullanıyor |
| **Host portu env'den gelir, varsayılana güvenilmez** — aynı makinede iki proje çakışmasın | `13-environments.md` | Kit `:3000` ve `:5432`'yi sabit varsayıyor; ikinci proje açınca sessiz çakışma doğuyor |
| **Üretilen doküman CI'da doğrulanır** (DBML örneği) — "güncel tutulmalı" cümlesi kapıya çevrilir | `04-database.md` | Kit doküman güncelliğini niyete bırakıyor |
| **Bağımlılık güncellemesi bota devredilir** (Renovate) | `00-stack.md` veya `09` | Kitte sürüm politikası var ama *kim uygulayacak* yok |

### ✅ Kite YAZILDI (2026-08-17) — session 17'yi beklemeden

Aşağıdakiler `~/.claude/plugins/marketplaces/bariskose-skills` içinde **zaten
uygulandı** (henüz commit/push edilmedi):

| Değişiklik | Dosya |
|---|---|
| **Adım 1a — "Bu proje kimin için?"** sorusu (kendi projem / kurum projesi) + tam karşılaştırma tablosu | `SKILL.md` |
| **Adım 1b — Backend karar kuralı** (dört soru: Next tek başına mı, Next+Nest mi) + Express/REST/sürümleme/tip paylaşımı kararları | `SKILL.md` |
| **Adım 6 ikiye ayrıldı** — 6a "canlıya çıkar" (kendi projen) · 6b "teslim paketini hazırla ve doğrula" (kurum). 6b'de domain/sunucu/DNS/SSL adımları **hiç açılmıyor** | `SKILL.md` |
| **Adım 7 kontrol listesi** 6a/6b'ye göre ikiye ayrıldı | `SKILL.md` |
| **İş bölümü kuralı** — "yapabildiğini kullanıcıya yaptırma". Ajanın kendi yapacakları / onay isteyecekleri / gerçekten kullanıcıya bırakacakları (hesap açma, ödeme, 2FA, `/plugin` gibi interaktif ekranlar) net listelendi. `Auto` modu varsayılıyor | `SKILL.md` |
| **"Ayrı Express/Nest backend kurulmaz" mutlak yasağı → koşula çevrildi** + Nest/Express/REST/Prisma/BullMQ gerekçe tablosu | `00-stack.md` |
| **TypeORM "kullanılmayacaklar"a eklendi** (gerekçesiyle) | `00-stack.md` |

⚠️ **Yapılması gereken:** bu değişiklikler commit edilmedi. `claude plugin
update` çalıştırılırsa depo sıfırlanıp **kaybolabilirler**. Commit + push
edilmeli, sonra plugin 1.15.0'a güncellenmeli.

### Session 17'de hâlâ yapılacaklar

Ayrıca `00-stack.md`'ye şu kural da eklenmiş oldu, ama gerekçesi burada dursun:
mutlak yasak yerine **koşul** — *"Ayrı backend yalnızca şu dört durumda:
başka istemcinin tükettiği API · sürekli çalışan worker · DI yaşam döngüsü ·
kurumun kendi sunucusu. Aksi halde tek deploy hedefi."*

#### Bu kural ne diyor, Next.js neresinde

**Varsayılan = Next.js tek başına** (hem arayüz hem API, tek deploy hedefi).
Üç koşuldan **hiçbiri yoksa** ikinci bir sunucu eklenmez. **En az biri varsa**
eklenebilir — ama ADR yazılıp gerekçe bırakılır.

**Next.js her iki durumda da arayüz tarafında duruyor.** Kural onun varlığını
değil, *yanına ikinci bir sunucu eklenip eklenmeyeceğini* düzenler:

```
Koşullar YOKSA:   Next.js  →  hem arayüz hem API      (tek program)
Koşullar VARSA:   Next.js  →  sadece arayüz
                  NestJS   →  API + worker            (ikinci program)
```

Bu projede **üçü de** sağlanıyor:

| Koşul | Bu projedeki karşılığı |
|---|---|
| Bağımsız Web API tüketen ayrı istemci | Ödev, React istemcisinin tükettiği ayrı bir Web API istiyor. İleride Expo mobil de **aynı** API'yi kullanacak |
| Sürekli çalışan worker | Dört zamanlanmış job (SLA hatırlatma, ihlal taraması, günlük özet, arşiv). Sunucusuz modelde sürekli çalışan süreç barındırılamaz |
| DI yaşam döngüsü gereksinimi | Ödev §14 Transient/Scoped/Singleton tablosunu **zorunlu doküman** olarak istiyor; Next Route Handler'ların DI konteyneri yok |

Kuralın amacı yasaklamak değil, **bedeli bilerek ödetmek**: ikinci sunucu iki
deploy, iki kimlik doğrulama kurulumu, CORS ve iki yerde tekrarlanan tip demek.
Karşılığı varsa öde, yoksa ödeme.

⚠️ **Kite yazılmayacaklar** (bunlar bu projeye özgü, ADR'lerde kalır):
NestJS/BullMQ/Prisma seçimleri, SLA politikaları, iş emri durum makinesi.
Kit araç seçmez, **kural** koyar — o yüzden stack değiştiğinde kit bozulmaz.

Bu adım, kiti bir sonraki belediye projesinde **daha iyi** başlatacağı için
atlanmayacak; session 17 opsiyonel değildir.
