---
name: yeni-proje
description: Yeni bir projeyi sıfırdan kurar — mühendislik standartlarını yerleştirir, analiz dokümanından soru sorarak PRD çıkarır, yol haritası ve ilk mimari kararı yazar, iskeleti kurar ve projeyi canlıya çıkarır. Boş bir klasörde "/yeni-proje" denince veya kullanıcı yeni bir projeye başlamak istediğini söylediğinde kullan. Türkçe çalışır.
---

# Yeni Proje Kurulumu

Kullanıcı yeni bir projeye başlıyor. Amacın: kurulumun **kullanıcının işi
olmaktan çıkması**. Kullanıcı yalnızca analiz dokümanını verir ve sorulara
cevap verir; geri kalanını sen yaparsın.

**Dil:** Kullanıcıyla **Türkçe** konuş. Kod, commit mesajı, değişken ve tablo
adları **İngilizce**.

**Kullanıcı kodu okuyup anlayamıyor olabilir.** Her adımdan sonra ne yaptığını
kod göstermeden, Türkçe, en fazla 5 maddede anlat. Sadece "ne" değil **"neden"**
de söyle. Emin olmadığın yerde "emin değilim" de — uydurma.

## Kit dosyaları nerede

Bu skill'in yanındaki `dosyalar/` klasöründe. Yerini şöyle bul:

```bash
# Önce plugin kökü, sonra kurulu sürüm önbelleği, en son marketplace kopyası
KIT="$CLAUDE_PLUGIN_ROOT/skills/yeni-proje/dosyalar"
[ -d "$KIT" ] || KIT=$(find ~/.claude/plugins/cache -type d -path '*yeni-proje/dosyalar' 2>/dev/null | sort -V | tail -1)
[ -d "$KIT" ] || KIT=$(find ~/.claude/plugins -type d -path '*yeni-proje/dosyalar' 2>/dev/null | head -1)
ls "$KIT"
```

Bulunamazsa **dur ve kullanıcıya söyle** — dosyaları ezberden yeniden yazma.

İçinde: `CLAUDE.md` · `REPO-YAPISI.md` · `docs/standards/00–17` + `sablonlar/`.

---

## Adım 0 — Bağımlılıklar

Bu kit iki dış parçaya dayanır:

| Parça | Ne için |
|---|---|
| `agent-skills@addy-agent-skills` | `interview-me`, `security-and-hardening`, `code-review-and-quality`, `test-driven-development` |
| `chrome-devtools-mcp@chrome-devtools-plugins` | Ekranları tarayıcıda **fiilen tıklayarak** doğrulama |

1. Kurulu olanları gör: `claude plugin list`
2. **Yalnızca eksik olanı** kullanıcıya bildir ve **izin iste**. Zaten kurulu
   olana dokunma — kullanıcının makinesinde başka sürüm/ayar olabilir.
3. İzin verilirse kur:
   ```bash
   claude plugin marketplace add addyosmani/agent-skills
   claude plugin install agent-skills@addy-agent-skills
   claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp
   claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
   ```
4. Kurulum sonrası bunların **aynı oturumda görünmeyebileceğini** söyle;
   görünmüyorsa kullanıcıdan Claude'u yeniden başlatmasını iste ve
   `/yeni-proje` ile devam edileceğini belirt.
5. Kullanıcı istemezse **kurma**. O parçaya bağlı adımlarda ne kaybedildiğini
   (örn. tarayıcı doğrulaması yapılamaz) açıkça söyle ve devam et.

**Sormadan kurma.** Kullanıcının makinesine izinsiz paket eklemek bu kitin
kendi kuralının ihlalidir.

### Adım 0b — Sesli bildirim (macOS)

Kullanıcı ekrana sürekli bakmıyor; Claude'un ne zaman beklediği duyulmalı.
Detaylı gerekçe ve doğrulama komutları: kit kökündeki `KURULUM.md`.

1. **Zaten kurulu mu, ÖNCE bak:**
   ```bash
   jq -e '.hooks.Stop' ~/.claude/settings.json
   ```
   Çıktı varsa **hiçbir şey yapma**, sadece "sesli bildirim zaten kurulu" de.

