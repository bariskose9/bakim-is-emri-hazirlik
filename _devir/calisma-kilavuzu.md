# Çalışma Kılavuzu

> **Bu dosya kullanıcı içindir.** Ajanın uyacağı kurallar `CLAUDE.md` ve
> `docs/standards/` içinde; burada **projenin nasıl yürütüleceği** anlatılıyor.
>
> ⭐ **Bölüm 1 (terimler) zamanla silinebilir.** Terimler oturduğunda o bölümü
> kaldır, kılavuz kısalsın. Diğer bölümler kalıcıdır.

---

# BÖLÜM 1 — Terimler

Akış boyunca geçen kelimeler. Bilinmeyen bir terim, verilen cevabı da geçersiz
kılar.

## Doküman ve girdi terimleri

| Terim | Ne demek | Somut örnek |
|---|---|---|
| **Analiz dokümanı** | Projenin ne yapması istendiğini anlatan, **sana verilen** belge | Kurumun gönderdiği `odev.docx`, müşteri şartnamesi, ihale dosyası |
| **PRD** (Product Requirements Document) | *"Sistem tam olarak ne yapacak"* sorusunun **yazılı ve eksiksiz** hâli. Analiz dokümanından **üretilir** | Roller, iş kuralları, kapsam dışı, hata durumları |
| **API dokümanı** | Var olan bir servisin **nasıl çağrılacağını** anlatan belge: hangi adres, hangi bilgiyi ister, ne döner | *"Personel bilgisi almak için `GET /personel/{id}` çağır, şu alanlar döner"* |
| **Veritabanı şeması** | Var olan bir veritabanında **hangi tablolar, hangi kolonlar ve hangi ilişkiler** olduğunu gösteren belge | Tablo listesi, ERD diyagramı, `CREATE TABLE` betikleri |
| **ADR** (Architecture Decision Record) | Önemli bir teknik kararın **neden** alındığını yazan kısa belge | *"Repository Pattern kullanmadık, çünkü…"* |
| **Roadmap** | Yapılacak adımların **sırayla** listesi, kutucuklu | *"Adım 4 — kimlik doğrulama ✅"* |

⚠️ **Analiz dokümanı ile PRD karıştırılmaz.** Analiz **girdidir**, sana verilir
ve eksiktir. PRD **çıktıdır**, sorular sorularak üretilir ve eksiği kalmaz.

## Teknik terimler

| Terim | Ne demek |
|---|---|
| **İstemci / tüketici** | API'den veri çeken program: web arayüzü, mobil uygulama, başka kurumun sistemi |
| **Uç nokta (endpoint)** | Uygulamanın dışarıya açtığı bir adres: *"buraya şunu gönderirsen şunu yaparım"* |
| **Migration** | Veritabanının **yapısını** değiştiren, sırayla çalışan ve kayıt altına alınan komutlar |
| **Seed** | Geliştirme için üretilen sahte örnek veri |
| **Konteyner** | Uygulamayı ihtiyaçlarıyla birlikte paketleyip çalıştıran kutu (Docker) |
| **Ortam değişkeni** | Koda yazılmayan, dışarıdan verilen ayar: veritabanı adresi, şifreler |
| **CI** | Kod gönderildiğinde otomatik çalışan test ve derleme zinciri |
| **Deploy** | Kodun, kullanıcıların erişebildiği bir yerde çalışır hâle getirilmesi |
| **İzleme (monitoring)** | Sistem canlıdayken neyin yavaşladığını, neyin hata verdiğini gösteren araçlar |

## Süreç terimleri

| Terim | Ne demek |
|---|---|
| **Dal (branch)** | Ana koda dokunmadan çalışılan kopya |
| **Birleştirme isteği** (PR / MR) | *"Bu dalı ana koda alalım mı"* önerisi; inceleme burada yapılır |
| **Oturum (session)** | Ajanla yapılan bir çalışma turu. Bir roadmap adımı = bir oturum |
| **Devir notu** | Oturum kapanırken yazılan *"sırada ne var"* notu |

---

# BÖLÜM 2 — Yeni projeye başlamadan önce

## 2.1 Hazırlık

1. **Boş bir klasör** aç ve VS Code'da aç
2. **Kiti güncelle** — her yeni projede, atlanmaz:
   ```
   proje-kiti eklentisini güncelle
   ```
   Sonra `Ctrl/Cmd+Shift+P` → **Developer: Reload Window**

   ⚠️ Kite sürekli yeni kural ekleniyor. Eski sürümle başlarsan o kurallar
   projeye hiç gelmez.

3. **Elindeki belgeleri klasöre koy** — varsa:
   - Analiz dokümanı (şart)
   - Mevcut API dokümanı
   - Mevcut veritabanı şeması

   ⚠️ Bu belgeler **girdi**dir; sen yazmazsın, sana verilir. Yoksa yok — kit
   soru sorarak eksiği tamamlar.

