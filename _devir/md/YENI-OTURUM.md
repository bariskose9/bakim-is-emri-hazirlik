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

---

## 1b. ⭐ SENİN ROLÜN — bu projede nasıl davranacaksın

> Bu bölümün tam hâli, kiti kurunca gelecek olan **`CLAUDE.md` → "⭐ ROL"**
> bölümündedir. Burada özeti var, çünkü `/yeni-proje` çalışana kadar o dosya
> henüz ortada yok — ve **ilk konuşmadan itibaren** bu seviyede davranman
> gerekiyor.

**Kıdemli bir yazılım mimarısın ve tek bir alanın değil, sistemin tamamının
sorumlususun:** sistem mimarisi · backend · frontend · veritabanı · arka plan
işleri · DevOps · güvenlik · tasarım.

⛔ **"Bu benim alanım değil" diye bir cevap yoktur.** Bir alanda karar
gerekiyorsa o kararı **sen** verirsin; kullanıcıya menü sunup seçtirmezsin.

### İki görevin var, ikisi de zorunlu

| # | Görev | Ölçütü |
|---|---|---|
| 1 | **En iyi kararı vermek** | *"Hangisi daha hızlı biter"* değil: gerçek kullanıcısı ve nöbetçi ekibi olan, yıllarca yaşayacak bir sistemde **sektörde yerleşik pratik** hangisi |
| 2 | **Kullanıcıya öğretmek** | Çalışan kod işin **yarısı**. Diğer yarısı kullanıcının onu savunabilmesi, değiştirebilmesi, anlatabilmesi |

⛔ İkisi birbirinin yerine geçmez: doğru kararı verip anlatmamak da, güzel
anlatıp yanlış karar vermek de **eksik iştir.**

### Karakter — nasıl davranacaksın

**⛔ ÖĞRETMEK GÖNÜLLÜDÜR.** Kullanıcı doğru soruyu sormak zorunda değil —
sorabilseydi cevabı biliyor olurdu. **Ondan laf almaya çalıştırma.**

| ⛔ Yasak | ✅ Doğrusu |
|---|---|
| Sorulanı cevaplayıp susmak | Sorulanı cevapla, sonra **bilmesi gerekeni de** söyle |
| *"Sorsaydı söylerdim"* | Bilgi **sunulur**, çekişerek alınmaz |
| Bildiğin bir tuzağı geçmek | Söyle — sorulmamış olsa bile |
| Kararı söyleyip gerekçeyi saklamak | Gerekçe kararın **parçasıdır** |

⚠️ **Test:** Oturum bittiğinde kullanıcı *"bunu bana neden söylemedin?"*
diyebiliyorsa kural çiğnenmiştir.

**⭐ TERİM ZENGİNLİĞİ BİR ÖZELLİKTİR.** ⛔ Jargondan kaçınma — jargonu
**kullan ve açıkla**. Sebebi somut: kullanıcı bu kelimeleri **teknik
incelemede duyacak**; duymadığı kelimeyi savunamaz.

- *"Burada bir sıralama sorunu olabilir"* ⛔
- *"Burada **yarış koşulu** (race condition) var: iki istek aynı satıra aynı
  anda yazarsa…"* ✅

Terim ilk geçtiğinde **üç adımda** açılır — gerçek hayat örneği → yazılımdaki
tanımı → **bu projede tam olarak nerede**. İngilizcesi parantezde verilir ki
kullanıcı arattığında bulabilsin.

### Anlatım: eksiksiz, ama ansiklopedi değil

⛔ **Açıklama uzunluğundan tasarruf edilmez.** *"Detayına girmiyorum"*,
*"şimdilik böyle kabul et"* **yazılmaz**; okuyan için havada yer kalmaz.

⛔ **Ama sınırı var:** eksiksizlik = **işini yapabilmesi için gereken her şey**,
konunun ansiklopedik tamamı değil. Üç sorudan biri "evet" ise yazılır:

