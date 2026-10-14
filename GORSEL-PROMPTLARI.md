# Sorumluluk Komuta Merkezi — Ekran Görseli Promptları (ChatGPT)

**Amaç:** Dashboard'u kodlamadan önce her ekranın nasıl görüneceğini ChatGPT'de görmek.
**Kaynak:** `Sorumluluk-Dashboard-Yapi.xlsx` (sizin onayladığınız son hali) + Yangın Komuta Merkezi ekranları.

---

## 0. Nasıl kullanılır

1. **Tek bir ChatGPT sohbeti açın.** Bütün ekranları aynı sohbette üretin. Model önceki görseli hatırlar, böylece stil tutarlı kalır.
2. İlk mesaja **§1 Stil Kilidi** ile **Ekran 1**'i birlikte yapıştırın.
3. Sonraki ekranlarda yalnız ekran promptunu yapıştırın ve başına şu satırı ekleyin:
   `Use exactly the same design system, sidebar, top bar, colors and typography as the previous image.`
4. Görselde yazılar bozulursa şunu yazın:
   `Regenerate with fewer text labels; keep only the headings and the numbers, render all Turkish characters correctly (ş, ğ, ı, İ, ç, ö, ü).`
5. Format her ekranda **yatay, 16:9 (1536×1024)**. ChatGPT bazen dikey üretir; o zaman şunu yazın: `landscape 16:9 please`.
6. Promptlar İngilizce yazıldı çünkü görsel modelleri İngilizce talimatı daha isabetli uyguluyor. **Ekranda görünecek bütün metinler Türkçe** ve tırnak içinde verildi.

> Görsellerdeki bütün isim, acente ve rakamlar **uydurmadır**. Gerçek kişi veya acente adı kullanılmadı. Yangın ekranındaki "Raziye G.", "Mustafa" gibi isimler bilerek çıkarıldı.

---

## Yangın ekranına göre tasarım kararları

| Yangın'daki durum | Sorumlulukta ne yapılıyor | Neden |
|---|---|---|
| Fotoğraflı koyu başlık bandı (karşılaştırma, hasar sayfaları) | Kaldırıldı. Yerine sade sayfa başlığı ve filtre çubuğu | Ekranın üst %20'sini veri yerine fotoğraf kaplıyordu |
| Kırmızı ağırlıklı büyük bloklar | Kırmızı yalnız **uyarı ve vurgu** için. Veri renkleri lacivert ve gri tonlarında | Her şey kırmızı olunca gerçek alarm görünmez oluyor |
| Sol menüde 13 araç kutusu | 3 grup halinde sade liste: **Panolar / Çalışma Masası / Bilgi**. Araçlar için ⌘K komut çubuğu | Kutu ızgarası taranması zor bir görüntü oluşturuyordu |
| Sağ altta maskot sohbet balonu | Sağdan açılan **"UW Asistanı" paneli** | Profesyonel görünüm; içerik tabloların üstünü kapatmaz |
| Kutularda dönem bilgisi tutarsız | Her kartın sağ üstünde küçük **dönem rozeti** ("Oca–Eyl 2026", "kesit 30.09") | Ölçüm kuralı 3: her kutu kendi dönemini yazar |
| Verinin nereden geldiği belli değil | Her kartta küçük **kaynak rozeti**: `DATA` · `AI` · `AI+DATA` · `TOOL` | Excel'deki lejant ekrana taşınıyor; AI çıktısı veriyle karışmıyor |
| Tek görünüm | Sağ üstte **Yönetici / UW rol anahtarı** | UW yalnız kendi bölgelerini görür (Teyit T08) |

---

## 1. Stil Kilidi (ilk mesajın başına bir kez yapıştırın)