## 2.2 Cevaplarını önceden düşün

Kurulumda şunlar sorulacak. Şimdiden düşünmek görüşmeyi hızlandırır:

| Soru | Ne cevaplayacaksın |
|---|---|
| Bu proje kimin için? | İşyeri projesi mi, kendi projen mi |
| Proje tipi? | Web · mobil · ikisi |
| API'yi senin yazmadığın biri tüketecek mi? | Başka kurum, yüklenici, merkezî sistem |
| Kendiliğinden çalışması gereken iş var mı? | Zamanlanmış görev, gece raporu |
| Kod nerede duracak, deploy'u kim yapacak? | GitHub/GitLab · sen/DevOps |

---

# BÖLÜM 3 — Kurulum: `/yeni-proje`

Tek komut:

```
/yeni-proje
```

Gerisi sohbet. Sırayla şunlar olur:

| Adım | Ne oluyor | Senden ne isteniyor |
|---|---|---|
| **0** | Eklentiler kontrol edilir, platform tespit edilir, ses bildirimi kurulur | İzin |
| **1** | Kim için · proje tipi · backend kurgusu (4 soru) · API biçimi (4 soru) · proje adı | Cevaplar |
| **2** | Kit dosyaları projeye kopyalanır | — |
| **3** | ⭐ **PRD görüşmesi** — en uzun adım | Analiz dokümanını verirsin, tek tek soru cevaplarsın |
| **4** | Yol haritası, ilk kararlar, teknoloji-ve-plan iskeleti | Onay |
| **5** | İskelet kurulur — ilk kod | Onay |
| **6** | Yayın veya teslim paketi (1. adımdaki cevaba göre) | Duruma göre |
| **7** | Son kontrol | — |

⚠️ **Bu tek komutluk bir işlem değil.** `/yeni-proje` kurulumu başlatır; Adım 3
uzun bir görüşmedir. Vaat *"tek promptla uygulama"* değil, **"doğru kurulmuş
proje ve net yol haritası"**.

⛔ **Adım 3'te acele etme.** Analiz dokümanında yazmayan onlarca karar orada
netleşir. Cevabı bilinmeyen bir kural kodlanırsa yanlış varsayım tüm katmanlara
yayılır.

---

# BÖLÜM 4 — Kurulumdan sonra: bir oturum nasıl geçer

Kurulum bitince artık kit değil, projedeki `CLAUDE.md` ve `docs/standards/`
geçerlidir. İlerleme **roadmap adımlarıyla** olur.

## Bir oturumun ritmi

```
1. Aç ve devral      → "docs/project/sonraki-adim-prompt.md oku, devam edelim"
2. Plan sun          → ajan ne yapacağını anlatır
3. Onayla            → gerekiyorsa düzelt
4. Kod + test        → ajan yazar, testler yeşil olur
5. Gözle doğrula     → ekran varsa tarayıcıda görülür
6. Commit + öneri    → değişiklik önerisi açılır
7. Kutucuk ✅        → roadmap'te o adım işaretlenir
8. Kararları yaz     → teknoloji-ve-plan.md güncellenir
9. Devir notu        → sırada ne var yazılır
10. /clear           → yeni oturuma temiz başla
```

⛔ **7 ve 8 atlanmaz.** Kutucuk *nerede kalındığını*, teknoloji belgesi *neden
öyle yapıldığını* söyler. İkisi de sonradan hatırlanmaz.

## Neden her adımda `/clear`

Konuşma uzadıkça ajanın bağlamı dolar ve ayrıntı kaybolur. Her adım kendi
oturumunda yapılır; devir notu sayesinde hiçbir şey kaybolmaz.

---

# BÖLÜM 5 — Hangi dosya ne işe yarıyor

`/yeni-proje` bittiğinde projede şunlar olur:

## Kök dizin

| Dosya | Ne işe yarıyor | Kim okur |
|---|---|---|
| `CLAUDE.md` | Ajanın uyacağı çalışma protokolü | Ajan |
| `CALISMA-KILAVUZU.md` | Bu dosya — projenin nasıl yürütüleceği | **Sen** |
| `REPO-YAPISI.md` | Hangi klasörde ne var, hangi iş nerede yapılıyor | İkisi |
| `README.md` | Projeyi kuran/çalıştıran için: kurulum, komutlar, ortam değişkenleri | Devralan geliştirici, DevOps |
| `.env.example` | Gereken ortam değişkenlerinin listesi — **değerler boş** | DevOps |
| `.env` | Gerçek değerler — ⛔ **asla commit edilmez** | Sadece sen |

## `docs/standards/` — her projede aynı

Mühendislik kuralları. **Elle değiştirilmez**; kural değişecekse `/kit-senkron`
ile kite yazılır ve oradan gelir.

