# Windows makinesine taşıma — adım adım

Bu proje klasörünü GitHub üzerinden Windows makinesine taşımanın tam yolu.
Her adımı sırayla uygula; atlanırsa sonraki adım çalışmaz.

---

## BÖLÜM 1 — Mac'te yapılacaklar (bir kez)

### 1.1 Depoyu hazırla

Claude Code'a şunu de:

```
Bu klasörü git deposu yap ve kişisel GitHub'ıma ÖZEL (private) depo olarak gönder.
Depo adı: bakim-is-emri-hazirlik
```

Yapılacak işlemler (Claude bunları senin yerine yapar):

```bash
git init
git branch -M main
git add .
git commit -m "chore: hazırlık dokümanları ve kit incelemesi"
gh repo create bakim-is-emri-hazirlik --private --source=. --push
```

⛔ **`--private` şart.** Ödev dosyası ve hazırlık notların içeride; herkese açık
olmamalı.

### 1.2 Depo adresini not al

Komut bitince şuna benzer bir adres verir:

```
https://github.com/bariskose9/bakim-is-emri-hazirlik
```

Bu adresi telefonuna veya bir not defterine yaz — Windows'ta lazım olacak.

---

## BÖLÜM 2 — Windows makinesinde yapılacaklar

### 2.1 Gerekli programları kur

Sırayla, hepsi ücretsiz:

| Program | Nereden | Kontrol komutu |
|---|---|---|
| **Git** | git-scm.com | `git --version` |
| **Node.js 24 LTS** | nodejs.org | `node --version` |
| **pnpm** | `npm install -g pnpm` | `pnpm --version` |
| **Docker Desktop** | docker.com | `docker --version` |
| **VS Code** | code.visualstudio.com | — |
| **GitHub CLI** | cli.github.com | `gh --version` |

⚠️ **Docker Desktop kurarken WSL2 arka ucunu seç.** Kurulum sırasında sorar;
"Use WSL 2 instead of Hyper-V" işaretli olmalı.

### 2.2 Git'i tanıt

PowerShell aç, sırayla:

```powershell
git config --global user.name "Barış Köse"
git config --global user.email "kilicarslan45@gmail.com"
git config --global core.autocrlf input
```

⚠️ Üçüncü satır önemli: Windows dosyaları farklı satır sonuyla indirir ve bu,
Docker içindeki betikleri bozar. Bu ayar onu engeller.

### 2.3 GitHub'a giriş yap

```powershell
gh auth login
```

Sorulara cevaplar: **GitHub.com** → **HTTPS** → **Login with a web browser**.
Ekranda bir kod çıkar, tarayıcıda yapıştırırsın.

⚠️ Burada **kendi kişisel GitHub hesabına** giriyorsun — belediyenin hesabına
değil. Dokümanlar senin deponda.

### 2.4 Projeyi indir

```powershell
cd C:\Users\<kullanıcı-adın>\Documents
gh repo clone bariskose9/bakim-is-emri-hazirlik
cd bakim-is-emri-hazirlik
```

Klasör indi. İçinde `_devir` ve `_kit-inceleme` klasörlerini göreceksin.

### 2.5 VS Code'da aç

```powershell
code .
```

### 2.6 Claude Code eklentisini kur ve giriş yap

VS Code içinde:

1. Sol kenardaki **Extensions** ikonu (kare şeklinde dört kutu)
2. Arama kutusuna **Claude Code** yaz
3. Anthropic tarafından yayınlanan eklentiyi **Install**
4. Kurulunca giriş isteyecek → **belediyenin verdiği hesapla** gir

### 2.7 Eklentileri (plugin) kur

Claude Code açıldıktan sonra terminale sırayla yapıştır:

```
claude plugin marketplace add bariskose9/bariskose-skills
claude plugin marketplace add addyosmani/agent-skills
claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp
```

```
claude plugin install proje-kiti@bariskose-skills
claude plugin install agent-skills@addy-agent-skills
claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

`/plugin` ekranından kurmak istersen aranacak adlar:

| Marketplace adı | Plugin adı |
|---|---|
| `bariskose-skills` | `proje-kiti` |
| `addy-agent-skills` | `agent-skills` |
| `chrome-devtools-plugins` | `chrome-devtools-mcp` |

### 2.8 Yeniden başlat

`Ctrl+Shift+P` → yaz: **Developer: Reload Window** → Enter.

Kurulum tamam. Doğrulamak için:

```
claude plugin list
```

Üçü de görünmeli.

---

## BÖLÜM 3 — Çalışmaya başlarken

### 3.1 İlk mesaj

Claude Code'a şunu yapıştır:

```
_devir klasöründeki dosyaları oku, kaldığımız yerden devam ediyoruz:
_devir/kurulum-plani.md
_devir/proje-teknoloji-ve-plan.md
_devir/sunum-taslagi.md

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

### 3.2 Projeyi kurmaya geçerken

Rehber incelemesi bittiğinde, kuruluma başlamadan önce:

```
_kit-inceleme klasörünü sil.

_devir klasöründen ŞUNLARI SİL (işlevi bitti):
  NE-YAPACAGIM.md, WINDOWS-ADIMLARI.md, OKU-ONCE.md, WHATSAPP-NOTU.txt,
  kurulum-plani.md  (eski taslak — içeriği rehbere taşındı)

_devir klasöründen ŞUNLARI KORU, geçici bir yere taşı:
  YENI-OTURUM.md, odev.docx, proje-teknoloji-ve-plan.md, sunum-taslagi.md

Sonra /yeni-proje çalıştır. Kurulum bitince korunanları yerleştir:
  odev.docx                  → docs/project/
  proje-teknoloji-ve-plan.md → docs/sunum/
  sunum-taslagi.md           → docs/sunum/
  YENI-OTURUM.md             → kurulum bitince silinir (işlevi biter)
```

⛔ **"_devir klasörünü sil" deme.** İçinde ~190 sayfalık teknoloji rehberi ve
sunum taslağı var; ikisi de projenin kalıcı dokümanları. Yalnızca kurulum
yönergesi dosyaları silinir.

⚠️ `_kit-inceleme` neden siliniyor: `/yeni-proje` aynı dosyaları
`docs/standards/` olarak zaten getirecek. İkisi bir arada durursa aynı bilgi
iki yerde kalır, biri güncellenir diğeri eskir.

---

## BÖLÜM 4 — İki makine arasında senkron

Aynı projeye iki makineden dokunacaksan kural basit: **çalışmaya başlamadan
önce indir, bitirince gönder.**

```powershell
git pull        # başlarken — karşı makinedeki değişiklikleri al
git push        # bitirirken — kendi değişikliklerini gönder
```

⛔ **`git pull` yapmadan çalışmaya başlama.** Aynı dosyayı iki makinede
değiştirirsen çakışma çıkar ve çözmek zaman alır.

---

## Sık karşılaşılan sorunlar

| Belirti | Sebep | Çözüm |
|---|---|---|
| `docker` komutu bulunamıyor | Docker Desktop kapalı | Başlat menüsünden aç, balina ikonu yeşil olmalı |
| `pnpm` bulunamıyor | PATH güncellenmemiş | PowerShell'i kapat-aç |
| Klonlama "repository not found" | Yanlış hesapla giriş | `gh auth status` ile kontrol et |
| Betikler `\r` hatası veriyor | Satır sonu bozulmuş | `git config --global core.autocrlf input` sonra yeniden klonla |
| Plugin'ler görünmüyor | Yeniden başlatılmamış | `Ctrl+Shift+P` → Developer: Reload Window |
