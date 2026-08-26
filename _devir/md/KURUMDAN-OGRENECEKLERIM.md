# Kurumdan öğreneceklerim

> **Bu dosya, İzmir Büyükşehir'e soracağın soruların listesi.** Her sorunun
> yanında **neden sorduğun** ve **cevaba göre ne değişeceği** yazılı — böylece
> karşı taraf "bu niye soruyor" demez, sen de cevabı alınca ne yapacağını
> bilirsin.
>
> ⛔ **Buradaki hiçbir soru mühendislik sorusu değildir.** Hepsi yalnızca
> kurumun bilebileceği **olgular**. Teknik kararları ben veriyorum, sana
> gerekçesiyle onaylatıyorum.

---

## Nasıl kullanılır

| Durum | Ne yap |
|---|---|
| Cevabı aldın | Satırın **Cevap** kolonunu doldur |
| Cevap "bilmiyorum" geldi | ⚠️ **Varsayım** kolonunu doldur — ben o varsayımla ilerlerim |
| Soru gereksiz çıktı | Sil, ama **neden gereksizdi** yaz |

⭐ Cevaplar geldikçe `docs/project/PRD.md` ve `docs/project/altyapi-durumu.md`
dosyalarına işlenir. Bu dosya kurulum bitince silinir.

---

# BÖLÜM 1 — Kesinlikle sorulması gerekenler

Bunlar cevaplanmadan **kod yazılmaya başlanabilir**, ama teslimden önce
mutlaka netleşmeli.

## 1.1 ⭐ Veri tabanı isimlendirme kuralları — EN ÖNEMLİSİ

> **Soru:** *"Ödevin 11. maddesinde 'veri tabanı tasarımı **kurum tarafından
> iletilen** veri tabanı geliştirme ve isimlendirme kurallarına uygun olmalıdır'
> yazıyor. Böyle bir doküman var mı, alabilir miyim?"*

| | |
|---|---|
| **Neden soruyorum** | Ödev metni böyle bir dokümandan **bahsediyor ama eklememiş**. Varsa ve uymazsak, teslim doğrudan kural ihlali sayılır |
| **Cevaba göre ne değişir** | Tablo adları (`work_order` mı `WorkOrder` mı), kolon adları, birincil anahtar biçimi, tarih alanı adları — **tüm veri modeli** |
| **Cevap gelmezse** | PostgreSQL yaygın pratiği: `snake_case` tablo/kolon, çoğul tablo adı, `id` birincil anahtar, `created_at`/`updated_at`. PRD → Varsayımlar'a yazılır |
| **Cevap** | *(doldurulacak)* |

⚠️ **Bu soruyu ilk gün sor.** Cevap sonradan gelirse tüm migration'lar yeniden
yazılır.

## 1.2 Kod nereye gönderilecek

> **Soru:** *"Bu ödev için kodu nereye göndermemi istersiniz — GitHub mı,
> kurumun GitLab'ı mı? GitLab ise hesap ve adres verilecek mi?"*

| | |
|---|---|
| **Neden soruyorum** | Ödev §30 **Pull Request veya Merge Request** istiyor. PR/MR yalnızca bir barındırma servisinde olur; git tek başına üretemez |
| **Cevaba göre ne değişir** | Hangi CI dosyası birincil olur (`.github/workflows/ci.yml` mi `.gitlab-ci.yml` mi) ve teslim linkini nereden vereceğin |
| **Cevap gelmezse** | GitHub'da başlarım. ⭐ **İkisi de hazır olacak** — CI adımları `pnpm ci:verify` içinde, iki platform dosyası da onu çağırıyor. GitLab adresi sonra gelirse ikinci remote eklenir, ek iş çıkmaz |
| **Cevap** | *(doldurulacak)* |

## 1.3 Teslim tarihi

> **Soru:** *"Teslim için son tarih nedir? Teknik sunum ne zaman planlanıyor?"*

| | |
|---|---|
| **Neden soruyorum** | Ödev metninde **hiçbir yerde tarih yok** (795 satır tarandı). Kapsam kararları buna göre verilir |
| **Cevaba göre ne değişir** | Süre darsa: mobil, dosya yükleme gibi **ödevde istenmeyen** maddeler kapsam dışı kalır. Bolsa eklenebilir |
| **Cevap gelmezse** | ~10 iş günü hedefiyle ilerlenir |
| **Cevap** | *(doldurulacak)* |