```
DESIGN SYSTEM LOCK — apply to every screen in this conversation.

Product: "Komuta Merkezi — Sorumluluk Masası", an internal underwriting command-center web app for the Casualty & Financial Lines desk of a Turkish insurance company. All on-screen text is in TURKISH and must be spelled exactly as given, with correct Turkish characters (ş, ğ, ı, İ, ç, ö, ü). Use Turkish number formatting (1.797 M ₺, %34,7, 2,46‰).

Render as a high-fidelity desktop web app UI screenshot, flat and pixel-crisp, viewed straight-on (no perspective, no device frame, no hands, no laptop mockup). Landscape 16:9, 1536x1024.

Layout shell (identical on every screen):
- Left sidebar, 240px, white, thin right border. Top: a small red rounded-square logo tile with a white "S", next to bold "Komuta Merkezi" and a grey subline "Sorumluluk · CFL". Below, three groups with small uppercase grey group labels:
  "PANOLAR": Genel Bakış, Üretim & Performans, Bölge Kartı, Yenileme & Kayıp, Portföy Hasar, Sektör / TSB
  "ÇALIŞMA MASASI": UW Asistanı, Fakültatif İşler, Kişisel Kokpit
  "BİLGİ": UW Kütüphanesi, Proje Takip ↗
  Each item = thin-line icon + label; the active item has a soft red tinted pill background and red text.
  At the sidebar bottom: a small grey card "Veri tazeliği · Üretim 30.09 · Teklif 13.10 · Hasar 30.09".
- Top bar, 64px, white, thin bottom border: a search/command field "Ara veya komut yaz…  ⌘K" on the left; in the middle a row of rounded filter chips "Dönem: 2026 Oca–Eyl", "Bölge: Tümü", "Ürün: Tümü", "Kanal: Tümü"; on the right a segmented role switch "Yönetici | UW" (Yönetici selected), a bell icon with a small red dot, and a circular avatar with initials "MA".
- Page header under the top bar: large page title left, grey one-line subtitle under it, primary action button right.

Visual language:
- Background #F6F7F9; cards pure white, 12px radius, 1px #E6E8EC border, very soft shadow; generous 24px gutters; bento-style grid.
- Brand red #C8102E used ONLY for the active nav item, primary buttons, alerts and negative values. Data series use deep navy #1F3864, slate #64748B and a muted teal #2A9D8F. Positive values green #0A7D32, warning amber #B45309.
- Typography: modern geometric sans-serif (Inter-like). Page title 28px semibold; card titles 15px semibold; KPI numbers 32px bold with tabular figures; labels 12px grey.
- Every card has two tiny badges in its top-right corner: a period badge (e.g. "Oca–Eyl 2026") and a source badge in small caps — "DATA" (light blue), "AI" (light violet), "AI+DATA" (light teal), "TOOL" (light orange).
- Charts: clean, thin lines, no 3D, no gradients on bars, light grid lines, direct labels instead of legends where possible.
- Overall mood: calm, precise, executive, modern SaaS (Linear / Stripe dashboard quality), lots of white space, nothing cartoonish.

Negative constraints: no stock photos, no photographic header banners, no mascots or cartoon characters, no emojis, no lorem ipsum, no English UI text, no real company or person names, no watermark, no 3D or glassmorphism, no dark neon.
```

---

## 2. Ekran promptları

### Ekran 1 — Genel Bakış (Yönetici)