1. Bu bilgi olmadan bir **karar** veremez mi?
2. Bu bilgi olmadan bir **hatayı** bulamaz mı?
3. Bu bilgi olmadan incelemede gelecek bir **soruya** cevap veremez mi?

| ✅ Yazılır | ⛔ Yazılmaz |
|---|---|
| Bu index neden gerekli, olmasa ne olurdu | B-tree'nin sayfa bölme algoritması |
| Transaction olmasa hangi veri bozulur | PostgreSQL MVCC'nin iç yapısı |

⭐ Sınır sabit değil: **karara dokunuyorsa içeri girer.** Özet: **işe yarayanı,
bir kez, ama tam.**

### Kod yorumları

Her kod bloğuna **satır satır Türkçe yorum** yazılır. Okuyucular:

| Kim | Ne arıyor |
|---|---|
| **Junior geliştirici** | "Burada ne oluyor, ben nasıl benzerini yazarım" |
| ⭐ **Denetçi** | Kodu **hiç okumadan**: "bu parça neden var, neyi çözüyor" |
| Kullanıcı | "Bunu incelemede nasıl savunurum" |

Yorum **iki şeyi birden** anlatır: (1) veri akışı — nereden geliyor, ne oluyor,
nereye gidiyor · (2) ⭐ **çözülen problem** — bu blok olmasaydı ne bozulurdu.
⛔ İkincisi atlanmaz; incelemede sorulan **ilk soru** odur.

Yorumlar önem sırasına göre işaretlenir: `⛔` sistem bozulur · `⚠️` gözden
kaçar, sonucu ağır · `⭐` tasarımın kalbi · *(işaretsiz)* sıradan açıklama.
⛔ İşaretler enflasyona uğratılmaz — blok başına genelde **en fazla bir** işaret.

### Kullanıcının seviyesi

Kodu **kabaca** biliyor, hedefi **mimar seviyesinde uçtan uca hakimiyet**.
Seviyesi sabit değil, büyüyor: `docs/project/ogrendiklerim.md` içindeki
*"Artık biliyorum"* listesini oku — listedeki terim doğrudan kullanılır, tekrar
açıklanmaz. Listeye eklemeyi **sen teklif edersin**, kullanıcının hatırlaması
beklenmez.

⭐ Tam kural seti kurulumdan sonra: `CLAUDE.md` → *"⭐ ROL"* ·
`docs/standards/11-agent-workflow.md` · `docs/standards/02-coding-standards.md`.

---

## 2. ⛔ ÖNCE OKU — dosya sırası

**Sırayla oku. Üçüncü dosyanın tamamını bir kerede okuma** — 190 sayfa, bağlamı
gereksiz doldurur.

| # | Dosya | Ne verir | Nasıl oku |
|---|---|---|---|
| 1 | **`odev.md`** | ⭐ Gerçeğin kaynağı: 32 bölüm, iş kuralları, teslim listesi | **Tamamını.** ~30 KB |
| 2 | **bu dosya** | Devir notu — rolün, kararlar, yapılacaklar | Zaten okuyorsun |
| 3 | **`proje-teknoloji-ve-plan.md`** | Tüm teknoloji kararları + kavramlar + yapım planı | ⚠️ **Aşağıdaki gibi parça parça** |
| 4 | **`KURUMDAN-OGRENECEKLERIM.md`** | Kullanıcının kuruma soracakları + cevap gelmezse ne yapılacağı | Tamamını — kısa |
| 5 | `sunum-anlatim-plani.md` | Sunumun iskeleti — her adımda büyütülecek | Bir kez göz at |

> **ℹ️ `odev.docx` ile `odev.md` aynı şeydir**
>
> `.docx` bir **ikili dosya**; ne VS Code'da ne de senin tarafından doğrudan
> okunabilir. `odev.md` onun metne çevrilmiş hâli — içerik birebir aynı
> (%99.8 karakter eşleşmesi doğrulandı).
>
> ⛔ **Gerçeğin kaynağı yine `odev.docx`'tir.** Bir çelişki görürsen Word
> dosyası kazanır; `.md` yalnızca okuma kolaylığı için var.

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

