# Devir notu — bu projeyi devralan Claude Code oturumu için

> **Bu dosyayı okuyan sensin: yeni bir makinede, yeni bir hesapta açılmış,
> bu projenin geçmişini hiç görmemiş bir oturum.**
>
> Bu not sana üç şey verir: **neyin zaten kararlaştırıldığı**, **hangi dosyayı
> hangi sırayla okuyacağın**, ve **kullanıcıya neyi sorup neyi sormayacağın.**
>
> ⛔ **Buradaki kararlar yeniden açılmaz.** Her biri ölçümle verildi ve
> gerekçesi `proje-teknoloji-ve-plan.md` içinde yazılı. Bir karara katılmıyorsan
> önce oradaki gerekçeyi oku; hâlâ katılmıyorsan **kullanıcıya söyle**, tek
> başına değiştirme.

---

## 1. Durum — bir paragrafta

İzmir Büyükşehir Belediyesi bir teknik değerlendirme çalışması verdi: **Bakım ve
İş Emri Yönetim Sistemi**. Ödev .NET yığını şart koşuyordu, ancak kurum
*"istediğin stack'i kullan"* dedi. Bu yüzden her zorunlu teknoloji
JavaScript/TypeScript ailesindeki karşılığıyla değiştirildi — **hiçbir yetenek
düşürülmeden**. Bu hazırlık aşaması **bitti**. Kod henüz **hiç yazılmadı**.
Senin işin kodu yazmak.

Teslimden sonra **canlı teknik inceleme** var: kullanıcı her kararı
savunabilmeli. Bu yüzden "çalışıyor" yetmez, **anlatılabilir** olmalı.

⭐ **Kullanıcı hakkında bilmen gereken:** Kodu kabaca biliyor, hedefi mimar /
proje yöneticisi seviyesinde uçtan uca hakimiyet. Terimleri açıklamadan geçme;
kavramları **üç adımda** aç: gerçek hayat örneği → yazılımdaki tanımı → bu
projede tam olarak nerede. Kod bloklarına **satır satır Türkçe yorum** yaz.
(Kural: kitin `02-coding-standards.md` ve `11-agent-workflow.md` dosyalarında.)

---

## 2. ⛔ ÖNCE OKU — dosya sırası

**Sırayla oku. Üçüncü dosyanın tamamını bir kerede okuma** — 190 sayfa, bağlamı
gereksiz doldurur.

| # | Dosya | Ne verir | Nasıl oku |
|---|---|---|---|
| 1 | **`odev.docx`** | Gerçeğin kaynağı: 32 bölüm, iş kuralları, teslim listesi | **Tamamını.** ~26 KB, kısa |
| 2 | **bu dosya** | Devir notu | Zaten okuyorsun |
| 3 | **`proje-teknoloji-ve-plan.md`** | Tüm teknoloji kararları + kavramlar + yapım planı | ⚠️ **Aşağıdaki gibi parça parça** |
| 4 | `sunum-taslagi.md` | Sunumun iskeleti — her adımda büyütülecek | Bir kez göz at |

### 3. dosya nasıl okunur