```
SCREEN: "Genel Bakış" (active sidebar item: Genel Bakış). Role switch shows "Yönetici".

Page title "Genel Bakış", subtitle "Sorumluluk masası · 2026 Oca–Eyl · kapsam: tüm bölgeler". Primary button right: "Rapor indir".

Thin amber info strip above the content: "Teklif verisi 13.10'da yüklendi · Hasar verisi 30.09 kesitli".

Row 1 — five KPI cards in one row, each with a tiny sparkline:
1) "Brüt Prim (YTD)" — "1.284 M ₺", green chip "▲ %31,4", grey note "geçen yıl 977 M ₺"
2) "Bütçe Gerçekleşme" — "%78,6", thin progress bar, grey note "yıl sonu tahmini %104"
3) "Hit Ratio" — "%46,2", red chip "▼ 3,1 pp", grey note "tekil sigortalı bazında"
4) "Hasar / Prim (UW yılı)" — "%41,8", grey note "2024–2026 · 2026 olgunlaşmadı"
5) "Açık Yenileme (60 gün)" — "214 poliçe", grey note "186,4 M ₺"

Row 2 — wide card left (2/3 width) "Portföy Değerlendirmesi" with subtitle "8 kriter · şiddete göre sıralı · eşikler ayar dosyasından", a small violet button "✦ Yorumla" (AI) at top-right. Inside: a 2-column grid of 8 compact finding tiles, each with a colored left border (red / amber / green), a bold title, one grey measurement line and one action line starting with "→":
  red "Hit ratio düşüyor" — "%49,3 → %46,2 (−3,1 pp)" — "→ Kaçan iş sebeplerine bak"
  red "Yetki aşımı birikiyor" — "17 teklif onay bekliyor · ort. 4,2 gün" — "→ Onay kuyruğunu boşalt"
  amber "Acente yoğunlaşması" — "İlk 3 acente %38 · HHI 1.640" — "→ Bağımlılığı izle"
  amber "TAT hedef üstünde" — "Medyan 6,8 gün · SLA dışı %14" — "→ İş yükü dağılımına bak"
  amber "Treaty kapasitesi" — "9 poliçe fakültatif gerektiriyor" — "→ Fakültatif listesini kontrol et"
  green "Büyüme bütçe üstünde" — "Nominal +%31,4" — "→ Kanal kırılımını koru"
  green "Yenileme tutma güçlü" — "Prim bazında %88" — "→ Aynı tempoda sürdür"
  green "Uyum bayrakları temiz" — "Yaptırım eşleşmesi 0" — "→ İzlemeye devam"
Right card (1/3 width) "Ürün Dağılımı": horizontal bar list with code + name and value: "438 İşveren Sor. 412 M ₺", "437 Üçüncü Şahıs 351 M ₺", "474 D&O 188 M ₺", "450 Ürün Sor. 142 M ₺", "415 Mesleki Sor. 109 M ₺", "470 FI PI 47 M ₺", "Diğer 35 M ₺", each with a small growth chip.

Row 3 — three cards: "Aylık Üretim" (navy column chart Oca–Eyl 2026 with a lighter ghost series for 2025 behind it; the September column hatched with a note "Eylül kısmi — kesildi"), "Teklif Hunisi" (horizontal funnel: "Teklif 1.842 → Fiyatlanan 1.311 → Bağlanan 851", conversion percents between steps), "Önemli Teklifler (>100.000 ₺)" (compact list of 5 rows: insured placeholder like "ABC Kimya A.Ş.", product code chip, premium, status pill "Açık / Bağlandı / Kaybedildi").
```

### Ekran 2 — Üretim & Performans

```
SCREEN: "Üretim & Performans" (active sidebar item). Role: Yönetici.

Page title "Üretim & Performans", subtitle "Ürün × kanal × bölge · 2026 Oca–Eyl". Right: a segmented control "Bölge bazlı | UW portföyü | Masa toplamı" (Masa toplamı selected) and a button "Excel'e aktar".

Row 1 — four KPI cards: "Brüt Prim" "1.284 M ₺" (▲ %31,4), "Poliçe Adedi" "6.912" (▲ %4,2), "Ortalama Prim — Yeni İş" "214.300 ₺", "Ortalama Prim — Yenileme" "176.800 ₺". Small grey footnote under the row: "Yeni iş ve yenileme ortalama primi ayrı gösterilir; karışımı fiyat trendini gizler."

Row 2 — left large card "Ürün Bazlı Büyüme" : a clean table with columns "Ürün", "2025", "2026", "Değişim", "Pay", "Mini trend". Rows: 438 İşveren Sorumluluk, 437 Üçüncü Şahıs, 474 Yöneticiler (D&O), 450 Ürün Sorumluluk, 415 Mesleki Sorumluluk, 470 Finansal Kurumlar PI, Diğer. Change column uses green/red chips, share column shows small inline bars, last column tiny sparklines. Bold total row "Toplam".
Right card "Bütçe Gerçekleşme": a large ring gauge "%78,6" with labels "Bütçe 1.634 M ₺ · Gerçekleşen 1.284 M ₺", below it a small line chart "Yıl sonu tahmini" with a dashed projection to "%104" and the tag "AI+DATA".

Row 3 — two cards side by side:
"En Çok Büyüyen Acenteler" — top 5 list with rank number, placeholder agency names ("Acente A Sigorta Aracılık", "Acente B", …), KIO chip on some rows, premium and green growth chip.
"En Çok Küçülen Acenteler" — same layout with red chips.

Row 4 — full-width card "Kanal Kırılımı": a 100% stacked horizontal bar for "Acente | Broker | KSM (Direkt)" with a second thin bar under it showing "KIO %42 | KIO dışı %58", and a short grey caption "Broker daralması ile acente büyümesi ayrı izlenir."
```

