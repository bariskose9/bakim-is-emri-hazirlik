# Dosya rehberi — her dosya ne işe yarıyor

> **Bu dosya "bu da ne" sorusunun tek cevabı.** Projedeki her dosya burada
> açıklanıyor: ne işe yaradığı, **kimin okuduğu**, elle düzenlenip
> düzenlenmeyeceği.
>
> ⚠️ Bugün proje **hazırlık aşamasında** — henüz kod yok. Kod yazılmaya
> başlandığında yeni klasörler açılacak; onların ne olduğu **BÖLÜM 3**'te
> önceden anlatıldı.

---

# BÖLÜM 1 — Şu an var olan dosyalar

## Proje kökü

| Dosya | Ne işe yarıyor | Kim okur | Elle düzenlenir mi |
|---|---|---|:---:|
| `.gitignore` | **Git'e gönderilmeyecek** dosyaların listesi. Ödev metni ve gizli değerler burada listeli | Git | ✔ |
| `.gitattributes` | ⛔ Satır sonu kuralı: `* text=auto eol=lf`. Windows'un `CRLF` yapmasını engeller — yoksa Linux konteynerindeki betikler çalışmaz | Git | ✘ |
| `.vscode/extensions.json` | VS Code'un *"önerilen eklentiler var"* uyarısını çıkaran liste. **Install All** deyince hepsi kurulur | VS Code | ✔ |
| `.gstack/` | Üçüncü taraf bir aracın bıraktığı klasör. ⛔ **Projeyle ilgisi yok**, silinebilir | — | ✘ |
| `_devir/` | Hazırlık belgelerinin tamamı — aşağıda | İkisi | ✔ |

## `_devir/` — iki klasör, aynı içerik

| Klasör | İçinde | Ne için |
|---|---|---|
| **`md/`** | Markdown kaynak dosyaları | VS Code'da okumak, düzenlemek, **ajanın okuması** |
| **`pdf/`** | Aynı belgelerin PDF hâli | **Telefonda okumak** |

⭐ **Dosya adları birebir aynı** — yalnızca uzantı değişir. `md/KURULUM.md` ile
`pdf/KURULUM.pdf` **aynı belgedir**.

⚠️ **PDF'lerin hepsi karanlık temalıdır.** Yazdırman gerekirse söyle, aydınlık
sürüm üretilir — karanlık sürüm sayfayı simsiyah basar, kartuşu bitirir.

## Belgeler — tek tek

### `YENI-OTURUM` ⭐

**Kim okur:** **Ajan** (yeni Claude Code oturumu)

Projeyi devralan oturumun okuyacağı **ilk ve tek** dosya. İçinde: ajanın rolü
ve karakteri · hangi dosyayı hangi sırayla okuyacağı · `/yeni-proje`'nin
soracağı soruların **cevap anahtarı** · sana neyi sorup **neyi sormayacağı** ·
devralınan **açık işler**.

⭐ **Yeni oturumda yapıştıracağın tek satır:**

```
_devir/md/YENI-OTURUM.md dosyasını oku ve içindeki talimatları uygula.
```

---

### `odev`

**Kim okur:** İkisi · ⛔ **Depoda YOK**

Kurumun verdiği teknik değerlendirme çalışması — **gerçeğin kaynağı.** 32 bölüm.

| Sürüm | Ne için |
|---|---|
| `odev.docx` | Word aslı. ⚠️ VS Code gösteremez (ikili dosya) |
| `md/odev.md` | Okunabilir hâli — %99.8 karakter eşleşmesi doğrulandı |
| `pdf/odev.pdf` | Telefonda okumak için |

⛔ **Üçü de `.gitignore`'da.** Depo herkese açık; kurumun değerlendirme
çalışması yayımlanmaz. **Windows makinesine elle taşınmalı** (USB / OneDrive /
WhatsApp) — depoyu klonlamak yetmez.

⚠️ Çelişki görürsen **`odev.docx` kazanır**; `.md` yalnızca okuma kolaylığı.

---

### `proje-teknoloji-ve-plan` ⭐

**Kim okur:** İkisi · **~155 sayfa** — projenin en büyük belgesi

Üç soruyu birden cevaplıyor:

| # | Soru | Nerede |
|---|---|---|
| 1 | Hangi teknoloji seçildi ve **neden** | BÖLÜM **A** · **C** (23 kart) |
| 2 | O teknoloji **nedir**, ne işe yarar | BÖLÜM **C** · **E** (kavramlar) |
| 3 | Ne **sırayla** yapılacak | BÖLÜM **H** (17 adım) |

Ayrıca sistemin uçtan uca akışı: **BÖLÜM F** (sunucu tarafı) ve **BÖLÜM G**
(arayüz / UI tarafı).

⭐ Teknik incelemede savunacağın her gerekçe burada. **Tek seferde okunmaz** —
başvuru kitabı gibi, aranan bölüme gidilir.

---

### `KURUMDAN-OGRENECEKLERIM`

**Kim okur:** **Sen**

Kuruma soracağın soruların listesi. Her soruda üç şey var: **neden soruyorum ·
cevaba göre ne değişir · cevap gelmezse ne yaparım.**

