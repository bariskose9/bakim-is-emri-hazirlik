# Repo Yapısı

<!--
ŞABLON — proje kurulduktan sonra GERÇEK klasör yapısına göre doldurulur.
Bu yorum bloğu doldurduktan sonra silinir.

BU DOSYA NEDEN VAR
Kod okuyamayan biri (ve hafızasız bir ajan oturumu) "bu dosya ne işe yarıyor"
sorusunu buradan cevaplar. Klasör listesi değil, HANGİ İŞİN NEREDE OLDUĞU
haritasıdır.

NASIL DOLDURULUR
Tahminle değil, kurulan projeye BAKARAK. Var olmayan klasör yazılmaz.
Her satırda "ne var" değil "ne işe yarıyor" anlatılır.

NE ZAMAN GÜNCELLENİR
Yeni bir üst düzey klasör açıldığında veya bir klasörün işi değiştiğinde.
-->

**Son güncelleme:** <!-- TARİH -->

## Tek cümlede

<!-- Bu depo ne barındırıyor: tek uygulama mı, web + mobil mi, kaç deploy hedefi. -->

## Üst düzey klasörler

| Klasör / dosya | Ne işe yarıyor | Elle düzenlenir mi |
|---|---|:---:|
| `src/` | Uygulama kodu | ✔ |
| `docs/standards/` | Mühendislik kuralları — **her projede aynı** | ✘ (kural değişmedikçe) |
| `docs/project/` | Bu projeye özel: PRD, roadmap, kararlar, altyapı durumu | ✔ |
| `tests/` | Testler | ✔ |
| | | |

## Kod nasıl bölünmüş

<!--
Katman sırası `01-architecture.md`'de: UI → API/route → servis → repository → DB.
Burada o katmanların bu projede FİİLEN hangi klasöre denk geldiği yazılır.
Özellik bazlı klasörleme kullanılıyorsa bir örnek özellik üzerinden gösterilir.
-->

| Katman | Nerede | Kural |
|---|---|---|
| Arayüz | | İş mantığı yazılmaz |
| API ucu | | Girdi doğrulaması burada başlar |
| İş mantığı | | |
| Veri erişimi | | ORM/SQL yalnızca burada |

## Üretilen dosyalar — elle düzenlenmez

<!-- ORM istemcisi, derleme çıktısı, tip üretimi vb. Yazılmazsa biri elle
düzenler ve ilk derlemede kaybolur. -->

- `<yol>` — <ne üretiyor, hangi komutla>

## Nereye bakmalı — sık sorulanlar

| Soru | Cevap |
|---|---|
| Yeni bir sayfa nereye eklenir | |
| Yeni bir API ucu nereye eklenir | |
| Veritabanı şeması nerede | |
| Ortam değişkenleri nerede tanımlı | `.env.example` (adlar) · `docs/project/altyapi-durumu.md` (hangi ortamda ne var) |
| Bir kuralın gerekçesi nerede | `docs/project/decisions/ADR-*.md` |