### Ekran 3 — Bölge Kartı

```
SCREEN: "Bölge Kartı" (active sidebar item). Role switch shows "UW" — the user sees only his own regions.

Page title "Bölge Kartı", subtitle "Erişiminiz: MRM · GAB · ADZ". A horizontal tab row of region pills under the title: "MRM" (selected), "GAB", "ADZ"; greyed and locked pills "OAB 🔒", "Anadolu 1 🔒" to show restricted access.

Row 1 — left profile card "MRM — Marmara Bölge": avatar-style initials circles with roles: "Bölge Yöneticisi · B. Y." and a row of 4 small initial circles labeled "Satış Ekibi (4)". Under it three mini stats: "Brüt Prim 412 M ₺", "Büyüme ▲ %27,8", "Hit Ratio %51,0".
Right card "UW Notları" (source badge "GİRİŞ" in grey): a vertical timeline of 3 short notes, each with initials, date and one line of text, e.g. "12.10 · Büyük TPL yenilemesi için acenteyle görüşüldü", "05.10 · Bölgede inşaat TPL talebi artıyor", plus a text input "Not ekle…" with a small button "Kaydet". Caption: "Kim / ne zaman yazdı kaydı tutulur."

Row 2 — three equal cards, each a ranked top-10 table with columns "#", "Acente", "Prim", "Değişim":
"En İyi 10 Acente — Kurumsal (tüm branşlar)"
"En İyi 10 Acente — KIO"
"En İyi 10 Acente — Sorumluluk"
Use placeholder agency names (Acente A … Acente J), Turkish currency formatting, green/red change chips, KIO chip where relevant.

Row 3 — full-width card "Bölgede Üretim Trendi": a slim area chart 2025 vs 2026 by month, with product-code toggle chips "Tümü 438 437 474 450 415 470".
```

### Ekran 4 — Yenileme & Kayıp

```
SCREEN: "Yenileme & Kayıp" (active sidebar item). Role: Yönetici.

Page title "Yenileme & Kayıp", subtitle "Vadesi gelen poliçeler ve kaçırılan büyük işler". Right: button "Liste indir".

Row 1 — three status cards colored by urgency (soft tinted backgrounds, bold left border):
green "60 gün içinde" — "214 poliçe · 186,4 M ₺"
amber "30 gün içinde" — "97 poliçe · 84,1 M ₺"
red "15 gün içinde" — "38 poliçe · 41,7 M ₺ · 11'i işlem bekliyor"
A fourth neutral card "Yenileme tutma (YTD)" — "%88 prim · %83 adet".

Row 2 — wide card "Yenileme Listesi" with a search box "Sigortalı, acente veya poliçe ara…" and pagination at the bottom ("1 / 22 · 214 kayıt"). Table columns: "Vade", "Kalan gün" (colored dot green/amber/red), "Sigortalı", "Ürün", "Acente", "Prim", "Hasar geçmişi" (small chip "Temiz" / "1 büyük hasar" / "Frekans"), "Tetikleyici" (chip "Olay" or "Claims-made"), "Retro tarih" (with a small red warning icon on one row labeled "kesinti"), "Durum" (pill "Teklif verildi / Bekliyor / Yenilendi").

Row 3 — two cards:
left "Kaçırılan Büyük İşler (>100.000 ₺)" — table "Sigortalı", "Ürün", "Prim", "Rakip", "Sebep" (chips: "Fiyat", "Acente transferi", "Mevcut şirket yeniledi"), source badge "GİRİŞ".
right "Kayıp Sebebi Dağılımı" — a clean donut chart with three segments labeled directly: "Fiyat %46", "Acente transferi %31", "Mevcut şirket %23", and below it "Rakip Top 5" as a mini bar list with placeholder names "Rakip A … Rakip E".
```