⭐ **En kritiği §1.1:** Ödev §11 *"kurum tarafından **iletilen** veri tabanı
isimlendirme kuralları"* diyor ama **böyle bir doküman ödevin ekinde yok.**
Cevap geç gelirse tüm migration'lar yeniden yazılır — ilk gün sorulmalı.

⚠️ Cevaplar geldikçe `PRD.md` ve `altyapi-durumu.md`'ye işlenir; sonra bu dosya
silinir.

---

### `KURULUM`

**Kim okur:** **Sen**

Windows makinesinde ne kurulacağı ve nasıl başlanacağı. Üç bölüm: **A** ilk
kurulum (programlar, git, GitHub, eklentiler) · **B** bu projeye devam ·
**C** sonraki projeler.

⚠️ İçinde **üç hesabı karıştırma** tablosu var — en sık yapılan hata:
kişisel GitHub · belediyenin Claude hesabı · kurumun GitLab'ı.

---

### `sunum-anlatim-plani`

**Kim okur:** **Sen**

Teknik sunumun **anlatım sırası** — 30 dakikalık akış.

⛔ **İçinde bilgi yok, sadece yol tarifi var.** 46 bağlantının hepsi rehbere
işaret ediyor. Sebebi: aynı gerekçe iki yerde yazılmaz; biri değişince ikisi
birden değişmez.

---

### `calisma-kilavuzu`

**Kim okur:** **Sen** · ⚠️ **Bu projeye ait değil**

Kitle **herhangi bir** projenin nasıl yürütüleceğini anlatıyor: terimler,
`/yeni-proje`, ortam değişkenleri, canlıya çıkış yolları, komutlar.

⛔ **Kitten kopyalanır, her projede aynıdır.** Buradan düzeltmek işe yaramaz —
bir sonraki projede eski hâliyle geri gelir. Kural değiştirmek için
`/kit-senkron`.

---

### `DOSYA-REHBERI`

**Kim okur:** İkisi

Bu dosya. Projeyi ilk kez açan kişi *"bu da ne"* sorusunu buradan cevaplar.

---

# BÖLÜM 2 — Depoda olmayan dosyalar

⛔ Yerelde var, GitHub'a **gönderilmiyor**:

```
_devir/odev.docx        ← kurumun Word dosyası
_devir/md/odev.md       ← okunabilir hâli
_devir/pdf/odev.pdf     ← telefon için
```

**Sebep:** Depo herkese açık. Kurumun değerlendirme çalışması yayımlanmaz —
sonraki adaylar bulabilir ve kurum bunu güven ihlali sayabilir.

