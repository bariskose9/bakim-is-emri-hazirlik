# Oturum özeti — 24–25 Ağustos 2026

> **Bu belge, sohbette anlatılanların telefonda okunabilir hâli.** Terminale
> bakmana gerek yok; burada ne istediğin, ne yapıldığı ve **senden ne beklendiği**
> yazılı.

---

# BÖLÜM 1 — Senin verdiğin düzeltmeler ve karşılıkları

## 1.1 Çalışma kılavuzunda

### API tüketici sorusu
Senin yazdığın gibi düzeltildi. Üstüne şu netlik eklendi: **kendi web arayüzün
ve kendi mobilin bu soruya "hayır"dır** — ikisini de sen yazıyorsun, ne
isteyeceklerini biliyorsun.

### `.env.example` — en büyük ekleme
`benim-belediyem` projesine bakıp **gerçek** listeyi çıkardım:

- **11 kategori**: çekirdek · veritabanı · portlar · kimlik · KVKK şifreleme ·
  kuyruk · e-posta · dosya yükleme · hata takibi · yasal · dış servisler.
  Her satırda *"ne zaman gerekir"* kolonu var — hangisi her projede, hangisi
  modül eklendikçe geliyor
- **Değer nereden gelir** — dört kaynak işaretle gösterildi:

| İşaret | Anlamı | Nasıl elde edilir |
|---|---|---|
| 🎲 | Üretilir | Terminalde `openssl rand -base64 32` |
| ✍️ | Sen seçersin | Bir değer belirlersin, tutarlı olması yeter |
| 📋 | Panelden kopyalanır | Servise üye olursun, panelinde yazar |
| 🔗 | Türetilir | Başka değerlerden birleşir |

- `DATABASE_URL`'in hangi dört parçadan oluştuğu şemayla gösterildi
- ⛔ **`NEXT_PUBLIC_` ön eki uyarısı** — bu ön ekli değer tarayıcıya **gömülür**,
  kullanıcı F12 ile okur. `NEXT_PUBLIC_DATABASE_URL` yazmak veritabanı şifreni
  siteye basmak demektir
- Yeni bir değişken eklenince dokunulacak **dört yer**

### "Kendi değerlerini yazar" — sorduğun şeyin cevabı
**Hiçbir ayarın tek doğru değeri yok.** Tabloyla gösterildi:

| Değişken | Senin bilgisayarında | Canlıda |
|---|---|---|
| `DATABASE_URL` | Docker'daki kap | Belediyenin sunucusu |
| `WEB_PORT` | 3100 (3000 doluydu) | 3000 |
| `JWT_SECRET` | rastgele | **tamamen başka** bir rastgele |

⛔ `JWT_SECRET` üç ortamda **zorunlu olarak farklı** olmalı. Aynı olursa senin
laptopundan sızan sır canlı sisteme giriş açar.

### Canlıya çıkış — yeni BÖLÜM 5B
Sorduğun tam soruydu: *"domain + AWS + Docker mı, yoksa benim-belediyem'deki
gibi mi?"* Üç yol katman katman yazıldı:

| Yol | Neler kullanılıyor | Ne zaman |
|---|---|---|
| **A — Yönetilen** | Vercel · Neon · Cloudflare · Resend · Sentry · Upstash | Kendi projen, hızlı çıkmak istiyorsun |
| **B — Kendi sunucun** | AWS/Hetzner · **Docker Engine** · Caddy+SSL · `pg_dump` yedek | Veri kurum dışına çıkamaz, ya da öğrenmek istiyorsun |
| **C — Kurumun DevOps'u** | Sen sadece git'e gönderirsin | İşyeri projesi — **en olası** |

*"Docker'ı oraya mı kuruyoruz"* → **evet.** Kendi bilgisayarında Docker Desktop
var (Engine + arayüz + sanal makine). Sunucuda arayüze gerek yok, yalnızca
**Docker Engine** kurulur. Sonrası birebir aynı: `docker compose up -d`, aynı
`docker-compose.yml`. Tek fark `.env` dosyası.