2. **Platformu tespit et** — komutlar tamamen farklı:

   **macOS** — Türkçe sesin varlığını doğrula:
   ```bash
   say -v '?' | grep tr_TR      # "Yelda" görünmeli
   ```
   Yoksa `say -v Yelda` yerine varsayılan sesi kullan, kullanıcıya söyle.

   **Windows** — kurulu sesleri listele (`41f` = Türkçe):
   ```powershell
   $v = New-Object -ComObject SAPI.SpVoice
   $v.GetVoices() | ForEach-Object { "$($_.GetDescription()) -> $($_.GetAttribute('Language'))" }
   ```
   Türkçe yoksa varsayılan sesle okunur (İngilizce aksanlı ama anlaşılır);
   kullanıcıya *Ayarlar → Saat ve Dil → Konuşma → Ses ekle → Türkçe* de.

3. **İzin iste**, sonra `~/.claude/settings.json` içine ekle (mevcut ayarları
   **koruyarak** birleştir, üzerine yazma). Yalnızca kendi platformunun
   bloğunu yaz — ikisini birden ekleme:

   **macOS:**
   ```json
   {
     "hooks": {
       "Notification": [
         { "hooks": [{ "type": "command", "async": true,
           "command": "afplay /System/Library/Sounds/Funk.aiff; say -v Yelda 'Sorum var' 2>/dev/null || true" }] }
       ],
       "Stop": [
         { "hooks": [{ "type": "command", "async": true,
           "command": "afplay /System/Library/Sounds/Glass.aiff; say -v Yelda 'Senin sıran' 2>/dev/null || true" }] }
       ]
     }
   }
   ```

   **Windows** (`"shell": "powershell"` ŞART — yoksa komut bash'te çalışır ve patlar):
   ```json
   {
     "hooks": {
       "Notification": [
         { "hooks": [{ "type": "command", "shell": "powershell", "async": true,
           "command": "[console]::beep(880,250); $v=New-Object -ComObject SAPI.SpVoice; $t=@($v.GetVoices() | Where-Object { $_.GetAttribute('Language') -eq '41f' })[0]; if ($t) { $v.Voice = $t }; $v.Speak('Sorum var') | Out-Null" }] }
       ],
       "Stop": [
         { "hooks": [{ "type": "command", "shell": "powershell", "async": true,
           "command": "[console]::beep(1320,200); $v=New-Object -ComObject SAPI.SpVoice; $t=@($v.GetVoices() | Where-Object { $_.GetAttribute('Language') -eq '41f' })[0]; if ($t) { $v.Voice = $t }; $v.Speak('Senin sıran') | Out-Null" }] }
       ]
     }
   }
   ```

   Windows'ta `SAPI.SpVoice` (COM) ve `[console]::beep` seçildi çünkü
   `System.Speech` ve `System.Media.SystemSounds` PowerShell 7'de ayrı paket
   istiyor; COM ve `beep` her sürümde hazır.

4. **Yazdıktan sonra doğrula** — yazdım demek yetmez. JSON'un geçerli
   olduğunu kontrol et **ve sesi fiilen çal**:
   ```bash
   # macOS
   jq -e '.hooks | to_entries[] | "\(.key): \(.value[0].hooks[0].command)"' ~/.claude/settings.json
   afplay /System/Library/Sounds/Glass.aiff; say -v Yelda 'Senin sıran'
   ```
   ```powershell
   # Windows
   Get-Content "$env:USERPROFILE\.claude\settings.json" | ConvertFrom-Json | Select-Object -ExpandProperty hooks
   [console]::beep(1320,200); (New-Object -ComObject SAPI.SpVoice).Speak('Senin sıran') | Out-Null
   ```
   Kullanıcıya "sesi duydun mu" diye **sor**; duymadıysa çıkış aygıtına
   baktır (sanal ses aygıtı seçiliyken ses sessiz kalabiliyor).

⚠️ **PROJE ayarına (`.claude/settings.json`) KOYMA.** Hook'lar birden fazla
ayar dosyasında tanımlıysa **hepsi birden çalışır** ve ses iki kez duyulur.
Global ayar zaten tüm projeleri kapsıyor.

⚠️ **Windows bloğu henüz gerçek bir Windows makinesinde doğrulanmadı** (kit
macOS'te geliştirildi, orada PowerShell yok). Microsoft'un güncel API
dokümanına bakılarak yazıldı. İlk Windows kullanımında çalışmazsa hata
mesajını `KURULUM.md`'ye işle.

⚠️ Platform bu ikisinden biri değilse (Linux) adımı **atla** ve sebebini söyle.

## Adım 1 — Proje tipi ve stack

Tek tek sor, varsayım yapma:

1. **Bu proje kimin için?** → `kendi projem` · `kurum projesi`
   Bu ilk soru, çünkü sonraki her şeyi belirliyor (Adım 6 tamamen buna bakar).
2. **Proje tipi:** web · mobil (Expo) · ikisi (ortak API)
3. **Backend kurgusu:** aşağıdaki dört soruyu **sen sor, sen karara bağla**
4. **Proje adı** ve **hedef kullanıcı kitlesi** (bir cümle)

**Varsayılan stack:** Next.js App Router + TypeScript (strict) + Tailwind +
shadcn/ui + Prisma + PostgreSQL + Zod · mobil varsa Expo, aynı REST API.
Kullanıcı farklı bir şey söylerse (başka sunucu, başka veritabanı, başka
hosting) **onu kullan**, tartışma.

### 1a — Kim için: kendi projem mi, kurum projesi mi

| | **Kendi projem** | **Kurum projesi** |
|---|---|---|
| Kod nerede | GitHub | Kurumun GitLab'ı |
| CI dosyası | `.github/workflows/ci.yml` | `.gitlab-ci.yml` |
| İnceleme | Pull Request | Merge Request |
| Alan adı · sunucu · SSL | **Kullanıcı** alır, sen yönlendirirsin | Kurumun zaten var |
| Deploy | **Sen** yaparsın | **DevOps** yapar — sen hiç deploy etmezsin |
| Teslim | Çalışan canlı site + link | **Çalıştırılabilir paket** (Adım 6b) |
| Docker | Local + kendi sunucu senaryosunda | **Her zaman zorunlu** — DevOps'la sözleşme |
| Ortam değişkenleri | Kullanıcı girer | `.env.example` ile **belgelenir**, DevOps girer |
| İzleme | Kurulur | Kurumun sistemi — sen **JSON log** üretirsin |

⛔ Cevap `kurum projesi` ise **alan adı, sunucu kiralama, DNS, SSL adımlarını
hiç açma.** O işler DevOps'a ait; kullanıcıya gereksiz iş yüklemek olur.

### 1b — Backend kurgusu: Next tek başına mı, Next + Nest mi

Dört soru. **Hepsi "hayır" ise Next tek başına. En az biri "evet" ise
Next (arayüz) + NestJS (API + worker).**

1. API'yi kendi web arayüzünden **başkası** tüketecek mi? (mobil, başka sistem)
2. Kullanıcı istek atmasa da **kendiliğinden** çalışması gereken iş var mı?
   (zamanlanmış görev, kuyruk, webhook karşılama)
3. Katmanlı mimari + **DI yaşam döngüsü** (singleton/scoped) + çok modüllü yapı
   gerekiyor mu?
4. Kod kurumun **kendi sunucusunda** mı çalışacak (sunucusuz platform yok)?

**Neden bu kural:** Next.js Route Handler ile API yazılabilir ama üç şeyi
veremez — sürekli çalışan arka plan süreci, DI konteyneri ve yaşam döngüleri,
zorlanan katman sınırları. Bunlara ihtiyaç yoksa ikinci bir sunucu **saf
maliyettir**: iki deploy, CORS, kimlik doğrulamanın iki tarafta kurgulanması,
tiplerin paylaşılması, yerel geliştirmede dört süreç.

⛔ **Express'i çıplak seçme.** NestJS zaten Express'in üstünde çalışır; Nest'i
seçince Express'i almış olursun. Çıplak Express yalnızca tek amaçlı, 5–10 uçlu
mikro servislerde (webhook alıcı, proxy) tercih edilir.

**Ayrı backend seçildiyse kararlar:**

| Konu | Seçim | Gerekçe |
|---|---|---|
| HTTP adaptörü | **Express** (Nest varsayılanı) | Darboğaz veritabanıdır, HTTP katmanı değil. Fastify'ın kazancı bu senaryoda ölçülemez; adaptör tek satırla değiştirilebilir |
| API biçimi | **REST** | HTTP önbelleği çalışır, uç bazında izlenir, DevOps tanır. GraphQL yalnızca *kontrol etmediğin* çok sayıda istemci varsa |
| Sürümleme | `/api/v1/...` **baştan** | Kural ve gerekçesi `03-api-guidelines.md` → "Sözleşme ömrü"nde, burada tekrarlanmaz. Mobil varsa **zorunlu**: uygulama kullanıcının telefonunda eski sürümde kalır |
| Tip paylaşımı | Monorepo + `packages/contracts` | Zod şeması tek yerde; API alan adı değişince frontend **derlenmez** — hata çalışma anına kalmaz |

Mobil seçilirse `05-auth-security.md` ve `17-mobile.md` birlikte okunur:
oturum kararı **baştan** hem çerezi hem jetonu kapsayacak şekilde alınır.
Sonradan eklemek kimlik doğrulamayı baştan yazdırır.

## Adım 2 — Kit dosyalarını yerleştir

1. `dosyalar/` içeriğini projeye kopyala: `CLAUDE.md`, `REPO-YAPISI.md`,
   `docs/standards/**`
2. `CLAUDE.md` §0 bloğunu Adım 1'deki cevaplarla **doldur**
3. `docs/standards/sablonlar/` içindeki şablonları `docs/project/` altına aç:
   `PRD.md` · `roadmap.md` · `altyapi-durumu.md` · `CHANGELOG.md` ·
   `sonraki-adim-prompt.md` · `decisions/ADR-000-sablon.md` ·
   (veritabanı varsa) `data-model.md` · (dış servis varsa) `integrations.md` ·
   (sahte veri gerekiyorsa) `fake-data-guide.md`
4. **`docs/standards/` içini değiştirme.** Tek istisna `00-stack.md` sürüm
   tablosu — o da Adım 5'te fiilen kurulanla eşitlenir.

## Adım 3 — PRD (en kritik adım)

Kullanıcının analiz dokümanını iste. **Her zaman eksiktir.**

- `interview-me` skill'ini kullan. **Tek tek** sor, birden fazla soruyu aynı
  anda yığma. Cevap belirsizse netleşene kadar üzerine git.
- En az şunlar netleşmeden geçme: kim kullanacak · hangi problemi çözüyor ·
  **kapsam dışı ne** · roller ve her rolün yapamadığı · her modülün iş kuralları
  (sayı vererek) · hata durumunda ne olacağı
- Cevapları `docs/project/PRD.md` şablonuna yaz.
- **"§9 Açık sorular" bölümü boşalmadan kod yazma.** Cevabını bilmediğin şeyi
  kendin doldurma — yanlış varsayım en pahalı hatadır.
- ⛔ **KULLANICI HESABI VARSA `§5.x Hesap yönetimi ve veri hakları` DOLDURULUR
  ve kapsam dışı bırakılamaz** (KVKK m.11). Sor: verimi indir dosyasına ne
  girecek · hesap silinince ne SİLİNİYOR, ne hangi KANUN gereği ne kadar
  SAKLANIYOR. Kurallar `14-privacy-and-compliance.md` → "Hesap silme"
  bölümünde; **oradaki "anonimleştirme demeyin" uyarısını atlamayın.**

## Adım 4 — Yol haritası ve ilk kararlar

1. `roadmap.md`: adımlar **bağımlılık sırasına** göre. İlk üç adım hemen her
   projede aynıdır — repo+iskelet, hosting+veritabanı+CI+sağlık ucu, veri
   modeli+tohumlama. Mobil varsa **son adım** olarak yer alır.
2. `decisions/ADR-001-*.md`: genellikle "neden bu stack / neden tek repo".
   `ADR-000-sablon.md` biçimini kullan.
3. `altyapi-durumu.md`: **boş bile olsa şimdi aç.** İlk hesap açıldığı anda
   yazılmaya başlar; sonradan hatırlamak işe yaramaz.
4. `sonraki-adim-prompt.md`: şu notla oluştur —
   *"Henüz doldurulmadı. İlk roadmap adımı bitince `15-oturum-devri.md`
   protokolüne göre baştan yazılacak."*

## Adım 5 — İskeleti kur

1. Framework'ü kur, TypeScript strict, lint + format yapılandır
2. `git init`, `.gitignore`, `.env.example` (⛔ `.env` **asla** commit edilmez)
2b. `dosyalar/.claude/settings.json`'ı projeye kopyala — **salt okunur** komutlar
   için hazır izin listesi. Amacı: kullanıcının aynı güvenli komuta defalarca
   "izin ver" tıklamaması. **Script adlarını `package.json`'daki gerçek
   adlarla eşitle**; olmayan script'i listede bırakma.
   ⛔ Bu listeye **durum değiştiren** hiçbir şey eklenmez: `build`, `e2e`,
   veritabanına yazan test, `git push`, `docker compose`, ve **hiçbir koşulda**
   `npx`/`node`/`python` gibi keyfi kod çalıştıran joker kalıp.
   Kullanıcıya modu da söyle: **`Auto`** (Shift+Tab) güvenli işleri geçer,
   riskli olanda durur — `Bypass permissions` önerilmez.
3. `00-stack.md` sürüm tablosunu **fiilen kurulanla eşitle**. En yenisi
   kullanılmıyorsa **nedenini yaz**
4. `REPO-YAPISI.md`'yi gerçek klasör yapısına göre doldur
5. İlk commit — `08-git-workflow.md` biçimiyle

## Adım 6 — Yayın: Adım 1a'daki cevaba göre İKİYE ayrılır

⛔ **Önce Adım 1a'ya bak.** Yanlış kolu çalıştırmak, kurum projesinde
kullanıcıya gereksiz hesap/domain işi yükler; kendi projesinde ise projeyi
yayınsız bırakır.

### Adım 6a — Kendi projem: canlıya çıkar

1. GitHub deposu aç (`gh`), dalı push et
2. Hosting + veritabanı bağla. **Hesap gerektiren her adımda kullanıcıya ne
   yapacağını adım adım söyle ve bekle** — onun yerine hesap açamazsın
3. CI kur: `lint → typecheck → test → build`
4. `GET /api/health` (uygulama + veritabanı) ekle ve canlıda çalıştığını göster
5. **Yapılan her dış işlemi anında `altyapi-durumu.md`'ye yaz** — hangi hesap,
   panelde ne seçildi, hangi değişken hangi ortamda. ⛔ Anahtar **değeri** yazılmaz
6. Duman testi: anasayfa açılıyor mu, `/api/health` yeşil mi
7. Kendi sunucusuna çıkılıyorsa (Vercel değil): sunucu kirala → Docker Engine
   kur → `docker compose up -d` → domain'i DNS'te IP'ye yönlendir → reverse
   proxy (Caddy) ile HTTPS. Her adımda kullanıcıyı yönlendir, bekle.

### Adım 6b — Kurum projesi: teslim paketini hazırla ve DOĞRULA

Burada **deploy etmiyorsun.** Ürettiğin şey, DevOps'un çalıştıracağı pakettir.
⛔ Domain, sunucu, DNS, SSL adımlarını **hiç açma**.

1. Kurumun GitLab'ına push et; `.gitlab-ci.yml` kur (adımlar `package.json`
   script'inde, CI dosyası ince sarmalayıcı olsun — platform değişirse taşınsın)
2. **Teslim paketini üret:**
   - `Dockerfile` — çok aşamalı (multi-stage), üretim imajı küçük
   - `docker-compose.yml` — tüm servisler, portlar, başlangıç sırası, health check
   - `.env.example` — **eksiksiz**; DevOps hiçbir değişkeni tahmin etmemeli
   - `README.md` — kurulum, çalıştırma, migration, test, bilinen eksikler
   - Migration stratejisi — şema değişikliği ne zaman hangi komutla uygulanır
   - `GET /health/live` + `GET /health/ready` (ready veritabanını da yoklar)
3. **Paketi kendi makinende DOĞRULA** — "yazdım" yetmez:
   ```bash
   docker compose up --build        # sıfırdan, tek komutta ayağa kalkmalı
   curl localhost:<API_PORT>/health/ready
   ```
   `chrome-devtools` MCP ile ekranları fiilen tıkla; konsol hatası kalmasın.
4. Log biçimi **JSON** olmalı — kurumun toplama sistemi düz metni toplayamaz
5. Kullanıcıya DevOps'a söyleyeceği cümleyi **hazır ver**, örnek:
   > "Uygulama GitLab'da `main` dalında. `docker compose up --build` ile ayağa
   > kalkıyor. Gereken değişkenler `.env.example` içinde listeli, değerleri
   > sizde. Migration'lar açılışta `prisma migrate deploy` ile çalışıyor.
   > Sağlık uçları `/health/live` ve `/health/ready`."

## Adım 7 — Son kontrol

Bitirmeden önce kendine sor ve **eksik varsa kullanıcıya sor**:

- [ ] `CLAUDE.md` §0 dolu mu
- [ ] PRD'de açık soru kaldı mı
- [ ] Her şablon dolduruldu mu (boş şablon bırakmak hiç açmamaktan kötüdür)
- [ ] `altyapi-durumu.md` bu oturumda yapılan **her** dış işlemi içeriyor mu
- [ ] `00-stack.md` sürümleri `package.json` ile birebir aynı mı
- [ ] **6a ise:** canlı adres ve `/api/health` çalışıyor mu
- [ ] **6b ise:** `docker compose up --build` temiz makinede ayağa kalkıyor mu,
      `.env.example` eksiksiz mi, sağlık uçları yeşil mi
- [ ] `.env` commit edilmemiş, `.env.example` commit edilmiş mi
- [ ] `sonraki-adim-prompt.md` bir sonraki adımı tarif ediyor mu

Sonra kullanıcıya **Türkçe, en fazla 5 maddede** özet ver: ne kuruldu, canlı
adres, sıradaki roadmap adımı, senden beklenen (varsa hesap/ayar).

---

## Sınırlar — kullanıcıya açıkça söyle

- **Bu skill özellikleri yazmaz.** Kurulumu bitirir ve yol haritasını çıkarır.
  Sonrasında roadmap adım adım ilerler ve **her adımda plan sunulup onay
  beklenir** (`CLAUDE.md` §3 kapıları). Vaat "tek promptla uygulama" değil,
  **"tek promptla doğru kurulmuş proje ve net yol haritası"**.
- Kurulum bittikten sonra artık bu skill değil, projedeki `CLAUDE.md` ve
  `docs/standards/` geçerlidir.
- Bir kural ile kullanıcının isteği çakışırsa **dur ve sor**. Kendi başına karar verme.
- Bir şeyi bozduğunu fark edersen saklama, hemen söyle.

## İş bölümü — yapabildiğini kullanıcıya yaptırma

Bu kural `11-agent-workflow.md` → *"Mühendislik seçimi kullanıcıya devredilmez"*
kuralının **ikizidir**, aynısı değil:

| | Konu | Kural |
|---|---|---|
| `11-agent-workflow.md` | **Kararı kim verir** | Mühendislik tercihini ajan verir, kullanıcıya menü sunmaz |
| Buradaki kural | **İşi kim yapar** | Ajanın elinden gelen işi kullanıcıya yaptırmaz |

⛔ **Kendi yapabileceğin hiçbir işi kullanıcıdan isteme.** Kullanıcının zamanı
yalnızca senin *yapamadığın* işler için harcanır. Bu kit `Auto` (edit
automatically) modunda kullanılacak şekilde yazılmıştır: güvenli işleri sorma,
yap ve **ne yaptığını tek cümleyle söyle**.

**Sen yaparsın** (sormadan): dosya yazma/düzenleme, bağımlılık kurma, migration
üretme, test yazma ve koşturma, lint/format, commit, `docker compose` ile local
ayağa kaldırma, **`chrome-devtools` MCP ile ekranları açıp tıklayarak
doğrulama**, konsol ve ağ hatalarını okuma, ekran görüntüsü alma.

**Onay iste** (yap ama önce tek cümlelik izin): `git push`, uzak depo açma,
üretim veritabanına dokunan işlem, para/hesap gerektiren adım, geri alınması
zor silme.

**Sadece bunları kullanıcıdan iste** (fiilen yapamadıkların): hesap açma ve
giriş (GitHub/GitLab/hosting/domain sağlayıcı), ödeme, kurumun panelinden
yetki/erişim alma, iki faktörlü doğrulama kodu, fiziksel/kurumsal onay,
`/plugin` gibi interaktif Claude Code ekranları.

Bir adımı kullanıcıya devrediyorsan **neden devrettiğini söyle** ("hesap
açmak için kimlik doğrulaman gerekiyor, ben giremiyorum"). Gerekçesiz
devretme, kullanıcıya "bunu neden ben yapıyorum" dedirtir.