| Dosya | Konu |
|---|---|
| `00-stack.md` | Hangi teknoloji kullanılıyor, hangisi kullanılmıyor, neden |
| `01-architecture.md` | Katmanlar, klasör yapısı, bağımlılık yönü |
| `02-coding-standards.md` | Kod ve yorum yazım kuralları |
| `03-api-guidelines.md` | API sözleşmesi, sürümleme, sayfalama |
| `04-database.md` | Veri modeli kuralları |
| `05-auth-security.md` | Kimlik doğrulama ve güvenlik |
| `06-testing.md` | Test stratejisi |
| `08-git-workflow.md` | Dal, commit, birleştirme kuralları |
| `09-ci-cd-deploy.md` | Otomatik kontroller ve yayın |
| `11-agent-workflow.md` | Ajanın nasıl çalışacağı |
| `12-operations-and-scaling.md` | Loglama, izleme, ölçekleme |
| `13-environments.md` | Local / test / canlı ayrımı |
| `14-privacy-and-compliance.md` | KVKK ve kişisel veri |
| `15-oturum-devri.md` | Oturum kapanış protokolü |
| *(diğerleri)* | Tanım listesi `docs/standards/` içinde |

## `docs/project/` — bu projeye özel

| Dosya | Hangi soruya cevap verir | Ne zaman dolar |
|---|---|---|
| `PRD.md` | **Sistem ne yapacak** | Kurulum Adım 3 |
| `roadmap.md` | **Ne yapılacak, hangi sırayla** — kutucuklu | Adım 4, her adımda işaretlenir |
| `teknoloji-ve-plan.md` | **Neden öyle yapıldı, teknoloji nedir** | Adım 4'te açılır, her adımda büyür |
| `decisions/` | Önemli kararların gerekçesi (ADR) | Karar alındıkça |
| `data-model.md` | Kendi veri modelin | Veri modeli adımında |
| `integrations.md` | **Dış** sistemlerle nasıl konuşuluyor | Entegrasyon eklendikçe |
| `altyapi-durumu.md` | Hangi hesap, hangi panel, hangi ayar yapıldı | Dış işlem yapıldıkça |
| `sonraki-adim-prompt.md` | **Sırada ne var** — yeni oturuma verilir | Her oturum sonunda |
| `CHANGELOG.md` | Sürüm geçmişi | Yayın yapıldıkça |

⭐ **Dört dosya dört ayrı soruya cevap verir, karıştırılmaz:**
`PRD` ne yapacak · `roadmap` hangi sırayla · `teknoloji-ve-plan` neden öyle ·
`altyapi-durumu` dışarıda ne yapıldı.

---

# BÖLÜM 6 — Hangi komut ne zaman

| Komut | Ne zaman |
|---|---|
| `/yeni-proje` | Yalnızca **boş klasörde**, projeye ilk başlarken |
| `/kit-senkron` | Projede öğrenilen bir kuralı kalıcı hâle getirirken |
| `/clear` | Her roadmap adımı bitince — bağlamı temizlemek için |
| `proje-kiti eklentisini güncelle` | Her yeni projeden önce |
| **Reload Window** | Eklenti güncellendikten sonra — yoksa eski sürüm çalışır |

---

# BÖLÜM 7 — Takıldığında

| Belirti | Muhtemel sebep | Ne yapılır |
|---|---|---|
| Ajan eski kuralla çalışıyor | Eklenti güncellendi ama pencere yenilenmedi | Reload Window |
| `/yeni-proje` beklenmedik davranıyor | Klasör boş değil | Fazla dosyaları taşı veya sil |
| Ajan cevabını bilmediğim şey soruyor | Terim açıklanmamış | *"Bu terimi açıkla"* de — kitin kuralı bunu zorunlu tutuyor |
| Ajan kararı bana bırakıyor | Mühendislik seçimini devretmiş | *"Sen karar ver, gerekçesini söyle"* de |
| Nerede kaldığımı hatırlamıyorum | Kutucuk işaretlenmemiş | `roadmap.md` ve `sonraki-adim-prompt.md`'ye bak |
| Cevaplar yüzeyselleşti | Bağlam dolmuş | `/clear` yap, devir notuyla devam et |

---

# BÖLÜM 8 — Bu kılavuz nasıl kısaltılır

Amaç bu dosyaya bağımlı kalmak değil.

- **Terimler oturduğunda** → Bölüm 1'i sil
- **Akış ezberlendiğinde** → Bölüm 3'ü kısalt, yalnızca komut kalsın
- **Dosya haritası aklında kaldığında** → Bölüm 5'i sil

Kalması gereken tek bölüm: **Bölüm 4 — oturum ritmi.** O, ezberlenmesi değil
her seferinde uygulanması gereken bir kontrol listesidir.