C yolunda **senden beklenmeyenler** de listelendi: alan adı almak, sunucu
kiralamak, DNS/SSL, yedekleme, SSH.

### `altyapi-durumu.md`
Her satır artık **NE · NEREDE · NEDEN** yazıyor. Örnek:

> *2026-08-24 · Neon'da "bakim-prod" açıldı, bölge: Frankfurt*
> *Neden: KVKK — kişisel veri AB/Türkiye içinde kalsın. ⛔ Bölge sonradan
> DEĞİŞTİRİLEMEZ.*

Ayrıca dosya **ikiye ayrıldı**: kendi projende *"yapıldı defteri"*, işyeri
projesinde *"gereksinim listesi"* — çünkü paneli DevOps açıyor, sen sadece
neyin neden gerektiğini anlatıyorsun.

### `/kit-senkron`
"Gerek kalmadı" demiştin ama iki sorun cevapsızdı, onlar eklendi:

- **GitHub'a kim gönderiyor:** ajan. Push öncesi sana soruyor
- **Başka makinede güncelleme:** `claude plugin update proje-kiti@bariskose-skills`
  + **Reload Window**
- Üç yerdeki sürüm farkı tablosu: GitHub / indirilen kopya / çalışan sürüm.
  ⚠️ En sık karışan yer: güncelleme yapılır ama pencere yenilenmez

---

## 1.2 Teknoloji rehberinde

### "Emeği index'lere harcadım"
Açıldı. **Index nedir:** kitabın arkasındaki dizin. **"Emek" nedir:** mühendisin
sınırlı zamanı. Nereye harcarsan:

| Nereye | Ne kadar iş | Kazanç |
|---|---|---|
| Express → Fastify | Adaptör değiştir, eklentileri düzelt, ekibe öğret | **0.15 ms** |
| Doğru index | Yavaş sorguyu bul, `EXPLAIN`'e bak, tek satır yaz | **75 ms** |

Anlatılan şey teknoloji değil, **öncelik muhakemesi**: darboğazın olduğu yeri
hızlandır.

### N+1 sorgu ve DataLoader
50 öğrencinin velisini toplama örneğiyle: **51 arşiv gidişi vs 2.** Kodun yanlış
ve doğru hâli satır satır yorumlu yazıldı.

- **REST'te risk düşük:** cevabın şeklini sen yazıyorsun, `include`'u bir kez
  doğru yazarsın
- **GraphQL'de yüksek:** cevabın şeklini istemci belirliyor, kimse yanlış kod
  yazmadan N+1 kendiliğinden oluşuyor

> **Ölçtüm:** `dataloader` **13.6M indirme/hafta**, yerini alan alternatif
> çıkmamış. Son yayını 2024-12 ama bu **terk edilmişlik değil tamamlanmışlık** —
> tek iş yapıyor ve bitirmiş. `graphql-yoga` (1.7M) ve `@apollo/server` (2.9M)
> ikisi de aktif ve ikisi de DataLoader öneriyor. **Cevap değişmemiş, sana
> sormaya gerek kalmadı.**

Ama asıl istediğin şey **kite yazıldı** → BÖLÜM 2'ye bak.

### GraphQL gerçekten ne zaman kazanır
Beş tüketicili somut senaryoyla açıldı: ilçe belediyesi · yüklenici · 153
sistemi · açık veri portalı · üniversite. Beşi farklı alan istiyor, hiçbirini
sen yazmıyorsun.

⭐ **Ayrım cümlesi:** *"İstemci sayısı sabit ve hepsi bende ise REST; açık uçlu
ve ihtiyaçlarını ben bilmiyorsam GraphQL."*

### Silinen
REST/GraphQL *"ne türden şey"* tablosu — dediğin gibi kaldırıldı.