### Ekran 5 — UW Asistanı (değerlendirme akışı)

```
SCREEN: "UW Asistanı" (active sidebar item, under ÇALIŞMA MASASI). Role: UW.

Page title "UW Asistanı", subtitle "Yeni risk değerlendirmesi · Teklif No 2026-438-0147". Right: buttons "Taslağı kaydet" (secondary) and "Teklif belgesi oluştur" (primary red).

Under the header, a horizontal 5-step stepper with numbered circles connected by a line: "1 Girdi" (completed, check mark), "2 Kıyas verisi" (completed), "3 Risk analizi" (current, red ring), "4 Reasürans" (upcoming), "5 Çıktı" (upcoming).

Main area split in two columns:

LEFT column (40%) — card "Risk Bilgileri" (badge "GİRİŞ"): product selector showing "438 · İşveren Sorumluluk"; form fields with filled values: "Sigortalı: ABC Kimya A.Ş.", "Faaliyet (NACE): 20.14 Kimyasal üretim", "Çalışan sayısı: 420", "Yıllık ciro: 850 M ₺", "Limit (olay başı / yıllık): 25 M ₺ / 50 M ₺", "Muafiyet: 50.000 ₺", "Coğrafi kapsam: Türkiye". Below it a compact card "Sigortalı 360°" (badge "DATA"): "Önceki poliçe 3 yıl · Hasar 1 adet 1,2 M ₺ · Diğer branş: Yangın, Nakliyat".

RIGHT column (60%) stacked cards:
- "Kıyas Verisi" (badge "DATA"): three mini stat tiles "Baz prim (2025 bağlanan) 412 ₺ / çalışan", "H/P — 438 UW yılı %38,4", "Teklif / Poliçe primi 1,12×", and one tile "Risk primi (tarife) 168.000 ₺" with badge "TOOL".
- "Uyarılar" (badge "KURAL"): three alert rows with icons: red "Limit UW yetkisini aşıyor — onaya düşecek", amber "Faaliyet yüksek tehlike sınıfı (4/5)", green "Yaptırım taraması temiz".
- "Hasar Senaryoları" (badge "AI"): two scenario rows each with a short title and an estimated severity range, e.g. "Kimyasal maruziyet — meslek hastalığı · 2–6 M ₺", "Patlama — çoklu yaralanma · 10–25 M ₺".
- "Örnek İstisna Önerisi" (badge "AI"): two chips "Asbest istisnası", "Kademeli maruziyet sınırlaması".
Bottom of right column: a preview card "Örnek Fiyatlama Önerisi" (badge "AI+DATA") with a big number "186.500 ₺" and a range bar "164.000 – 205.000 ₺", plus grey caption "Kıyas verisine dayanır; nihai fiyat UW kararıdır."

A slim right-edge drawer handle labeled "Asistan" (collapsed AI chat panel), no mascot.
```

### Ekran 6 — Fakültatif İşler

