# `_devir` klasörü — ne nerede

Bu klasör, projenin **hazırlık aşamasında** üretilen belgeleri taşır. Kod
yazılmaya başlandığında içeriği `docs/` altına yerleşecek ve bu klasör
kalkacak.

## İki klasör, aynı içerik

| Klasör | İçinde | Ne için |
|---|---|---|
| **`md/`** | Markdown kaynak dosyaları | VS Code'da okumak, düzenlemek, **ajanın okuması** |
| **`pdf/`** | Aynı belgelerin PDF hâli | **Telefonda okumak** |

⭐ **Dosya adları birebir aynı** — yalnızca uzantı değişir. `md/KURULUM.md` ile
`pdf/KURULUM.pdf` aynı belgedir.

⚠️ **PDF'lerin hepsi karanlık temalıdır** (telefonda ve loş ortamda okumak
için). Yazdırman gerekirse söyle, aydınlık sürüm üretilir — karanlık sürüm
sayfayı simsiyah basar.

## Belgeler

| Belge | Kim okur | Ne verir |
|---|---|---|
| **`YENI-OTURUM`** | ⭐ **Ajan** | Devir notu: rol, kararlar, okuma sırası, açık işler |
| **`odev`** | İkisi | Kurumun teknik değerlendirme çalışması |
| **`proje-teknoloji-ve-plan`** | İkisi | Tüm teknoloji kararları, kavramlar, 17 adımlık yapım planı |
| **`KURUMDAN-OGRENECEKLERIM`** | **Sen** | Kuruma soracakların + cevap gelmezse ne yapılacağı |
| **`KURULUM`** | **Sen** | Windows'ta ne kurulacak, nasıl başlanacak |
| **`sunum-anlatim-plani`** | **Sen** | Teknik sunumun anlatım sırası |
| **`calisma-kilavuzu`** | **Sen** | Kitle nasıl çalışılır — her projede aynı, kitten gelir |

## ⛔ Depoda olmayan dosyalar

Bunlar **yerelde var ama depoya gönderilmiyor** (`.gitignore`):

```
_devir/odev.docx        ← kurumun verdiği Word dosyası
_devir/md/odev.md       ← okunabilir hâli
_devir/pdf/odev.pdf     ← telefonda okumak için
```

**Sebep:** Depo herkese açık. Kurumun değerlendirme çalışması yayımlanmaz —
sonraki adaylar bulabilir ve kurum bunu güven ihlali sayabilir.

⭐ **Rehberdeki alıntılar sorun değil:** ölçüldü, ödevin yalnızca **%13'ü**
rehberde birebir geçiyor ve hepsi yorumla birlikte kısa gereksinim satırları.
Bu normal kaynak gösterme; ödevi yeniden yayımlamak değil.

⚠️ **Windows makinesine geçerken bu üç dosyayı elle taşı** (USB / OneDrive /
WhatsApp). Depoyu klonlamak yetmez.
