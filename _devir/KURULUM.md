# Kurulum — Windows makinesinde ne yapacağım

> **Bu dosya senin için.** Ajanın okuması gereken dosya `YENI-OTURUM.md`.
>
> İki ayrı durum var, karıştırma:
>
> | | Ne zaman | Nereye git |
> |---|---|---|
> | **A** | Windows makinesini **ilk kez** kuruyorsun | Aşağıdan başla |
> | **B** | Programlar kurulu, **yeni bir proje** açıyorsun | BÖLÜM B |

---

# BÖLÜM A — İlk kurulum (bir kez yapılır)

## A1. Programları kur

Sırayla indir ve kur:

| # | Program | Adres | Not |
|---|---|---|---|
| 1 | **Git** | git-scm.com | |
| 2 | **Node.js 24 LTS** | nodejs.org | ⚠️ LTS sürümü seç, "Current" değil |
| 3 | **Docker Desktop** | docker.com | ⚠️ Kurulumda **"Use WSL 2"** işaretli olmalı |
| 4 | **VS Code** | code.visualstudio.com | |
| 5 | **GitHub CLI** | cli.github.com | |

Sonra PowerShell aç:

```powershell
npm install -g pnpm
```

**Kontrol** — beşi de sürüm numarası vermeli:

```powershell
git --version ; node --version ; pnpm --version ; docker --version ; gh --version
```

## A2. Git'i tanıt

```powershell
git config --global user.name "Barış Köse"
git config --global user.email "kilicarslan45@gmail.com"
git config --global core.autocrlf input
```

⛔ **Üçüncü satır atlanmaz.** Windows satır sonlarını `CRLF` yapar; Linux
konteynerindeki betikler bunu okuyamaz ve `\r` hatası verir.

## A3. GitHub'a giriş yap

```powershell
gh auth login
```

| Soru | Cevap |
|---|---|
| *What account?* | **GitHub.com** |
| *Preferred protocol?* | **HTTPS** |
| *Authenticate Git?* | **Yes** |
| *How to login?* | **Login with a web browser** |

Ekranda kod çıkar, tarayıcı açılır, kodu yapıştırırsın.

⚠️ Burada **kişisel GitHub hesabın** kullanılıyor (`bariskose9`). Belediyenin
hesabı değil — ayrımı A5'te.

## A4. Projeyi indir

```powershell
cd C:\Users\<kullanıcı-adın>\Documents
gh repo clone bariskose9/bakim-is-emri-hazirlik
cd bakim-is-emri-hazirlik
code .
```

## A5. Claude Code eklentisini kur

VS Code içinde:

1. Sol kenarda **Extensions** ikonu (dört kutu)
2. Arama kutusuna **Claude Code**
3. Anthropic'inkini **Install**
4. Giriş isteyince ⚠️ **belediyenin verdiği hesapla** gir

## A6. Eklentileri (plugin) kur

Claude Code açıldıktan sonra sohbete yapıştır:

```
claude plugin marketplace add bariskose9/bariskose-skills
claude plugin marketplace add addyosmani/agent-skills
claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp

claude plugin install proje-kiti@bariskose-skills
claude plugin install agent-skills@addy-agent-skills
claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

⭐ **Kit deposu herkese açık** — kurumsal makinede kişisel GitHub hesabı
bağlaman gerekmez, kimlik doğrulaması istemez.

`/plugin` ekranından kuracaksan aranacak adlar:

| Marketplace | Plugin |
|---|---|
| `bariskose-skills` | `proje-kiti` |
| `addy-agent-skills` | `agent-skills` |
| `chrome-devtools-plugins` | `chrome-devtools-mcp` |

## A7. Pencereyi yenile

⛔ **Bu adım atlanamaz.** Eklenti kurulsa da **çalışan sürüm hâlâ eskisidir.**

**Fareyle:** Üst menü **View** → **Command Palette…** → kutuya `Reload Window`
yaz → **Developer: Reload Window**

**Klavyeyle:** `Ctrl+Shift+P` → aynı kutu

**Kontrol:**

```
claude plugin list
```

Üçü de listede görünmeli.

---

# BÖLÜM B — Bu projeye devam et

Kurulum bitti. Claude Code'a şunu yapıştır:

```
_devir/YENI-OTURUM.md dosyasını oku ve içindeki talimatları uygula.
```

⭐ **Bu tek satır yeterli.** `YENI-OTURUM.md` içinde şunlar yazılı: ajanın rolü,
hangi dosyayı hangi sırayla okuyacağı, hangi kararların verilmiş olduğu, sana
neyi sorup neyi sormayacağı, ve devralınan açık işler.

## `_devir` klasöründe ne var

| Dosya | Kim okur |
|---|---|
| **`YENI-OTURUM.md`** | ⭐ **Ajan** — devir notu |
| **`odev.md`** | İkisi — ödevin okunabilir hâli |
| `odev.docx` | Arşiv — ⚠️ VS Code gösteremez (ikili dosya), `odev.md`'yi kullan |
| **`proje-teknoloji-ve-plan.md`** | İkisi — tüm kararlar, kavramlar, yapım planı |
| **`KURUMDAN-OGRENECEKLERIM.md`** | **Sen** — kuruma soracakların |
| `sunum-anlatim-plani.md` | Sen — sunum sırası |
| `calisma-kilavuzu.md` | Sen — kitle nasıl çalışılır (her projede aynı) |
| `KURULUM.md` | Sen — bu dosya |

## Kod yazmaya geçerken

Rehber incelemesi bitip `/yeni-proje` çalıştıracağın zaman:

```
_devir klasöründen ŞUNLARI SİL (işlevi bitti):
  KURULUM.md