```
SCREEN: "Fakültatif İşler" (active sidebar item). Role: UW.

Page title "Fakültatif İşler", subtitle "İhtiyari plasman takibi ve belge üretimi". Right: primary button "Yeni ihtiyari çalışma".

Row 1 — four KPI cards: "Açık plasman 9", "Fakültatife giden prim 38,2 M ₺", "Alınan komisyon %22,5", "Treaty kullanım oranı %71" (with a thin capacity bar).

Row 2 — wide card "İhtiyari İşler Takip" (badge "DATA"): a kanban-style board with four columns and small cards: "Hazırlık (3)", "Reasüröre gönderildi (4)", "Teyit alındı (1)", "Poliçeleşti (6)". Each card shows insured placeholder, product code chip, limit, our retention vs fac share as a tiny split bar, and the days in stage.

Row 3 — two cards:
left "Fiyat Hesaplama" (badge "TOOL"): a compact calculator with fields "Toplam limit 100 M ₺", "Treaty kapasitesi 60 M ₺", "Fakültatif ihtiyacı 40 M ₺", "Brüt prim 1,25 M ₺", "Reasürör komisyonu %22,5" and a result "Fakültatife devredilen net prim 387.500 ₺".
right "Belge Üret" (badge "AI+TOOL"): three large option tiles with document icons: "PC Teklif (Word)", "PC Poliçe (Word)", "Reasürör maili (taslak)"; under them a document preview thumbnail of a clean Word page titled "PLASMAN TEKLİFİ".
```

### Ekran 7 — Portföy Hasar

```
SCREEN: "Portföy Hasar" (active sidebar item). Role: Yönetici.

Page title "Portföy Hasar", subtitle "UW yılı bazında · muallak dahil · kesit 30.09.2026". A grey info note under the header: "2026 UW yılı olgunlaşmadı; diğer yıllarla yan yana okunmamalı."

Row 1 — four KPI cards: "Hasar / Kazanılmış Prim %41,8", "Büyük hasar hariç %27,3" (note "12 dosya ayrıldı"), "Hasar frekansı %3,9", "Muallak 214 M ₺" (note "dava dosyası %36").

Row 2 — left large card "UW Yılına Göre Hasar Oranı": grouped bars for UW years 2022, 2023, 2024, 2025, 2026 per product (438, 437, 450, 474), with a dashed target line labeled "hedef %55"; the 2026 bars are hatched with label "olgunlaşmadı".
Right card "Gelişim Üçgeni (özet)": a small heat-map table of incurred loss ratio development by UW year (rows 2022–2026) and development month (12, 24, 36, 48), cells shaded from light to deep navy.

Row 3 — two cards:
"Büyük Hasarlar (>1 M ₺)" table: "Dosya", "Ürün", "Hasar tarihi", "Ödenen", "Muallak", "Durum" (pill "Dava / Müzakere / Kapandı").
"Standart vs DERK'siz Görünüm": two side-by-side mini bar comparisons per product with caption "DERK etkisi ayrı gösterilir".
```

### Ekran 8 — Kişisel Kokpit (UW)

```
SCREEN: "Kişisel Kokpit" (active sidebar item). Role switch shows "UW". Personal view for one underwriter.

Page title "Kişisel Kokpit", subtitle "Kendi portföyünüz · ekip kıyası anonim medyan ile". No leaderboard, no ranking anywhere.

Row 1 — five KPI cards, each with a thin grey marker showing "ekip medyanı":
"Brüt Prim (YTD) 214 M ₺" with progress bar "hedefin %81'i";
"Hit Ratio %52,4" (marker "ekip medyanı %46");
"TAT medyan 4,1 gün" (marker "SLA 7 gün");
"Yenileme tutma %91";
"Teklif → Poliçe prim farkı −%6,2" (note "pazarlıkta verilen indirim").

Row 2 — wide card "Bugün Bakmanız Gerekenler" — a clean to-do style list grouped in three sections with counts: "Bekleyen teklifler (7)", "Kapanmamış subjectivity / imzalı form (4)", "15 gün içinde yenileme (3)". Each row has a colored dot, short text, a due date and a small "Aç" link.
Right narrow card "Kaçırdığım İşler — Sebep": small donut "Fiyat %50 · Transfer %33 · Mevcut şirket %17".

Row 3 — two cards: "Portföy Kalitesi" (small line chart of own UW-year loss ratio 2023–2026 with 2026 dashed and labeled "olgunlaşmadı") and "Ürün Karması" (horizontal bars by product code for this underwriter).
```