⭐ **Rehberdeki alıntılar sorun değil — ölçüldü:** ödevin yalnızca **%13'ü**
rehberde birebir geçiyor (286 cümlenin 37'si) ve hepsi yorumla birlikte kısa
gereksinim satırları. Bu normal **kaynak gösterme**; ödevi yeniden yayımlamak
değil.

⚠️ Bu üç dosya **git geçmişinden de temizlendi.** Yalnızca `.gitignore`'a
eklemek yetmezdi — bir kez commit edilmiş bir dosya geçmişte durur ve
`git show <commit>:<dosya>` ile geri alınabilir.

---

# BÖLÜM 3 — Kit nerede durur, projeye ne zaman gelir

## ⭐ İKİ AYRI ŞEY — en çok karışan yer

Kiti kurduğunda **iki şey** geliyor ama **aynı anda değil**:

| | **Komutlar (skill'ler)** | **Dosyalar** |
|---|---|---|
| Ne zaman gelir | ⭐ **Kurulur kurulmaz** | ⛔ Yalnızca `/yeni-proje` çalışınca |
| Nerede durur | `~/.claude/plugins/marketplaces/bariskose-skills/` | **Senin proje klasöründe** |
| `/yeni-proje` gerekir mi | ⛔ **Hayır** | ✅ Evet |

⭐ **Yani `/kit-senkron`, `/video-analiz`, `/pdf-uret` — üçü de kurulumdan
hemen sonra çalışır.** Bir proje kurmuş olman gerekmiyor.

## Kurulur kurulmaz kullanabildiğin komutlar

| Komut | Ne yapar |
|---|---|
| `/yeni-proje` | Yeni projeyi baştan sona kurar — dosyaları **o zaman** yerleştirir |
| `/kit-senkron` | Projede öğrenilen kuralı **kite geri yazar**, kitteki iyileştirmeleri projeye getirir |
| `/video-analiz <url>` | YouTube videosunu inceleyip kitte eksik olanı bulur |
| `/pdf-uret <dosya.md>` | Markdown'ı karanlık temalı PDF'e çevirir |

## Kitin kendi belgeleri — projene KOPYALANMAZ

Bunlar kitin kurulduğu klasörde durur, kitin **kendisini** anlatır:

| Dosya | Ne anlatır |
|---|---|
| `README.md` | Kit ne yapar, hangi komutlar var, klasör yapısı |
| `KURULUM.md` | Kit nasıl kurulur, hangi marketplace'ler eklenir |

## ⭐ "Kit kullanım kılavuzu" nerede — zaten sende

**`calisma-kilavuzu`** — bu klasörde duruyor ve **kitin kullanım kılavuzu odur.**
İçinde: terimler · `/yeni-proje` adımları · ortam değişkenleri · canlıya çıkış
yolları · komutların ne zaman kullanılacağı · takılınca ne yapılacağı.

⚠️ **Kitten kopyalanır, her projede aynıdır.** Kit güncellenince yenisi gelir;
buradan düzeltmek işe yaramaz.

---

## `/yeni-proje` çalışınca projeye YERLEŞECEK dosyalar

Bunların hepsi kitin `skills/yeni-proje/dosyalar/` klasöründen kopyalanır:

| Dosya | Ne işe yarıyor | Elle düzenlenir mi |
|---|---|:---:|
| `CLAUDE.md` | **Ajanın** çalışma protokolü ve rolü. Her projede aynı | ✘ |
| `CALISMA-KILAVUZU.md` | Kit kullanım kılavuzu — **senin** için | ✘ |
| `REPO-YAPISI.md` | Kod klasörlerinin haritası — **bu dosyanın kod tarafındaki devamı** | ✔ |
| `docs/standards/00–17` | **18 kural dosyası**: stack, mimari, kod standartları, API, veritabanı, güvenlik, test, git, CI, DoD, ajan düzeni, işletim, ortamlar, KVKK, oturum devri, kurulum, mobil | ⛔ **Asla** — `/kit-senkron` ile |
| `docs/standards/sablonlar/` | **12 şablon** — `docs/project/` altına açılacak boş belgeler | ✔ (açıldıktan sonra) |
| `.vscode/extensions.json` | Eklenti önerileri — VS Code *"Install All"* diyecek | ✔ |
| `.claude/settings.json` | ⭐ Ajanın **izin listesi**: `npm run test`, `lint`, `typecheck` gibi zararsız komutları her seferinde sormadan çalıştırır | ✔ |

## Bu projeye özel doldurulacaklar

| Dosya | Ne işe yarıyor |
|---|---|
| `docs/project/PRD.md` | ⭐ Sistem **ne yapacak** — roller, iş kuralları, kapsam dışı, varsayımlar |
| `docs/project/roadmap.md` | **Hangi sırayla** yapılacak — kutucuklu, nerede kalındığı görünür |
| `docs/project/data-model.md` | Veri modeli kararları |
| `docs/project/altyapi-durumu.md` | ⭐ **Kod dışında** ne yapıldı: hangi hesap, hangi panel, **neden** |
| `docs/project/decisions/ADR-*.md` | Önemli kararların gerekçesi |
| `docs/project/ogrendiklerim.md` | **Senin defterin** — *"Artık biliyorum"* listesi buradan okunuyor |
| `docs/project/sonraki-adim-prompt.md` | Sıradaki oturuma devir notu |

## Kodun kendisi

| Klasör | İçinde ne var |
|---|---|
| `apps/web/` | Next.js arayüzü (UI) — ⛔ iş kuralı yok |
| `apps/api/` | NestJS API — iş kuralları, transaction, yetki |
| `apps/worker/` | Arka plan işleri (BullMQ) — SLA hatırlatma, tarama |
| `packages/contracts/` | ⭐ Zod şemaları — **web ve api aynı şemayı** kullanır |
| `packages/domain/` | Saf iş kuralları — ⛔ Prisma/Nest/HTTP **bilmez** |

## Ayarlar ve üretilen dosyalar

| Dosya | Ne işe yarıyor | Elle düzenlenir mi |
|---|---|:---:|
| `.env.example` | Hangi ayarların gerektiğinin **listesi** — değerler boş | ✔ |
| `.env` | Gerçek değerler — ⛔ **asla commit edilmez** | ✔ |
| `docker-compose.yml` | Dört servisi tek komutla ayağa kaldırır | ✔ |
| `prisma/schema.prisma` | Veri modelinin **tanımı** | ✔ |
| `prisma/migrations/` | Veritabanı değişiklik geçmişi | ⛔ Üretilir |
| `docs/database.dbml` | Şema dokümanı — ⛔ **CI, güncel değilse kırmızı yanar** | ⛔ Üretilir |
| `node_modules/` | İndirilen paketler | ⛔ Asla |

---

# Nereye bakmalı — sık sorulanlar

| Soru | Cevap |
|---|---|
| Yeni oturuma nasıl başlarım | `md/YENI-OTURUM.md` — tek satır yapıştır |
| Windows'ta ne kuracağım | `md/KURULUM.md` |
| Kuruma ne soracağım | `md/KURUMDAN-OGRENECEKLERIM.md` |
| Şu teknoloji neden seçildi | `md/proje-teknoloji-ve-plan.md` → BÖLÜM **C** |
| SLA süreleri ne oldu | Aynı dosya → **E.4** |
| Sunumda ne anlatacağım | `md/sunum-anlatim-plani.md` |
| Kitle nasıl çalışılır | `md/calisma-kilavuzu.md` |
| Ödevde ne isteniyor | `md/odev.md` *(depoda yok, elle taşınır)* |
