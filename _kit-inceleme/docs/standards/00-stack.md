# 00 — Teknoloji Stack'i

Bu dosya "neyi kullanıyoruz, neyi kullanmıyoruz" sorusunun tek cevabıdır.
Burada olmayan bir kütüphane projeye eklenmeden önce **onay alınır** ve ADR yazılır.

## Zorunlu stack

Sürüm sütunu **fiilen kurulu** olanı gösterir; `package.json` ile birebir aynıdır.

| Katman | Seçim | Sürüm | Not |
|---|---|---|---|
| Framework | Next.js (App Router) | 16 | Pages Router kullanılmaz |
| Dil | TypeScript (strict) | 6 | JavaScript dosyası eklenmez · TS 7 henüz kullanılamıyor, aşağıya bak |
| Stil | Tailwind CSS | 4 | v4 CSS-first: `tailwind.config.ts` yok, token'lar `src/app/globals.css` içinde |
| UI bileşen | shadcn/ui (Radix tabanlı) | CLI 4 | Bileşen repoya kopyalanır, paket olarak bağlanmaz |
| Backend | Next.js Route Handlers — **veya** ayrı NestJS API | — | Karar kuralı aşağıda ("Backend kurgusu"). Çıplak Express kurulmaz |
| ORM | Prisma | 7 | Ham SQL sadece performans gerekçesiyle, ADR ile |
| Veritabanı | PostgreSQL | 18 | Local Docker imajı Neon'daki yama sürümüyle eşitlenir |
| Auth | Auth.js (NextAuth v5) | 5 (beta) | Web: httpOnly cookie · Mobil: Bearer JWT · aşağıya bak |
| Şifre özetleme | `argon2` (argon2id) | 0.45.1 | Parametreler `src/config/constants.ts` içinde · ADR-011 |
| Bot koruması | Cloudflare Turnstile | — | Giriş gerektirmeyen formlarda zorunlu · ADR ile kabul edildi |
| Validasyon | Zod | 4 | Her API girişinde zorunlu |
| Form | React Hook Form + Zod resolver | 7.83 / 5.5 | |
| Sunucu durumu | TanStack Query | 5 | Henüz kurulu değil — ilk gerçek liste ekranında eklenir |
| İstemci durumu | Zustand (sadece gerekiyorsa) | 5 | Redux kullanılmaz |
| Tarih | date-fns (+ `tr` yerel ayarı) | 4.4.0 | `moment.js` kullanılmaz |
| Unit test | Vitest + Testing Library | 4 | |
| E2E test | Playwright | 1.62 | Masaüstü + 375px mobil viewport |
| Erişilebilirlik denetimi | `@axe-core/playwright` | — | CI'da kritik ihlal = kırmızı |
| Performans denetimi | Lighthouse CI + `size-limit` | — | Performans bütçesi kapısı (`07-ui-design-system.md`) |
| Hata takibi | Sentry (`@sentry/nextjs`) | — | Ücretsiz katman |
| Hız sınırı | Ayrı paket yok — Postgres sayaç tablosu | — | Sunucusuzda bellek sayacı çalışmaz · ADR ile kabul edildi |
| Lint/Format | ESLint + Prettier | 9 / 3 | ESLint 10 kullanılamıyor, aşağıya bak |
| CI | GitHub Actions | — | |
| Hosting | Vercel | — | |
| Dosya depolama | Vercel Blob | — | Repoya dosya yüklenmez |
| Konteyner | Docker + Docker Compose | — | Sadece local geliştirme ve öğrenme amaçlı |
| Mobil | Expo (React Native) | — | Aynı REST API'yi tüketir |

## Sürüm tavanları — neden en yenisi değil

Bunlar tercih değil, **kısıt**. Kısıt kalkınca yükseltilir.

| Paket | Kullanılan | En yenisi | Neden yükseltilmedi |
|---|---|---|---|
| TypeScript | 6 | 7 | `typescript-eslint` TS 7'yi desteklemiyor (peer aralığı `<6.1.0`). TS 7'ye çıkmak lint kapısını tamamen devre dışı bırakırdı |
| ESLint | 9 | 10 | `eslint-config-next`'in içindeki `eslint-plugin-import` ve `eslint-plugin-jsx-a11y` en fazla ESLint 9 kabul ediyor |
| Node.js | 24 | 26 | 24 Active LTS; 26 henüz LTS değil (`00-stack.md` "Node.js LTS" kuralı) |

## Kimlik doğrulama — bilinen tuzak

`next-auth` (Auth.js) v5 hâlâ **beta** yayınlanıyor (`5.0.0-beta.*`); `latest`
etiketi v4'te. Daha önemlisi, **kaynak koddan doğrulanmış** bir sınır var
(`@auth/core` → `assert.ts`):

> `"Signing in with credentials only supported if JWT strategy is enabled"`

Yani **şifreyle giriş, veritabanı oturumu stratejisiyle çalışmıyor.** Oturumun
anında iptal edilebilir olması gerekiyorsa (çıkış ve şifre değişimi tüm
oturumları gerçekten düşürsün — `05-auth-security.md`) şifre girişi **elle**
yazılır; Auth.js yalnızca OAuth sağlayıcıları için kullanılır. Bu karar her
projede ADR ile kayda geçer.

## Backend kurgusu — Next tek başına mı, Next + NestJS mi

**Varsayılan: Next.js tek başına** (arayüz + Route Handler API, tek deploy
hedefi). Ayrı backend, ikinci bir deploy · CORS · kimlik doğrulamanın iki
tarafta kurgulanması · tiplerin elle paylaşılması · yerel geliştirmede dört
süreç demektir. Bu bedel **karşılığı varsa** ödenir.

