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

## 3. ⭐ Bir iş emrinin hayatı — sistemi baştan sona gösteren anlatım

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

---

## 4. ⏳ Bilinen teknik borçlar

Ödev §31 bunu açıkça soruyor. **Sorulmadan söylenir** — dürüstlük olumlu
değerlendirilir. Proje ilerledikçe doldurulacak.

---

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

---

## 6. Anlatım sırasında dikkat

**Her kararın hangi kutuda olduğunu söyle.** Dört kutu rehberin girişinde:
ödev istedi–zaten doğrusuydu · ödev istedi–JS eşleniğini kullandım · ödev
istemedi–gerçek hayat gerektirdi · ödev istedi–yapmadım.

Dördüncü kutudaki her madde **ölçüyle** desteklenir: indirme sayısı, son yayın
tarihi, sürüm kısıtı. *"Bence daha iyi"* denmez.

**En çok soru gelecek üç yer** — hazırlıklı ol:

1. *"AutoMapper nerede?"* → **E.6**, ölçümle
2. *"Next tek başına yetmez miydi?"* → **E.1**, dört koşul
3. *"Neden .NET değil?"* → giriş, üç ölçüt + hiçbir yeteneğin düşmediği tablosu

**Bölüm 5'i (bir iş emrinin hayatı) mutlaka anlat.** Değerlendirmeci "mimariyi
anlat" dediğinde çoğu aday katman isimlerini sayar. Tek bir isteğin yolculuğunu
anlatmak, mimariyi *anlatmadan göstermek* demektir.

---

## 7. Sunum yazım kuralları (kendime not)

- Her başlık **"bu ne demek"** ile başlar, sonra "neden böyle yaptık"a geçer
- Kod okumadan anlaşılmalı
- Her sapma **ölçüyle** desteklenir — "bence daha iyi" yok
- Her session bittiğinde o session'ın kararları **hemen** buraya işlenir;
  en sona bırakılmaz, çünkü gerekçe en iyi kararı verirken hatırlanır