---

# BÖLÜM 2 — DevOps sınırı

⭐ **Üç sorunun tamamı ve "bu ne demek" açıklamaları
`calisma-kilavuzu` → *"DEVOPS SINIRI — işe başlamadan sorulacak üç soru"*
bölümünde.** Orada her sorunun ne anlama geldiği, neyi değiştirdiği ve cevap
gelmezse ne yapılacağı yazılı — burada tekrarlanmıyor.

Burada yalnızca **cevaplar** tutuluyor:

| # | Soru | Cevap |
|---|---|---|
| 2.1 | Migration'ı canlıda kim çalıştırıyor | *(doldurulacak)* |
| 2.2 | Gizli değerleri kim, nereye giriyor | *(doldurulacak)* |
| 2.3 | Bir sürüm bozarsa geri almayı kim yapıyor | *(doldurulacak)* |

⚠️ **Üçünü de ilk gün sor.** Cevaplar `altyapi-durumu.md`'ye de işlenir —
kodda görünmezler ve altı ay sonra kimse hatırlamaz.

---

# BÖLÜM 3 — Kapsam soruları (istenirse süre isteyeceğin şeyler)

> Bunlar **ödevde istenmiyor.** Sorulmazsa gündeme getirmene gerek yok. Ama
> "şu da olsa iyi olurdu" denirse cevabın hazır olsun.

| Konu | Ödevde var mı | Eklenirse ne olur |
|---|---|---|
| **Mobil uygulama** (Expo) | ⛔ Yok | ⚠️ **Ek süre gerekir.** Mimari hazır (aynı API), ama ekranlar, mağaza süreci ve test ayrı iş |
| **Fotoğraf / dosya eki** | ⛔ Yok — yalnızca **yorum** isteniyor (§39, §91) | Depolama servisi + dosya doğrulama + boyut sınırı + KVKK (fotoğrafta insan olabilir). ⭐ **Veri modelinde yeri hazır bırakılacak**, eklenmesi kolay olsun |
| **Gerçek e-posta / SMS** | ⛔ Yok — **sistem içi bildirim** isteniyor (§16) | Sağlayıcı hesabı + alan adı doğrulama gerekir |
| **Gerçek personel entegrasyonu** | ⛔ Yok | Sahte veri kullanılacak (§5.1 gerçek veriyi zaten yasaklıyor) |
| **Çok dillilik** | ⛔ Yok | Tek dil: Türkçe |

⭐ **Bunların hepsi PRD → "Kapsam dışı" bölümüne gerekçesiyle yazılacak.**
Yazmak zayıflık değil, **kapsama hâkim olmak** demektir — ödev §31 zaten
"bilinen eksikler"i soruyor.

---

# BÖLÜM 4 — Sorulmayacaklar (ben karar veriyorum)

Bunları kuruma sorma. Cevabı kurumun bilmesi beklenmez ve zaten karara
bağlandı — hepsinin gerekçesi `proje-teknoloji-ve-plan.md` içinde:

| Konu | Karar | Gerekçe nerede |
|---|---|---|
| Hangi teknolojiler | Next.js + NestJS + Prisma + PostgreSQL + Zod + BullMQ | BÖLÜM A, C |
| Mimari | Modüler monolit + Clean Architecture | E.1 |
| SLA süreleri ve takvim | Kritik 3sa (7/24) · Yüksek 8sa · Normal 24sa · Düşük 72sa (mesai) | **E.4** |
| İş emri numarası biçimi | `IE-2026-000148` — yıl + artan sayaç | E.4 civarı |
| Sayfalama | Offset (sayfa numarası gösterildiği için) | E.10 |
| Mapping | Zod şeması + Prisma `select` — kütüphane yok | E.6 |
| Test stratejisi | Vitest + Testcontainers + dependency-cruiser + Playwright | C.7, C.11 |
| Veri modeli tasarımı | ⭐ Ödev **açıkça** *"tarafınızdan tasarlanmalıdır"* diyor (§11) | E.9 |

⛔ **§11'in son cümlesi önemli:** *"Hazır bir şemayı veya bu çalışmayla ilgisiz
mevcut bir projeyi uyarlamak yerine, verilen iş ihtiyacına göre veri modeli
oluşturmanız beklenmektedir."*

Yani **başka bir yerden şema kopyalamak açıkça yasaklanmış.** Veri modelini
ödevin iş kurallarından türeteceğiz.