**Şimdi oku (bağlamın ~%15'i):**

| Bölüm | Neden şimdi |
|---|---|
| Giriş + *Kararların dört kutusu* | Her kararın hangi gerekçeyle verildiği |
| **BÖLÜM 0** | Sistem nasıl çalışıyor — iki bilgisayar, isteğin yolculuğu |
| **BÖLÜM A** | Stack çeviri tablosu (.NET → JS) |
| **BÖLÜM H** | ⭐ **17 adımlık yapım planı** — senin yol haritan |

**Sonra, o adıma gelince oku:** BÖLÜM B (12 talep) · BÖLÜM C (23 teknoloji
kartı) · BÖLÜM E (kavramlar, SOLID, Factory, transaction, eşzamanlılık) ·
**BÖLÜM F** (bir iş emrinin sunucu tarafındaki hayatı) · **BÖLÜM G** (bir
ekranın arayüz/UI tarafındaki hayatı).

⭐ BÖLÜM H'deki her adımın sonunda **"Rehberde"** satırı var — o adımda hangi
bölümü okuyacağını söylüyor. Takip et.

### Okumana gerek OLMAYANLAR

`calisma-kilavuzu.md` (kullanıcının kılavuzu — kit zaten kopyalıyor) · `*.pdf`
(kullanıcının telefonda okuması için) · `NE-YAPACAGIM.md`,
`WINDOWS-ADIMLARI.md`, `OKU-ONCE.md`, `WHATSAPP-NOTU.txt` (kullanıcının kurulum
talimatları) · `kurulum-plani.md` (⛔ **eski taslak, bayat** — içeriği rehbere
taşındı, çelişki görürsen **rehber kazanır**).

---

## 3. İlk komut: `/yeni-proje` — ama cevap anahtarı hazır

Kurulumu kitin `/yeni-proje` skill'i yürütüyor. **Adım 1'de soracağı her sorunun
cevabı aşağıda.** ⛔ Bunları kullanıcıya sorma; *"şu şekilde alıyorum, yanlışsa
söyle"* diye **tek cümleyle doğrulat** ve devam et.

| `/yeni-proje` sorusu | Cevap | Gerekçe nerede |
|---|---|---|
| Bu proje kimin için? | **İşyeri projesi** | Kuruma teslim edilecek, deploy DevOps'ta |
| Proje tipi | **Web.** Mobil ileride (Expo) — API baştan buna hazır | Rehber A |
| Proje adı | `bakim-ve-is-emri-yonetim-sistemi` | — |
| Hedef kitle | Belediye personeli: yönetici · operasyon sorumlusu · teknik personel · talep sahibi | Ödev §5.1 |
| **1b-1** API'yi başkası tüketecek mi? | **EVET** — ödev §3 bağımsız Web API istiyor; ileride Expo mobil aynı API'yi kullanacak | Rehber E.1 |
| **1b-2** Kendiliğinden çalışan iş var mı? | **EVET** — dört zamanlanmış job (ödev §15) | Rehber C.6 |
| **1b-3** DI yaşam döngüsü gerekiyor mu? | **EVET** — ödev §14 Transient/Scoped/Singleton tablosunu zorunlu doküman olarak istiyor | Rehber C.1 §4 |
| **1b-4** Kurumun kendi sunucusunda mı? | **Muhtemelen evet** — kurum GitLab/kendi altyapısını kullanıyor | — |
| **Sonuç** | ⭐ **Next.js (arayüz) + NestJS (API + worker)** — dört koşuldan üçü net sağlanıyor | Rehber E.1 + ADR-002 |
| HTTP adaptörü | **Express** (Nest varsayılanı), Fastify değil | Rehber E.13 — ölçümle |
| API biçimi | **REST**, GraphQL yok | Rehber E.13 — izleme ve önbellek gerekçesi |
| Sürümleme | `/api/v1/...` baştan | Kit `03-api-guidelines.md` |
| Tip paylaşımı | Monorepo + `packages/contracts` (Zod) | Rehber E.3 |
| ORM | **Prisma**, TypeORM değil | Kit `00-stack.md` |

### Kurulacak eklentiler

Kit deposu **herkese açık** — kurumsal makinede kişisel GitHub hesabı bağlaman
**gerekmez**, kimlik doğrulaması istemez.

```
claude plugin marketplace add bariskose9/bariskose-skills
claude plugin marketplace add addyosmani/agent-skills
claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp

claude plugin install proje-kiti@bariskose-skills
claude plugin install agent-skills@addy-agent-skills
claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

⚠️ Kurduktan sonra **pencereyi yenile** (`Ctrl+Shift+P` → `Developer: Reload
Window`), yoksa çalışan sürüm hâlâ eskisidir. Kit sürümü **1.36.0 veya üstü**
olmalı.

---

## 4. Kullanıcıya SORACAKLARIN — kısa liste

Bunlar sadece kullanıcının (veya kurumun) bilebileceği şeyler. **Toplu değil,
tek tek sor.**

### A) Hemen, kuruluma başlamadan

| Soru | Neden gerekli |
|---|---|
| Proje hangi klasöre kurulacak? | `/yeni-proje` **boş klasör** ister |
| Docker Desktop kurulu ve **çalışıyor** mu? | Adım 2'de veritabanı konteyneri gerekiyor |
| Kod nereye gidecek — kurumun GitLab'ı mı, GitHub mı? Adres/hesap var mı? | CI dosyası buna göre yazılır. ⭐ **Yoksa engel değil**: CI adımları `package.json` betiklerinde yaşar, iki platform dosyası da ince sarmalayıcıdır. GitHub'da başlanır, adres gelince ikinci remote eklenir |
| **Teslim tarihi var mı?** | ⚠️ Ödevde yazmıyor. Varsa yol haritasının kapsamı buna göre ayarlanır |

### B) Adım 1'de (PRD görüşmesi) — en kritik adım

Ödev iş kurallarının **çoğunu zaten yazmış** (roller, durumlar, CRUD, kısıtlar).
⛔ Ödevde yazan bir şeyi kullanıcıya sorma — **oku.** Gerçekten açık kalanlar:

| Açık konu | Ödevdeki durumu |
|---|---|
| ⭐ **SLA süreleri ve hesaplama kuralları** | §7: *"tarafınızdan belirlemeniz beklenmektedir"* — dört öncelik için saat değerleri, ilk hatırlatma ve escalation zamanları. **Dokümante edilmesi zorunlu** |
| İş emri numarası biçimi | Yazmıyor — öner (`IE-2026-000148` gibi), onaylat |
| Çalışma saatleri SLA'ya dahil mi? | Yazmıyor — *"7/24 mü, mesai saatleri mi?"* Hesaplamayı kökten değiştirir |
| Gerçek lokasyon/varlık verisi var mı, yoksa uydurma mı? | Yazmıyor. ⛔ Gerçek kişisel veri **kullanılmaz** (§5.1) |
| Kapsam dışı ne? | Yazmıyor — PRD'de açıkça yazılmalı |
| KVKK: personel verisi işlenecek mi, nerede duracak? | Ödev secret'ları yasaklıyor; kişisel veri kararı kullanıcının |

⭐ **Kitin `interview-me` skill'ini kullan**, tek tek sor, cevap belirsizse üstüne git.

---

## 5. ⛔ SORMAYACAKLARIN

Bunlar karara bağlandı, gerekçeleri yazılı. Yeniden açmak kullanıcıya
**cevaplayamayacağı** bir mühendislik sorusu yöneltmektir — kitin
`11-agent-workflow.md` kuralı bunu yasaklıyor: **olgu sorulur, karar verilir.**

- Hangi framework / ORM / kuyruk / test aracı → **Rehber A ve C** (23 kart, ölçümlü)
- Mimari (modüler monolit + Clean Architecture) → **Rehber E.1**
- Mapping kütüphanesi (yok — Zod şeması + Prisma `select`) → **Rehber E.6 + ADR-004**
- Repository Pattern (yok) → **Rehber E.13**
- Fastify / GraphQL / pg-boss (hiçbiri) → **Rehber E.13**
- Kimlik doğrulama kurgusu (httpOnly cookie + Bearer, refresh rotation) → **Rehber C.15**
- Yapım sırası → **Rehber BÖLÜM H**
- Port ve konteyner adları → **Rehber C.10**

⚠️ **Ödev metni ile rehber çelişirse:** rehberdeki sapmalar **bilinçli** ve
gerekçeli (ör. AutoMapper yerine Zod). Rehber E.13 ve ADR'ler bunları açıklıyor.
Yeni bir sapma yapacaksan **ADR yaz** — açıklamasız sapma yasak.

---

## 6. İlk oturumda ne yapılacak

1. Bu dosyayı ve `odev.docx`'i oku
2. Rehberin **Giriş + BÖLÜM 0 + A + H** kısımlarını oku
3. Eklentileri kur, pencereyi yenile
4. §4-A'daki dört soruyu sor
5. `/yeni-proje` çalıştır — Adım 1 cevapları §3'teki tablodan, **doğrulat ve geç**
6. Adım 3 (PRD) → §4-B'deki açık konular
7. `/clear`, sonraki oturuma `docs/project/sonraki-adim-prompt.md` ile devret

⭐ **Oturum ritmi:** her roadmap adımı = bir oturum. Plan → onay → kod → test →
commit → PR/MR → `/clear`. Tahmin: **16–18 oturum**, toplam ~45–65 saat.
⛔ Tek oturumda bitirmeye çalışma — bağlam dolar, kalite düşer.

⚠️ **Her oturum sonunda o oturumun kararları `sunum-taslagi.md` ve ilgili ADR'ye
işlenir.** En sona bırakılmaz: gerekçe, kararı verirken en net hatırlanır.

---

## 7. Windows notları

Kit **Adım 0a**'da platformu kendisi tespit ediyor — sorma, tespit et ve tek
cümleyle söyle.

| Konu | Windows'ta |
|---|---|
| Kabuk | PowerShell (`&&` yerine `;`, yollar `\`) |
| Kit yolu | `C:\Users\<ad>\.claude\plugins\...` |
| Docker | Docker Desktop, **WSL2 backend** işaretli olmalı |
| ⛔ **Satır sonu** | Git satır sonlarını CRLF'e çevirir → Linux konteynerinde betikler çalışmaz. **`.gitattributes` içine `* text=auto eol=lf` ilk commit'te yazılır** |
| Node | LTS (24) — `.nvmrc` ile sabit |
| Portlar | Bu makinede paralel proje **yok**, varsayılanlar (3000/4000/5432/6379) serbest. `.env` boş bırakılabilir |

---

## 8. Bitiş kriteri

Ödev §30'un teslim listesinin tamamı + değerlendirmecinin **tek komutla**
(`docker compose up --build`) sistemi ayağa kaldırabilmesi + kullanıcının her
kararı savunabilmesi.

Her adım sonunda yeşil olması gerekenler:

```
pnpm lint && pnpm typecheck && pnpm test
pnpm test:integration      # Testcontainers, gerçek PostgreSQL
pnpm test:arch             # dependency-cruiser katman kuralları
pnpm --filter web build && pnpm --filter api build
```

⭐ **Koruma testleri iddia değil ölçümle kanıtlanır:** durum geçişi koruması,
pasif lokasyon kuralı ve eşzamanlılık kilidi için yazılan testler, koruma
**geçici olarak kaldırılıp kırmızıya döndüğü gözle görülerek** doğrulanır
(kit `06-testing.md`).
