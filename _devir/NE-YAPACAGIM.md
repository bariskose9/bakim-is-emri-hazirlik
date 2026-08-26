# Ne yapacağım — adım adım

İki ayrı durum var. Karıştırma:

- **A) YARIN:** Windows makinesini ilk kez kuracaksın ve bu projeye devam edeceksin
- **B) BUNDAN SONRA:** Belediyede yeni bir proje açacaksın

---

# A) YARIN — Windows makinesinde ilk kurulum

Bu bölüm **bir kez** yapılır. Sonraki günler bu adımlara dönmezsin.

## A1. Programları kur

Sırayla indir ve kur:

1. **Git** → git-scm.com
2. **Node.js 24 LTS** → nodejs.org
3. **Docker Desktop** → docker.com
   → Kurulumda **"Use WSL 2"** seçeneği işaretli olmalı
4. **VS Code** → code.visualstudio.com
5. **GitHub CLI** → cli.github.com

Sonra PowerShell aç ve şunu yaz:

```powershell
npm install -g pnpm
```

**Kontrol:** Hepsinin kurulduğunu doğrula.

```powershell
git --version
node --version
pnpm --version
docker --version
gh --version
```

Beşi de sürüm numarası vermeli. Vermeyen varsa o programı yeniden kur.

## A2. Git'i tanıt

PowerShell'de sırayla:

```powershell
git config --global user.name "Barış Köse"
git config --global user.email "kilicarslan45@gmail.com"
git config --global core.autocrlf input
```

Üçüncü satır atlanmamalı — atlanırsa Docker içindeki dosyalar bozulur.

## A3. GitHub'a giriş yap

```powershell
gh auth login
```

Sorular ve cevapları:
- *What account?* → **GitHub.com**
- *Preferred protocol?* → **HTTPS**
- *Authenticate Git?* → **Yes**
- *How to login?* → **Login with a web browser**

Ekranda bir kod çıkar, tarayıcı açılır, kodu yapıştırırsın.

⚠️ Buraya **kendi kişisel GitHub hesabınla** giriyorsun (bariskose9).
Belediyenin hesabı değil.

## A4. Projeyi indir

```powershell
cd C:\Users\<kullanıcı-adın>\Documents
gh repo clone bariskose9/bakim-is-emri-hazirlik
cd bakim-is-emri-hazirlik
code .
```


## A5. Claude Code eklentisini kur

VS Code içinde:

1. Sol kenarda **Extensions** ikonuna tıkla (dört kutu şeklinde)
2. Arama kutusuna **Claude Code** yaz
3. Anthropic'in eklentisini **Install**
4. Giriş isteyince **belediyenin verdiği hesapla** gir

⚠️ Burada belediyenin hesabı kullanılıyor — GitHub'daki kişisel hesabınla
karıştırma. İkisi ayrı şeyler ve ikisi de gerekli.

## A6. Eklentileri (plugin) kur

Claude Code açıldıktan sonra, sohbete şunu yapıştır:

```
Şu üç marketplace'i ekle ve üç plugin'i kur:

claude plugin marketplace add bariskose9/bariskose-skills
claude plugin marketplace add addyosmani/agent-skills
claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp

claude plugin install proje-kiti@bariskose-skills
claude plugin install agent-skills@addy-agent-skills
claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

## A7. Yeniden başlat

`Ctrl+Shift+P` → **Developer: Reload Window** → Enter

**Kontrol:**

```
claude plugin list
```

Üçü de listede görünmeli. Görünmüyorsa A7'yi tekrarla.

## A8. Çalışmaya başla

Şu mesajı yapıştır:

```
_devir klasöründeki dosyaları oku, kaldığımız yerden devam ediyoruz:
_devir/proje-teknoloji-ve-plan.md
_devir/sunum-anlatim-plani.md

İzmir Büyükşehir'in teknik değerlendirme ödevi (_devir/odev.docx) için
hazırlanan rehberi bölüm bölüm inceleyip düzeltme göndereceğim.

YAZIM KURALLARI:
- Her kavram üç adımda açılır: gerçek hayat örneği → yazılımdaki tanımı →
  BU projede tam olarak nerede. Üçüncüsü atlanamaz.
- Kod görülmeden anlaşılmayacak her başlıkta 5-15 satır örnek olur.
  "Yanlış hâli → neden yanlış → doğru hâli" kalıbı tercih edilir.
  Kodun içine açıklama yorumu yazılmaz.
- Doküman, sahibinin bilgi seviyesini ele vermez. Hedef kitle: mülakatı
  yapacak orta seviye yazılımcılar.