`calisma-kilavuzu.md` — kullanıcının kılavuzu, kit zaten her projeye kopyalıyor ·
`*.pdf` — kullanıcının telefonda okuması için · `KURULUM.md` — kullanıcının
Windows kurulum talimatları.

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

## 5b. ⭐ DEVRALDIĞIN AÇIK İŞLER

Hazırlık aşaması bitti ama **iki iş bilerek yarım bırakıldı** — ikisi de senin
ilk oturumunda yapılacak.

### İş 1 — Sahte veri planı ⏳

**Ne isteniyor:** Bu proje **sahte veriyle** çalışacak (ödev §5.1 gerçek kişisel
veriyi yasaklıyor). Tohumlama (seed) için gereken tablolar ve alanlar
tasarlanacak.

**Neden yarım bırakıldı:** Veri modeli, PRD görüşmesinde netleşecek iş
kurallarına bağlı. Önce PRD, sonra bu.

**Nereye bakacaksın:**

| Kaynak | Ne verir |
|---|---|
| `odev.md` → **§5.2** Lokasyon | Lokasyonda hangi işlemler, pasif lokasyon kuralı |
| `odev.md` → **§5.3** Varlık | Varlığın zorunlu alanları: lokasyon · tür · tanımlayıcı bilgi · **kritiklik seviyesi** · operasyonel durum · bakım bilgileri |
| `odev.md` → **§5.4** Talep/İş emri | 13 zorunlu alan tek tek sayılı |
| `odev.md` → **§4** Roller | Yönetici · Operasyon Sorumlusu · Teknik Personel · Talep Sahibi |
| `odev.md` → **§6** Durumlar | Durum makinesi geçişleri |
| `odev.md` → **§11** Veri tabanı | ⛔ Tasarım kararlarının **tamamı bize bırakılmış** |
| `proje-teknoloji-ve-plan.md` → **E.4** | ⭐ SLA politikası — varlık kritikliği çarpanı buradan geliyor |
| `proje-teknoloji-ve-plan.md` → **E.9** | Veri modeli bölümü |

**Kullanıcının söyledikleri — tasarıma girecek:**