### "Geniş arayüzlerde toplanmamalı"
Sorunun kaynağı bulundu: **"arayüz" kelimesi iki ayrı şeyi karşılıyor.**

| Hangi arayüz | İngilizcesi | Nedir | Kim görür |
|---|---|---|---|
| Kullanıcı arayüzü | **UI** | Ekran, buton, form | İnsan |
| Kod arayüzü | **interface** | Sınıfın söz verdiği metot listesi | Sadece kod |

SOLID'in I harfi **ekranla ilgisiz**. İşe alım ilanı benzetmesiyle açıldı.
Ayrıca **E.0'a Türkçe/İngilizce sözlük** eklendi: uç=endpoint, arka uç, ön uç,
istemci, jeton, kap, kuyruk, iş, kütüphane, çatı.

### "Aynı şema sunucuda tekrar doğrular"
7 satırlık tablo: **hangi adım hangi bilgisayarda çalışıyor.**
`packages/contracts` tek kopya, hem tarayıcı hem sunucu onu içeri alıyor.
`ZodValidationPipe` controller'a girmeden kapıda bekliyor.

**Neden iki kez:** `curl` örneğiyle — kullanıcı senin ekranını kullanmak zorunda
değil, isteği elle gönderebilir. Tarayıcıdaki doğrulama **nezaket**,
sunucudaki **güvenlik**.

### `version` kolonu
Kısa cevap: **kütüphane değil, Prisma şemasında bir `Int` kolon.**

- Word belgesi / baskı numarası benzetmesi
- Üretilen SQL
- İki kullanıcılı zaman çizelgesi: Ayşe 10:05'te kaydeder, Mehmet 10:06'da
  **409** alır
- İyimser/kötümser kilit karşılaştırması

---

## 1.3 Yeni: BÖLÜM G — Bir ekranın hayatı

Sorduğun frontend akışı **yoktu**, yazıldı. BÖLÜM F'nin tam ikizi, ~370 satır.
Eski BÖLÜM G (yapım planı) → **BÖLÜM H** oldu.

| Alt bölüm | İçerik |
|---|---|
| **G.0** | Önce arka uç mu arayüz mü — **kuralın gerçek hâli: "önce VERİ ŞEKLİ kesinleşsin"** + üç istisna |
| **G.1** | Katmanlar: TypeScript → React → Next.js, üstünde Tailwind / shadcn / TanStack Query / RHF / Zod. Hangisi **hangi soruyu** cevaplıyor |
| **G.2** | On adımlık ekran akışı, satır satır Türkçe yorumlu kodla |
| **G.3** | Hangi karar nerede görünüyor |

⭐ **G.0 senin sorduğun istisnayı çözüyor:** *"bazen backend hazır oluyor, sadece
UI değişiyor"* — evet, o zaman sıra tersine döner. Ve İzmir'de karşılaşacağın en
olası durum bu. O hâlde Adım 1–9 atlanır, **mevcut API'nin sözleşmesi
`packages/contracts`'a Zod şeması olarak yazılır**, Adım 10'dan başlanır.

---

# BÖLÜM 2 — Kite kalıcı yazılanlar (sürüm 1.36.0)

| Kural | Nereye | Ne yapıyor |
|---|---|---|
| **Anlatım düzeyi sabit değil** | `11-agent-workflow.md` | Ajan *"Artık biliyorum"* listesini okur, seviyene göre konuşur. **Listeye eklemeyi ajan teklif eder** — bir konu 3. kez soru sorulmadan geçildiğinde. Senin hatırlaman beklenmez |
| **Her teknolojinin alternatifi taranır** | `00-stack.md` | Sürüm ve indirme sayısı **fiilen ölçülür**, hafızadan aktarılmaz. Son yayını 18 aydan eskiyse veya daha yaygını çıkmışsa **sana sorulur**. ⛔ Ajan tek başına stack değiştiremez. Tarihsiz rakam yazılamaz |
| *"Önce ekran"* yasağı **koşula** çevrildi | `16-yeni-proje-kurulumu.md` | Üç istisna tabloyla |
| *"Artık biliyorum"* bölümü | `ogrendiklerim.md` şablonu | Kite gitmez, **projeler arası taşınır** |
| `altyapi-durumu` NE·NEREDE·NEDEN + iki mod | şablon | — |
| Seviye listesi yeni projeye taşınır | `SKILL.md` Adım 2 | Altıncı projede birinci projenin diliyle konuşulmaz |