Dört soru — **hepsi "hayır" ise Next tek başına, en az biri "evet" ise
Next (arayüz) + NestJS (API + worker):**

1. API'yi kendi web arayüzünden **başkası** tüketecek mi? (mobil, başka sistem)
2. Kullanıcı istek atmasa da **kendiliğinden** çalışması gereken iş var mı?
   (zamanlanmış görev, kuyruk, webhook karşılama)
3. Katmanlı mimari + **DI yaşam döngüsü** (singleton/scoped) + çok modüllü yapı
   gerekiyor mu?
4. Kod kurumun **kendi sunucusunda** mı çalışacak (sunucusuz platform yok)?

**Gerekçe:** Next Route Handler ile API yazılabilir ama üç şeyi veremez —
sürekli çalışan arka plan süreci, DI konteyneri ve yaşam döngüleri, zorlanan
katman sınırları. Bunlara ihtiyaç yoksa ikinci sunucu saf maliyettir.

⛔ **"Ayrı backend" kararı ADR'siz alınmaz.** Hangi koşulun sağlandığı yazılır.

Ayrı backend seçildiyse:

| Konu | Seçim | Gerekçe |
|---|---|---|
| Çatı | **NestJS** (çıplak Express değil) | Nest zaten Express'in üstünde çalışır; ayrıca modül, DI, Guard, Interceptor, Pipe, Filter getirir. Çıplak Express yalnızca 5–10 uçlu tek amaçlı serviste |
| HTTP adaptörü | **Express** (Nest varsayılanı) | İstek süresinin ~%95'i veritabanında geçer; HTTP katmanını hızlandırmak toplamda ölçülemez. Emek index'lere harcanır. *(Fastify adaptörü tek satırla değişir — ama ölçmeden geçilmez)* |
| API biçimi | **REST** | HTTP önbelleği kendiliğinden çalışır, uç bazında izlenir, DevOps tanır. *(GraphQL yalnızca **kontrol etmediğin** çok sayıda istemci farklı alan kombinasyonu istiyorsa; bu ihtimal ADR ile değerlendirilir)* |
| Sürümleme | `/api/v1/...` baştan | Kural `03-api-guidelines.md` → "Sözleşme ömrü"nde. Mobil varsa zorunlu |
| Monorepo aracı | pnpm workspaces + **Turborepo** | Yapı `01-architecture.md`'de. Nx daha güçlü ama kendi eklenti dünyasını getirir — bu boyutta gereksiz |
| Tip paylaşımı | `packages/contracts` | Zod şeması tek yerde; API alanı değişince frontend **derlenmez**, hata çalışma anına kalmaz |
| İş kuyruğu | BullMQ + Redis | Node'un fiili standardı; gecikmeli + tekrarlayan iş, retry, yatay ölçekleme |
| Log | `nestjs-pino` | JSON üretir; kurumsal toplama sistemleri düz metin toplayamaz (`12-operations-and-scaling.md`) |
| İstek bağlamı | `nestjs-cls` | Aktif kullanıcı ve correlation ID'yi katmanlara parametre geçmeden taşır; statik erişim yasağının karşılığı |

## Kullanılmayacaklar
- Redux / MobX — TanStack Query + Zustand yeterli
- **Çıplak Express backend** — Nest zaten Express'i içeriyor ve yapı getiriyor
- MongoDB — ilişkisel veri modeli kullanıyoruz
- jQuery, Bootstrap, Material UI — Tailwind + shadcn ile çakışır
- `moment.js` — yerine `date-fns`
- TypeORM — Prisma tercih edilir (4 kat yaygın, şema tek dosyada okunur,
  `synchronize` gibi veri kaybettiren bir kestirme yolu yok)

<!-- ⛔ SENKRON SINIRI — bu satırın ÜSTÜ kitle ortaktır ve kit-senkron tarafından
     eşitlenir. Projeye özel "kullanmıyoruz" maddeleri AŞAĞIYA yazılır.
     Gerekçesiz madde yazılmaz; sonraki oturum gerekçesiz yasağı anlamaz ve
     delmeye çalışır. Sınırı SİLME — silinirse genel yasaklar da senkrondan
     düşer ve kite yazılan yeni bir yasak bu projeye hiç ulaşmaz. -->

## Sürüm sütunu nasıl doldurulur

Yukarıdaki tablo bir **başlangıç noktasıdır**, kanıt değildir. Kurulum bitince
sürümler `package.json` ile **birebir eşitlenir**. En yenisi kullanılmıyorsa
**neden kullanılamadığı** yazılır — yoksa sonraki oturum "unutulmuş" sanıp
yükseltmeye çalışır ve aynı duvara toslar.

## Sürüm politikası
- Node.js LTS (>=20). Sürüm `.nvmrc` ile sabitlenir.
- Bağımlılıklar `package-lock.json` ile kilitlenir; `^` ile geniş aralık bırakılmaz.
  `package.json` içinde sürümler **tam** yazılır (`16.2.12`, `^16.2.12` değil).
- Major sürüm yükseltmesi ayrı PR olur, feature PR'ına karıştırılmaz.
- Bir bağımlılıkta yamalanmış sürüm varsa ama bağımlılık ağacı eskisini çekiyorsa,
  `package.json` → `overrides` ile yükseltilir ve gerekçesi PR'da yazılır.

## Yeni bağımlılık ekleme kuralı
Eklemeden önce sor ve şunu göster: ne işe yarıyor, alternatifi ne, paket boyutu,
son güncelleme tarihi, açık güvenlik uyarısı var mı. Tek fonksiyon için paket eklenmez.