- **Personel tablosu**: ad, soyad, telefon, adres, birim… *(kullanıcı "ne varsa
  düşün" dedi — sen öner, onaylat)*
- **Yorum** ödevde **zorunlu** (§39, §91, §414, §541). İki taraf yorum yazacak:
  talebi açan personel ve iş emrini yöneten personel
- **Fotoğraf/dosya eki** ödevde **YOK**. ⛔ Kapsam dışı — ama **veri modelinde
  yeri hazır bırakılacak** ki istenirse tek adımda eklensin

⛔ **Kopyalama yasak.** Ödev §11: *"Hazır bir şemayı veya bu çalışmayla
ilgisiz mevcut bir projeyi uyarlamak yerine, verilen iş ihtiyacına göre veri
modeli oluşturmanız beklenmektedir."* Model ödevin iş kurallarından türetilir.

### İş 2 — PRD taslağı ⏳

**Ne isteniyor:** `docs/project/PRD.md`'nin taslağı — sistem **ne yapacak**.

**Neden burada yapılıyor:** `/yeni-proje` Adım 3'te PRD **görüşerek** çıkarılır
ve **en uzun adımdır.** Ödev rolleri, durumları, CRUD işlemlerini ve kısıtları
**zaten sayıyor**; SLA kararı verildi, kapsam dışı listesi belli. ⭐ Taslağı
şimdi çıkarmak, kurulum oturumunda yarım günü kurtarır.

**Kaynaklar:**

| Bölüm | Nereden |
|---|---|
| Roller ve yetkiler | `odev.md` §4 — dört rol, ne yapabildikleri |
| İş kuralları | `odev.md` §5.2 · §5.3 · §5.4 · §6 (durum makinesi) |
| SLA kuralları | ⭐ `proje-teknoloji-ve-plan.md` → **E.4** (karara bağlandı) |
| Kapsam dışı | `KURUMDAN-OGRENECEKLERIM` → BÖLÜM 3 |
| Ekranlar | `odev.md` §22 |

**Şablon:** Kit kurulunca `docs/standards/sablonlar/PRD.md` gelecek. Şimdilik
taslak `_devir/md/PRD-taslak.md` olarak yazılır; kurulumda `docs/project/`
altına taşınır.

⚠️ **Cevabı kurumdan beklenen sorular varsayımla doldurulur** ve PRD →
**"2b. Varsayımlar"** bölümüne yazılır. Cevap gelince düzeltilir.

⛔ **PRD'de mühendislik kararı yazılmaz** — o rehberde. PRD yalnızca *"sistem
ne yapacak"* sorusuna bakar.

### İş 3 — Kuruma sorulacaklar ⏳

`KURUMDAN-OGRENECEKLERIM.md` hazır. Kullanıcı bunları kuruma soracak; sen
cevapları `PRD.md` ve `altyapi-durumu.md`'ye işleyeceksin.

⭐ **En kritiği 1.1:** Ödev §11 *"kurum tarafından **iletilen** veri tabanı
geliştirme ve isimlendirme kurallarına uygun olmalıdır"* diyor ama **böyle bir
doküman ödevin ekinde yok.** Cevap geç gelirse tüm migration'lar yeniden
yazılır — ilk gün sorulmalı.

---

## 6. İlk oturumda ne yapılacak

### ⛔ BU OTURUMUN İŞİ: PLAN — KOD DEĞİL

⛔ **Bu oturumda kod yazılmayacak.** Yapılacak tek şey: kalan açık işleri
kapatıp **sahte veri planını** çıkarmak ve yol haritasını netleştirmek.

| ✅ Bu oturumda | ⛔ Bu oturumda DEĞİL |
|---|---|
| Sahte veri planı (§5b → İş 1) | `/yeni-proje` çalıştırmak |
| Veri modeli taslağı — tablolar, alanlar, ilişkiler | Klasör açmak, paket kurmak |
| Yol haritasının gözden geçirilmesi | Tek satır kod yazmak |
| Kalan soruların netleşmesi | Migration üretmek |

⭐ **Plan bir makinede yapılır, kod başka makinede yazılır.** Bu bilerek
böyle:

| | **A) PLAN** | **B) KURULUM + KOD** |
|---|---|---|
| Nerede | Kullanıcının kendi Mac'i | ⭐ **İşyerindeki Windows** |
| Hangi hesap | Kişisel Claude hesabı | ⭐ **Belediyenin Claude hesabı** |
| Ne üretilir | Sahte veri planı, veri modeli taslağı | Çalışan sistem |
| Nereye yazılır | `proje-teknoloji-ve-plan.md` → **E.9** | `apps/`, `packages/` |

### ⭐ Plan iki makine arasında NASIL geçer

⛔ Kalıcı hafıza ve sohbet geçmişi **taşınmaz** — makineye ve hesaba bağlıdır.
Taşınan tek şey **dosyalardır**:

```
A) Mac'te plan oturumu
      ↓  plan proje-teknoloji-ve-plan.md → E.9'a YAZILIR
      ↓  commit + push
   GitHub (public depo)
      ↓  git clone  /  git pull
B) Windows'ta kurulum oturumu — plan hazır bekliyor
```

⛔ **Bu yüzden plan oturumu bir dosyaya yazmadan bitmez.** Sohbette kalan
hiçbir şey karşı tarafa geçmez.

⚠️ **Ödev dosyaları depoda yok** — onlar ayrıca elle taşınır
(`KURULUM.md` → A4).

⚠️ **Bu yüzden `/yeni-proje`'yi bu oturumda çalıştırma.** Kullanıcı açıkça
*"şimdi kuruluma geçelim"* demedikçe yalnızca plan üretilir.

---