⭐ **"Kişiliğim de kite yazılsın" isteğin ikiye ayrıldı:** **mekanizma** kite
gitti (herkese açık, kural olur), **profilin** kalıcı hafızaya yazıldı (kişisel
bilgi). Kendi koyduğumuz ayırt edici test bunu gerektiriyordu.

---

# BÖLÜM 3 — Yeni Claude oturumu için devir planı

**Dosya: `_devir/YENI-OTURUM.md`** — yeni oturumun okuyacağı tek belge.

## 3.1 "Bana soru sorması lazım mı?" — cevabı

**Şu an hayır.** İki noktada soracak, ikisi de kısıldı:

### A) Kuruluma başlarken — 4 soru
Teknik karar değil, sadece senin bilebileceğin olgular:

1. Hangi klasöre kurulacak
2. Docker Desktop çalışıyor mu
3. Kod GitLab'a mı GitHub'a mı gidecek
4. ⚠️ **Teslim tarihi var mı**

> **Ödevin 795 satırını taradım — teslim tarihi hiçbir yerde geçmiyor.**
> Kurumdan öğrenmen gereken tek şey bu. GitLab adresi yoksa engel değil; CI
> adımları `package.json` betiklerinde yaşıyor, iki platform dosyası da ince
> sarmalayıcı.

### B) PRD görüşmesinde — 6 konu
Ödevi baştan sona taradım: **iş kurallarının çoğunu zaten yazmış** (roller,
durumlar, CRUD, kısıtlar). Devir notuna ⛔ **"ödevde yazan bir şeyi kullanıcıya
sorma — oku"** kuralı kondu. Gerçekten açık kalanlar:

| Konu | Ödevdeki durumu |
|---|---|
| ⭐ **SLA süreleri ve hesaplama kuralları** | §7 kelimesi kelimesine: *"tarafınızdan belirlemeniz beklenmektedir"* — dört öncelik için saat değerleri, ilk hatırlatma ve escalation. Dokümante edilmesi **zorunlu** |
| SLA 7/24 mü, mesai saatlerinde mi? | Yazmıyor — hesabı **kökten** değiştirir |
| İş emri numarası biçimi | Yazmıyor — ajan önerir, sen onaylarsın |
| Gerçek veri mi, uydurma mı | Yazmıyor. ⛔ Gerçek kişisel veri kullanılmaz |
| Kapsam dışı ne | Yazmıyor — PRD'de açıkça yazılmalı |
| KVKK: personel verisi nerede duracak | Karar senin |

### C) ⛔ Sormayacakları
Stack · mimari · mapping · Repository Pattern · Fastify/GraphQL/pg-boss ·
kimlik doğrulama · yapım sırası · portlar. Hepsi karara bağlandı, her biri
rehberdeki gerekçesine işaretli.

**Gerekçe:** kitin kendi kuralı — *olgu sorulur, karar verilir.* Sana
cevaplayamayacağın mühendislik sorusu yöneltilmemeli.

## 3.2 Hangi dosyaları okuyacak

| # | Dosya | Nasıl |
|---|---|---|
| 1 | `odev.docx` | **Tamamı** — 26 KB |
| 2 | `YENI-OTURUM.md` | Devir notu |
| 3 | `proje-teknoloji-ve-plan.md` | ⚠️ **Parça parça** |
| 4 | `sunum-taslagi.md` | Göz atma |