_devir klasöründen ŞUNLARI KORU:
  YENI-OTURUM.md · odev.md · odev.docx · KURUMDAN-OGRENECEKLERIM.md
  proje-teknoloji-ve-plan.md · sunum-anlatim-plani.md · calisma-kilavuzu.md

/yeni-proje bitince korunanları yerleştir:
  odev.md, odev.docx           → docs/project/
  proje-teknoloji-ve-plan.md   → docs/project/
  sunum-anlatim-plani.md       → docs/sunum/
  KURUMDAN-OGRENECEKLERIM.md   → cevaplar PRD'ye işlenince silinir
  YENI-OTURUM.md               → kurulum bitince silinir
```

⛔ **"`_devir` klasörünü sil" deme.** İçinde ~200 sayfalık teknoloji rehberi ve
ödevin kendisi var. Yalnızca yukarıda **SİL** yazan dosyalar silinir.

---

# BÖLÜM C — Sonraki projelerde (belediyede yeni iş)

Programlar ve eklentiler zaten kurulu. Yapılacaklar kısa:

## C1. Boş klasör aç

```powershell
cd C:\Users\<kullanıcı-adın>\Documents
mkdir yeni-proje-adi
cd yeni-proje-adi
code .
```

## C2. Kiti güncelle

```
claude plugin update proje-kiti@bariskose-skills
```

Sonra **Reload Window** (A7).

⚠️ **Her yeni projede yap.** Kite sürekli yeni kural ekleniyor; eski sürümle
başlarsan o kuralları kaybedersin.

## C3. Kurulumu başlat

```
/yeni-proje
```

Sana sırayla soracak:

| # | Soru | Bu projelerde cevap |
|---|---|---|
| 1 | Bu proje kimin için? | **işyeri projesi** — GitLab, deploy DevOps'ta, sana domain/sunucu işi verilmez |
| 2 | Proje tipi | web / mobil / ikisi |
| 3 | Backend kurgusu | Dört soru sorup **kendisi** karar verecek |
| 4 | Analiz dokümanı | Kurumun verdiği dosyayı ver |
| 5 | PRD görüşmesi | Sorulara cevap verirsin |

## C4. Gizli bilgi kuralı

Kurum bilgisi (sunucu adresi, iç ağ, gerçek veri) çıkarsa:

- ⛔ **Kite yazılmaz** — kit deposu herkese açık
- Projenin kendi deposuna yazılır
- Değerler `.env`'e; `.env.example`'a yalnızca **isimler**

Bu kural zaten kitte yazılı, ajan uyacak. Sen sadece bil.

---

# Sık karşılaşılan sorunlar

| Belirti | Çözüm |
|---|---|
| `docker` bulunamıyor | Docker Desktop'ı başlat — sistem tepsisindeki balina **yeşil** olmalı |
| `pnpm` bulunamıyor | PowerShell'i kapat-aç |
| Klonlama *"repository not found"* | `gh auth status` — yanlış hesapla girilmiş olabilir |
| Betikler `\r` hatası veriyor | `git config --global core.autocrlf input` yap, sonra **yeniden klonla** |
| Plugin'ler görünmüyor | **Reload Window** (A7) |
| Ajan eski kuralla çalışıyor | Plugin'i güncelle **ve** Reload Window — ikisi birden gerekli |
| `/yeni-proje` beklenmedik davranıyor | Klasör boş değil — fazla dosyaları taşı |

---

# ⚠️ Üç hesabı karıştırma

En sık yapılan hata bu:

| Nerede | Hangi hesap | Ne için |
|---|---|---|
| `gh auth login` (PowerShell) | **Kişisel GitHub** (`bariskose9`) | Hazırlık deposunu indirmek |
| VS Code → Claude Code eklentisi | **Belediyenin hesabı** | Claude'u kullanmak |
| İleride kurumun GitLab'ı | **Kurumun hesabı** | Proje kodunu teslim etmek |

Üçü ayrı ve üçü de gerekli.

---

# Windows'a özgü notlar

| Konu | Durum |
|---|---|
| **Kit yolu** | macOS'teki `~/.claude/...` → `C:\Users\<kullanıcı>\.claude\...` |
| **Kabuk** | PowerShell — komut ayracı `&&` değil `;`, yollar `\` |
| ⛔ **Satır sonu** | `core.autocrlf input` (A2) ve `.gitattributes` içinde `* text=auto eol=lf` |
| **Portlar** | Bu makinede paralel proje **yok** → `.env` boş bırakılabilir, varsayılanlar (3000/4000/5432/6379) serbest |
| ⚠️ **Sesli bildirim** | Kitin Adım 0b PowerShell bloğu **yalnızca macOS'te test edildi.** Windows'ta ilk kez çalışacak; çalışmazsa hata mesajı kite işlenmeli |

---

# PDF sürümleri

Belgeler **karanlık** PDF olarak üretiliyor — telefonda okuma için.

⚠️ **Karanlık sürüm yazdırılmaz** — sayfayı simsiyah basar, kartuşu bitirir.
Yazdırman gerekirse söyle, aydınlık sürüm üretilir.

ℹ️ OLED ekranlarda (çoğu modern telefon) siyah piksel kapandığı için karanlık
sürüm gerçekten pil tasarrufu sağlar; LCD'de fark olmaz.
