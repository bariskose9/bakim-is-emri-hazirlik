# 01 — Mimari ve Klasör Yapısı

## Katmanlar (yukarıdan aşağıya, tek yön)

```
UI (React bileşeni)
   ↓ sadece veri ister, iş kuralı bilmez
API katmanı (route handler)   → doğrulama (Zod) + auth + HTTP çevirisi
   ↓
Servis katmanı (iş mantığı)   → kurallar burada. "Aynı gün ikinci randevu alınamaz" burada
   ↓
Repository katmanı (Prisma)   → sadece veri erişimi. İş kuralı içermez
   ↓
Veritabanı
```

**Kural:** Katman atlanmaz. Bileşen içinden Prisma çağrılmaz. Route handler içine
iş mantığı yazılmaz. Servis katmanı `Request`/`Response` nesnesi tanımaz.

## Klasör yapısı — özellik bazlı

```
src/
├── app/
│   ├── (public)/          → login gerektirmeyen sayfalar
│   ├── (protected)/       → login zorunlu sayfalar
│   ├── api/<kaynak>/      → route handler'lar
│   ├── layout.tsx
│   └── page.tsx
├── features/<özellik>/    → HER ÖZELLİK KENDİ KLASÖRÜNDE
│   ├── components/
│   ├── services/          → iş mantığı
│   ├── repositories/      → Prisma erişimi
│   ├── schemas/           → Zod şemaları
│   └── types.ts
├── components/ui/         → paylaşılan tasarım sistemi bileşenleri
├── lib/                   → auth, db client, http, cache, utils
└── config/                → sabitler, env okuma (tek yerden)
```

## Ayrı backend varsa — monorepo yapısı

Karar kuralı `00-stack.md` → "Backend kurgusu"nda. Ayrı backend seçildiyse
**tek git deposu** kullanılır (monorepo), pnpm workspaces + Turborepo ile.

```
apps/
├─ web/       → Next.js (yalnızca arayüz — Prisma'yı GÖRMEZ)
├─ api/       → NestJS (HTTP API)
├─ worker/    → NestJS (arka plan işleri, HTTP dinlemez)
└─ mobile/    → Expo (varsa)
packages/
├─ contracts/ → Zod şemaları + türetilen tipler — web, api, mobile buradan okur
└─ domain/    → saf iş kuralları (Prisma/Nest/HTTP bilmez)
```

⚠️ **Monorepo ≠ monolit.** Monorepo *kodun nerede durduğu*, monolit *programın
nasıl çalıştığı* hakkındadır. Bunlar bağımsız eksenlerdir.

**Polyrepo (ayrı depolar) neden değil:** ortak tipler özel bir npm paketi
olarak yayınlanmak zorunda kalır; her değişiklikte sürüm yükselt–yayınla–güncelle
döngüsü gelir ve iki depo arasında **sürümler kaçınılmaz olarak ayrışır.** Tek
mantıklı olduğu durum: depoların farklı ekiplere ve farklı yayın takvimlerine
ait olması.

### ⛔ Tip ve şema TEK yerde tanımlanır

Aynı veri şeklini iki projede ayrı ayrı yazmak yasaktır. Sebep somut: API'de
bir alanın adı değişir, diğer taraf güncellenmeyi unutur, **TypeScript hata
vermez** (kendi kopyasına bakıyordur) ve hata çalışma anında ekranda `undefined`
olarak çıkar.

Kural: şema `packages/contracts` içinde **bir kez** tanımlanır; API alanı
değiştiğinde tüketen taraf **derlenmez.** Hata ekrana değil, derleyiciye düşer.

## Servis yaşam döngüleri (ayrı backend varsa)

| Nest | .NET karşılığı | Ne zaman |
|---|---|---|
| `DEFAULT` (singleton) | `Singleton` | Durumsuz servisler: yapılandırma, sistem saati, politika sınıfları, mapper |
| `REQUEST` | `Scoped` | İsteğe özel veri: aktif kullanıcı, correlation ID |
| `TRANSIENT` | `Transient` | Nadir — gerekçesiz kullanılmaz |

⛔ **İstek bazlı veri singleton serviste tutulmaz.** Tutulursa iki kullanıcının
verisi karışır: *Ali'nin isteği Veli'nin bilgisiyle işlenir.* Bu hata tek
kullanıcılı testte **hiç görünmez**, yük altında ortaya çıkar ve kurumsal bir
sistemde yanlış kişinin verisini göstermek — yani KVKK ihlali — demektir.

⛔ **Captive dependency:** scoped bir servis singleton içine enjekte edilmez;
singleton onu ilk istekteki hâliyle dondurur.

**Aktif kullanıcı ve sistem saati** doğrudan statik yapılardan okunmaz;
`nestjs-cls` (AsyncLocalStorage) ve `Clock` soyutlaması üzerinden gelir —
her istek kendi izole bağlamında yaşar, ikisi de test edilebilir.
Arka plan işlerinde HTTP bağlamı **yoktur**; iş kendi bağlamını kurar.

## İsimlendirme
- Klasör ve dosya: `kebab-case` (`appointment-service.ts`)
- React bileşen dosyası: `PascalCase.tsx`
- Değişken/fonksiyon: `camelCase` · Tip/Interface: `PascalCase` · Sabit: `UPPER_SNAKE`
- Tüm kod isimleri **İngilizce**. Kullanıcıya görünen metinler Türkçe.

## Boyut sınırları
Dosya > 300 satır → böl. Fonksiyon > 50 satır → böl. İç içe if > 3 seviye → erken return.

## Veri akışı kuralları
- Sunucu bileşeni varsayılandır; `"use client"` sadece etkileşim gerekiyorsa.
- Gizli anahtar veya iş kuralı istemciye gönderilmez.
- Dış API çağrıları **sunucu tarafında** yapılır ve cache'lenir.