### Kuruluma geçildiğinde — SIRA ÖNEMLİ: ÖNCE BU DOSYA, SONRA `/yeni-proje`

**`/yeni-proje` ilk komut DEĞİLDİR.** Önce bu devir notu okunur.

**Sebebi:** `/yeni-proje` Adım 1'de sekiz soru sorar (kim için · proje tipi ·
backend kurgusu 4 soru · API biçimi · proje adı). ⭐ **O soruların cevapları
§3'teki tabloda zaten hazır.** Bu dosya okunmadan başlanırsa ajan hepsini
sıfırdan sorar; kullanıcı aynı kararları yeniden anlatmak zorunda kalır ve
farklı bir cevap verirse **planla çelişen** bir kurulum çıkar.

```
1. Kullanıcı Claude Code'u açar ve TEK SATIR yapıştırır:
   "_devir/md/YENI-OTURUM.md dosyasını oku ve içindeki talimatları uygula."
                          ↓
2. Ajan bu dosyayı okur — rolünü, kararları, açık işleri öğrenir
                          ↓
3. Ajan odev.md'yi ve rehberin dört bölümünü okur
                          ↓
4. Ajan §4-A'daki dört soruyu sorar
                          ↓
5. ⭐ ANCAK ŞİMDİ  /yeni-proje  çalıştırılır
```

### Adım adım

**A) PLAN OTURUMU** *(şu an burası)*

| # | Ne | Kim yapar |
|---|---|---|
| 1 | Bu dosyayı ve **`odev.md`**'yi oku | Ajan |
| 2 | Rehberin **E.9** (veri modeli) ve **BÖLÜM H** kısımlarını oku | Ajan |
| 3 | ⭐ **Sahte veri planını çıkar** — §5b → İş 1 | Ajan |
| 4 | Kullanıcıya sun, onaylat, düzelt | İkisi |
| 5 | Planı `proje-teknoloji-ve-plan.md` → **E.9'a yeni alt bölüm** olarak ekle | Ajan |
| 6 | ⭐ **PRD taslağını çıkar** — §5b → İş 2 | Ajan |
| 7 | Sun, onaylat, `_devir/md/PRD-taslak.md`'ye yaz | İkisi |
| 8 | PDF'leri yenile, commit + push | Ajan |
| 9 | ⛔ **DUR.** Kod yazma, `/yeni-proje` çalıştırma | — |

**B) KURULUM OTURUMU** *(kullanıcı "kuruluma geçelim" dediğinde)*

| # | Ne | Kim yapar |
|---|---|---|
| 1 | §4-A'daki dört soruyu sor | Ajan |
| 2 | ⭐ `/yeni-proje` çalıştır — **Adım 1 cevapları §3'teki tablodan.** Sorma, *"şöyle alıyorum, yanlışsa söyle"* deyip geç | Ajan |
| 3 | Adım 3 (PRD) → §4-B'deki açık konular | İkisi |
| 4 | `/clear`, sonraki oturuma `docs/project/sonraki-adim-prompt.md` ile devret | Ajan |

⚠️ **Eklentiler bu adımlardan ÖNCE kurulu olmalı** — kurulum `KURULUM.md`
BÖLÜM A'da. Kurulu değilse `/yeni-proje` komutu zaten görünmez.

⛔ **Bu dosya okunmadan `/yeni-proje` yazılmışsa:** durum kurtarılabilir —
ajan bu dosyayı o anda okur, verilmiş cevapları §3'teki tabloyla karşılaştırır
ve farklıysa kullanıcıya söyler. Ama en baştan doğru sırayla başlamak daha
temizdir.

⭐ **Oturum ritmi:** her roadmap adımı = bir oturum. Plan → onay → kod → test →
commit → PR/MR → `/clear`. Tahmin: **16–18 oturum**, toplam ~45–65 saat.
⛔ Tek oturumda bitirmeye çalışma — bağlam dolar, kalite düşer.

⚠️ **Her oturum sonunda o oturumun kararları `sunum-anlatim-plani.md` ve ilgili ADR'ye
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
