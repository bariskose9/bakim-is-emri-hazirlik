# Teknik Sunum — Anlatım Planı

> **Bu dosya içerik değil, plan.** Anlatılacak her şeyin kaynağı
> `proje-teknoloji-rehberi.md` dosyasıdır (~42 sayfa). Burada yalnızca *hangi
> sırayla, ne kadar sürede ve nasıl* anlatılacağı duruyor.
>
> Aynı bilgi iki dosyada tutulmuyor: bir konunun gerekçesi değiştiğinde tek yer
> güncelleniyor.

---

## 1. Sunum akışı — hangi sırayla anlatacaksın

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

## 2. Hangi bölüm rehberin neresinden anlatılıyor

| Sunum bölümü | Rehberdeki kaynağı |
|---|---|
| Sistem nasıl çalışıyor | **BÖLÜM 0** — iki bilgisayar, isteğin yolculuğu, teknoloji–adım tablosu |
| Karar çerçevesi | Giriş — üç ölçüt ve **kararların dört kutusu** |
| Stack çevirisi | **A.1** ve **A.2** tabloları |
| Sistem ne yapıyor | **BÖLÜM B** — on iki talep, her biri hangi teknolojiyle |
| Mimari ve katmanlar | **E.1** modüler monolit + Clean Architecture |
| Teknoloji kartları | **BÖLÜM C** — 23 kart |
| Zor maddeler | **E.2** SOLID · **E.4** Factory · **E.5** durum makinesi · **E.6** mapping · **E.7** hata yönetimi · **E.8** eşzamanlılık |
| Test ve teslim | **C.7** Testcontainers · **C.11** Vitest · **C.8** mimari testi · **C.10** Docker |
| Dokümantasyon | **E.12** ADR ve AI_USAGE |

⭐ Sunumda **rehberdeki sırayı takip etme.** Rehber referans kitabı; sunum ise
bir anlatı. Sıra yukarıdaki akış tablosunda.

---

## 3. ⭐ Bir iş emrinin hayatı

Sunumun kalbi. Anlatım rehberde: **BÖLÜM F — Bir iş emrinin hayatı.**

On adımlık yolculuk, her adımın hangi karara dayandığı tablosuyla birlikte
orada. Bu bölümü anlatmak, mimariyi anlatmadan göstermek demektir — bu yüzden
akışta 6 dakika ayrıldı.

---

## 4. Bilinen teknik borçlar

Rehberde: **KAPANIŞ → Bilinen teknik borçlar.** Sunumda sorulmadan söylenir.

---

## 5. Muhtemel sorular ve hazır cevaplar

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

## 6. Anlatım sırasında dikkat

**Her kararı kutusuyla birlikte söyle.** Dört kutunun tanımı rehberin girişinde
(*"Kararların dört kutusu"*). Dördüncü kutudaki her madde **ölçüyle**
desteklenir — *"bence daha iyi"* denmez.

**En çok soru gelecek üç yer:**

| Soru | Rehberde nerede |
|---|---|
| *"AutoMapper nerede?"* | **E.6** — ölçüm tablosuyla |
| *"Next tek başına yetmez miydi?"* | **E.1** — dört koşul |
| *"Neden .NET değil?"* | **Giriş** — üç ölçüt + yetenek tablosu |

**Bölüm 3'ü atlama.** Akıştaki en uzun süre (6 dk) ona ayrıldı; sebebi rehberin
**BÖLÜM F** girişinde yazılı.

**Bilmediğin bir şey sorulursa:** *"Emin değilim, rehberde şu bölümde ayrıntısı
var, bakıp döneyim."* Uydurmaktan çok daha iyi karşılanır — rehber zaten
ekranda açık olacak.

## 7. Rehber güncellenirken (kendime not)

Rehberin yazım kuralları kitte tanımlı: `11-agent-workflow.md` → *"Dışarıya
giden doküman — anlatım standardı"*. Özeti:

- Her kavram **üç adımda** açılır: gerçek hayat örneği → yazılımdaki tanımı →
  bu projede tam olarak nerede
- Kod görülmeden anlaşılmayacak her başlıkta kısa örnek olur
- Doküman, sahibinin bilgi seviyesini ele vermez
- Aynı gerekçe iki yerde yazılmaz — biri diğerine başlığıyla işaret eder

⭐ **Her session bittiğinde o session'ın kararları rehbere hemen işlenir.**
En sona bırakılmaz; gerekçe, kararı verirken en net hatırlanır.