- Verdiğim metinlerdeki örnekleri ve terimleri koru, yalnızca biçimi düzelt.
  Teknik hata görürsen sessizce düzeltme, önce bana söyle.
```

Artık kaldığın yerden devam ediyorsun.

## A9. Kod yazmaya geçerken (rehber incelemesi bitince)

```

_devir klasöründen ŞUNLARI SİL (işlevi bitti):
  NE-YAPACAGIM.md, WINDOWS-ADIMLARI.md, OKU-ONCE.md, WHATSAPP-NOTU.txt,

_devir klasöründen ŞUNLARI KORU, geçici bir yere taşı:
  YENI-OTURUM.md, odev.md, odev.docx, KURUMDAN-OGRENECEKLERIM.md,
  proje-teknoloji-ve-plan.md, sunum-anlatim-plani.md

Sonra /yeni-proje çalıştır. Kurulum bitince korunanları yerleştir:
  odev.docx                  → docs/project/
  proje-teknoloji-ve-plan.md → docs/sunum/
  sunum-anlatim-plani.md           → docs/sunum/
  YENI-OTURUM.md             → kurulum bitince silinir (işlevi biter)
```

⛔ **"_devir klasörünü sil" deme.** İçinde ~190 sayfalık teknoloji rehberi ve
sunum taslağı var; ikisi de projenin kalıcı dokümanları. Yalnızca kurulum
yönergesi dosyaları silinir.


---

# B) BUNDAN SONRA — belediyede yeni proje açarken

Programlar ve eklentiler zaten kurulu. Yapılacaklar çok daha kısa:

## B1. Boş klasör aç

```powershell
cd C:\Users\<kullanıcı-adın>\Documents
mkdir yeni-proje-adi
cd yeni-proje-adi
code .
```

## B2. Kiti güncelle

Claude Code'da:

```
proje-kiti eklentisini güncelle
```

Sonra `Ctrl+Shift+P` → **Developer: Reload Window**

⚠️ Bunu **her yeni projede** yap. Kite sürekli yeni kurallar ekliyoruz;
eski sürümle başlarsan o kuralları kaybedersin.

## B3. Kurulumu başlat

```
/yeni-proje
```

Bundan sonrası otomatik. Sana sırayla soracak:

1. **Bu proje kimin için?** → `kurum projesi`
   (Bu cevap her şeyi belirliyor: GitLab kullanılacak, deploy DevOps'ta,
   sana domain/sunucu işi verilmeyecek.)
2. **Proje tipi?** → web / mobil / ikisi
3. **Backend kurgusu** — dört soru sorup kendisi karar verecek
4. **Analiz dokümanı** — kurumun verdiği dosyayı ver
5. Sonra PRD görüşmesi başlayacak; sorulara cevap vereceksin

## B4. Gizli bilgi kuralı

Yeni projede kurum bilgisi (sunucu adresi, iç ağ, gerçek veri) çıkarsa:

- **Kite yazılmaz** — kit deposu herkese açık
- Projenin kendi (özel) deposuna yazılır
- Değerler `.env` içine, `.env.example` içine sadece isimler

Bu kural zaten kitte yazılı, Claude uyacak. Sen sadece bil.

---

# Sık karşılaşılan sorunlar

| Belirti | Çözüm |
|---|---|
| `docker` bulunamıyor | Docker Desktop'ı başlat, sistem tepsisindeki balina yeşil olmalı |
| `pnpm` bulunamıyor | PowerShell'i kapat-aç |
| Klonlama "repository not found" | `gh auth status` — yanlış hesapla girilmiş olabilir |
| Betikler `\r` hatası | `git config --global core.autocrlf input` yap, sonra yeniden klonla |
| Plugin'ler görünmüyor | `Ctrl+Shift+P` → Developer: Reload Window |
| Claude eski kiti kullanıyor | Plugin'i güncelle **ve** Reload Window yap — ikisi birden gerekli |

---

# İki hesabı karıştırma

Bu en sık yapılan hata:

| Nerede | Hangi hesap | Ne için |
|---|---|---|
| `gh auth login` (PowerShell) | **Kişisel GitHub** (bariskose9) | Hazırlık deposunu indirmek, kiti güncellemek |
| VS Code → Claude Code eklentisi | **Belediyenin hesabı** | Claude'u kullanmak |
| İleride kurumun GitLab'ı | **Kurumun hesabı** | Proje kodunu teslim etmek |

Üçü ayrı ve üçü de gerekli.