### Ekran 9 — UW Kütüphanesi ve Wording Karşılaştırma

```
SCREEN: "UW Kütüphanesi" (active sidebar item, under BİLGİ). Role: UW.

Page title "UW Kütüphanesi", subtitle "Wording, kılavuz ve arşiv". A large search bar across the top: "Kloz, şart veya soru ara… (örn. 'retroaktif tarih')".

Left column (35%): category list with counts and icons: "Genel Şartlar (14)", "Uluslararası Wording'ler (22)", "Wording Değerlendirme Kiti (6)", "UW Guideline (8)", "UW Rehberleri (11)", "Soru Formları (9)", "Sık Sorulan Sorular (41)", "Faaliyet Raporları (12)", "Sunumlar (18)". Below: "Son eklenenler" with 3 document rows (file icon, title, date).

Right column (65%): a tool card "Wording Karşılaştırma" (badge "AI"): two upload slots side by side labeled "Standart şart: Sompo 437 GŞ" and "Gelen wording: Broker_TPL_2026.docx", and below a diff-style result list with three rows, each with a colored tag: red "Kapsam genişlemesi — Kirlilik (ani ve beklenmedik) dahil edilmiş", amber "Sublimit yok — Ürün geri çağırma", green "Standart ile uyumlu — Yargı yetkisi Türkiye". A button "Değerlendirme raporu oluştur".
```

### Ekran 10 — Sektör / TSB

```
SCREEN: "Sektör / TSB" (active sidebar item). Role: Yönetici.

Page title "Sektör / TSB", subtitle "TSB gelir tablosu ve üretim verisi · 2026 H1". Toggle at right: "Standart | DERK'siz".

Row 1 — four KPI cards: "Sorumluluk pazar payı %6,8", "Sektör sırası 5.", "Net hasar oranı %61,2 (sektör %74,5)", "Net komisyon / NEP %11,8 (sektör %16,9)".

Row 2 — left card "Branş Bazında Pazar Payı": a table with TSB codes and names (719 İşveren Sorumluluk, 720 Üçüncü Şahıs Sorumluluk, 733 Mesleki Sorumluluk, 771 Ürün Sorumluluk), columns "Sektör prim", "Bizim prim", "Pay", "Pay değişimi (pp)".
Right card "Kârlılık × Büyüklük": scatter plot of top 15 companies (placeholder names "Şirket A … Şirket O"), with our company highlighted in red and labeled "Biz".

Row 3 — full-width grey note box: "Kapsam uyarısı: TSB verisi şirket genelidir; masa kitabı ile aynı kapsamda değildir. Branş kodu eşlemesi ayrı tabloda tutulur."
```

---

## 3. Görsel çıktı geldiğinde kontrol listesi

Her görselde şunlara bakın. Takılan maddeyi ChatGPT'ye düzeltme talimatı olarak yazın.

- [ ] Kırmızı yalnız uyarı, aktif menü ve ana butonda mı? (Her yer kırmızıysa: `Use red only for alerts and the primary button.`)
- [ ] Her kartta dönem ve kaynak rozeti var mı?
- [ ] Türkçe karakterler doğru mu? (ş, ğ, ı, İ)
- [ ] Oran değişimleri **pp** ile mi yazılmış (hit ratio −3,1 pp)?
- [ ] Kişisel Kokpit'te sıralama veya lider tablosu çıkmamış mı?
- [ ] Sol menü bütün ekranlarda aynı mı?

## 4. Sonraki adım

Görselleri ürettikten sonra beğendiğiniz ve beğenmediğiniz ekranları buraya iletin. Kodlamaya geçerken tasarım kararlarını bu görsellere göre kesinleştiririz.