⭐ **3. dosya için özel talimat:** 190 sayfayı bir kerede okumasın. Önce
Giriş + BÖLÜM 0 + A + H (yapım planı) — bağlamın ~%15'i. Kalanını adımı
geldiğinde okur; BÖLÜM H'nin her adımının sonunda *"Rehberde: …"* satırı var.

**Okumayacakları:** kılavuz (senin) · PDF'ler (senin) · Windows kurulum

## 3.3 Taşıma — dikkat edilecek tek şey

| Depo | Görünürlük | Sonucu |
|---|---|---|
| `bariskose-skills` (kit) | **PUBLIC** | ✅ Windows'ta eklentiler **kimlik doğrulaması olmadan** kurulur |
| `bakim-is-emri-hazirlik` | **PRIVATE** | ⚠️ `_devir/` içeriğini **elle taşıman** gerekiyor |

**Taşınacak dört dosya** (~290 KB, PDF'ler hariç):

```
YENI-OTURUM.md
odev.docx
proje-teknoloji-ve-plan.md
sunum-taslagi.md
```

USB, OneDrive veya kişisel GitHub ile clone — üçü de olur.

## 3.4 Yeni oturumu açınca yapıştıracağın metin

```
_devir/YENI-OTURUM.md dosyasını oku ve içindeki talimatları uygula.
```

---

# BÖLÜM 4 — Yol boyunca çıkan düzeltmeler

Bunları sen istemedin, ben denk geldim:

| Bulgu | Ne yapıldı |
|---|---|
| **Port ve konteyner planı rehberde yoktu** — yalnızca eski taslakta duruyordu. Teslim belgesinde olması gereken bir şey | **C.10'a taşındı**: konteyner içi port sabit, host portu `.env`'den, `bakim-` ön ekli adlar, Docker Desktop'tan port değiştirmenin neden kalıcı olmadığı |
| Eski dosya adı 4 yerde kalmış | `proje-teknoloji-rehberi.md` → `proje-teknoloji-ve-plan.md`. "40 sayfa" → "~190 sayfa" |
| Sunumda *"~58 sayfa"* ve *"6 dk"* bayat kalmış | 190 sayfa · 5+3 = 8 dk olarak düzeltildi |
| Sunumda port sorusu yoktu | İki soru eklendi, C.10'a işaretli |

---

# BÖLÜM 5 — ⭐ SENDEN BEKLENENLER

Ajanın yapamayacağı, yalnızca senin yapabileceğin işler:

## Yarın / yakında

| # | İş | Neden sen |
|---|---|---|
| 1 | ⚠️ **Kurumdan teslim tarihini öğren** | Ödevde yazmıyor; yol haritasının kapsamı buna bağlı |
| 2 | Kurumun **GitLab adresi ve hesabı** var mı öğren | CI dosyası buna göre. Yoksa engel değil |
| 3 | `_devir/` içindeki **dört dosyayı** Windows makinesine taşı | Depo private, ajan taşıyamaz |
| 4 | Windows'ta programları kur: Git · Node 24 LTS · **Docker Desktop (WSL2)** · VS Code · GitHub CLI · `pnpm` | Kurulum ekranları interaktif |
| 5 | Eklentileri kur + **Reload Window** | `/plugin` ekranı interaktif |

## Belgeler

| # | İş |
|---|---|
| 6 | Dört belgeyi oku, düzeltmelerini söyle |
| 7 | **SLA sürelerini düşünmeye başla** — dört öncelik için: tamamlanma süresi, ilk hatırlatma, escalation. Bu senin kararın ve ödevde açıkça isteniyor |

## Bu makinede kalanlar

`caffeinate` açık · kit 1.36.0 GitHub'da · proje deposu güncel · PDF'ler
`~/Downloads` içinde (açık + karanlık).

⚠️ **Kit 1.36.0'ın bu Mac'te devreye girmesi için Reload Window gerekiyor** —
`/yeni-proje` çalıştıracağın güne kadar acelesi yok.
