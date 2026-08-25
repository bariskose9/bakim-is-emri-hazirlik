# Windows makinesine geçiş — kontrol listesi

Bu klasör, projeye Windows 11 makinesinde devam edebilmek için gereken her şeyi
içerir. `_devir` ve `_kit-inceleme` klasörleri **kuruluma başlamadan önce
silinecek** geçici klasörlerdir.

## 1. Bu klasördeki dosyalar

| Dosya | Ne işe yarar | Kim okur |
|---|---|---|
| ⭐ **`YENI-OTURUM.md`** | **Devir notu** — yeni Claude oturumuna verilecek. Neyin kararlaştırıldığı, hangi dosyanın hangi sırayla okunacağı, neyin sorulup neyin sorulmayacağı | **Yeni Claude** |
| `odev.docx` | Kurumun verdiği teknik değerlendirme çalışması | İkisi |
| `proje-teknoloji-ve-plan.md` | Tüm teknoloji kararları, kavramlar ve 17 adımlık yapım planı | İkisi |
| `sunum-taslagi.md` | Teknik inceleme sunumunun iskeleti | Sen |
| `calisma-kilavuzu.md` | Kitle nasıl çalışılır (her projede aynı) | Sen |
| `kurulum-plani.md` | ⛔ **ESKİ TASLAK — bayat.** İçeriği rehbere taşındı. Çelişki olursa **rehber kazanır.** Yalnızca arşiv | — |

⭐ **Yeni oturumu açtığında yapıştıracağın metin:**

```
_devir/YENI-OTURUM.md dosyasını oku ve içindeki talimatları uygula.
```

⚠️ **Bu dosyalar claude.ai üzerindeki linklerden AÇILAMAZ** — o linkler kişisel
hesaba özeldir, kurumun verdiği hesapla görünmez. Dosyaları bu klasörle
birlikte taşımak zorunludur (USB, OneDrive veya kişisel GitHub deposu).

## 2. Windows'ta kurulacak eklentiler

Claude Code açıldıktan sonra terminale sırayla:

```
claude plugin marketplace add bariskose9/bariskose-skills
claude plugin marketplace add addyosmani/agent-skills
claude plugin marketplace add ChromeDevTools/chrome-devtools-mcp

claude plugin install proje-kiti@bariskose-skills
claude plugin install agent-skills@addy-agent-skills
claude plugin install chrome-devtools-mcp@chrome-devtools-plugins
```

`/plugin` ekranından kuracaksan aranacak adlar:

| Marketplace | Plugin |
|---|---|
| `bariskose-skills` | `proje-kiti` |
| `addy-agent-skills` | `agent-skills` |
| `chrome-devtools-plugins` | `chrome-devtools-mcp` |

Kurulum sonrası Claude Code **yeniden başlatılır** (VS Code'da
`Ctrl+Shift+P` → *Developer: Reload Window*).

## 3. Windows'a özgü dikkat edilecekler

**Sesli bildirim ayarı doğrulanmamış.** Kitin Adım 0b bölümündeki PowerShell
bloğu şimdiye kadar yalnızca macOS'te test edildi; Windows'ta ilk kez
çalıştırılacak. Çalışmazsa hata mesajı kite işlenmeli.

**Yollar değişir.** macOS'teki `~/.claude/...` yolunun Windows karşılığı:
`C:\Users\<kullanıcı>\.claude\...`

**Portlar.** Bu makinede paralel başka bir proje çalıştığı için portlar
kaydırılmıştı (web 3100, api 4100, postgres 55432, redis 6479). Windows
makinesinde başka proje yoksa `.env` içinde standart portlara alınabilir —
konteyner içi portlar zaten hep standarttır, değişen yalnızca host eşlemesidir.

**Docker Desktop** kurulu olmalı (WSL2 arka ucuyla).

## 4. İlk oturumda verilecek mesaj

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
- Verilen metinlerdeki örnekler ve terimler korunur, yalnızca biçim düzeltilir.
```

## 5. Kuruluma başlamadan önce

```
_kit-inceleme klasörünü sil.

_devir klasöründen ŞUNLARI SİL (işlevi bitti):
  NE-YAPACAGIM.md, WINDOWS-ADIMLARI.md, OKU-ONCE.md, WHATSAPP-NOTU.txt

_devir klasöründen ŞUNLARI KORU, geçici bir yere taşı:
  odev.docx, kurulum-plani.md, proje-teknoloji-ve-plan.md, sunum-taslagi.md

Sonra /yeni-proje çalıştır. Kurulum bitince korunanları yerleştir:
  odev.docx ve kurulum-plani.md   → docs/project/
  proje-teknoloji-ve-plan.md      → docs/sunum/
  sunum-taslagi.md                → docs/sunum/
```

⛔ **"_devir klasörünü sil" deme.** İçinde 40 sayfalık teknoloji rehberi ve
sunum taslağı var; ikisi de projenin kalıcı dokümanları. Yalnızca kurulum
yönergesi dosyaları silinir.

## PDF sürümleri

Her doküman iki PDF olarak üretiliyor:

| Dosya | Ne için |
|---|---|
| `*.pdf` | **Aydınlık** — sunum, ekrandan gösterme, yazdırma |
| `*-KARANLIK.pdf` | **Karanlık** — telefonda ve loş ortamda okuma |

⚠️ Karanlık sürüm **yazdırılmaz** — sayfayı simsiyah basar, kartuşu bitirir.

ℹ️ OLED ekranlarda (çoğu modern telefon) siyah piksel kapandığı için karanlık
sürüm gerçekten pil tasarrufu sağlar. LCD ekranda fark olmaz.

ℹ️ Okuma açısından ikisi farklı ortamlarda kazanır: aydınlık ortamda koyu
yazı/açık zemin okuma keskinliğini artırır, loş ortamda karanlık zemin parlamayı
azaltır.

**Yeniden üretmek için:** markdown dosyasının sonuna karanlık mod CSS bloğu
eklenip PDF üretilir. Stil bloğu **dosyanın sonunda** olmalı — başa konursa ilk
sayfa boş çıkıyor.
