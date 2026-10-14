# Sorumluluk (CFL) UW Dashboard — Yangın paketinden uyarlama analizi

**Hazırlanma:** 14.10.2026 · **Girdi:** `uw-dashboard-paket.zip` (Yangın / Endüstriyel Riskler) + CFL yönetim/rapor metodolojiniz (kokpit, periyodik performans raporu, sektör kârlılık paneli, ürün UW risk kartı, kefalet UW)

> Not: Diğer sohbetlerinizin içeriğine doğrudan erişimim yok. "Kullanım alışkanlıkları" kısmını hesabınızda tanımlı CFL skill'lerinden (metodoloji, kod listeleri, ekip yapısı, rapor standartları) çıkardım. Buradaki her CFL kuralının kaynağı o dosyalardır.

---

## 1. Yönetici özeti

**Sonuç:** Yangın paketinin **altyapısı ve 10 ölçüm kuralı aynen alınmalı**. Buna karşılık **veri modeli, KPI seti ve değerlendirme kriterleri baştan Sorumluluk'a göre kurulmalı**. Yangın panosu "bölge × branş × tarife" mantığıyla çalışıyor. Sorumluluk masası ise "ürün × kanal × limit × teklif hunisi × long-tail hasar" ekseninde yönetiliyor. Bu ekran kopyalanırsa sayılar doğru görünür ama yanlış soruya cevap verir.

| # | Ana bulgu | Etki |
|---|---|---|
| 1 | Yangın'daki ana boyut **bölge**. Sizinki **ürün kodu** (415/437/438/450/470/474/Kefalet) ve **kanal** (Acente / Broker / KSM × KIO) | Veri modeli ve `uw_kapsam` tablosu değişir |
| 2 | Yangın'da "fiyat yeterliliği = fiyat ÷ tarife". Sorumlulukta bağlayıcı bir tarife yok. Exposure bazı **ciro (‰), çalışan sayısı ve limit (ROL)** | Fiyat KPI'ı yeniden tanımlanmalı. En büyük birim tuzağı burada |
| 3 | Pakette **teklif hunisi yok**. Sizin en çok kullandığınız metrikler bunlar: hit ratio (unique sigortalı), TAT medyan, SLA, kaçan iş | Panoya eklenecek en değerli modül |
| 4 | Sorumlulukta hasar long-tail. Takvim yılı H/P **yanıltır**. DERK ve muallak etkisi büyük (771 / 733 örnekleriniz) | Hasar panosu kaza ya da UW yılı bazlı olmalı, DERK ayrı gösterilmeli |
| 5 | Pakette büyüme hedefi `TÜFE × 1,5` ile kurulu. Sizin metodolojinizde **reel büyüme = (1+n)/(1+TÜFE) − 1** | Bu çelişki giderilmezse dashboard ile çeyrek raporu farklı sayı verir |
| 6 | Uyum bayrakları farklı: ABD/Kanada exposure, yaptırım, treaty istisnası, yetki aşımı, retro date kesintisi | Kural 5'teki ayrı kolon disiplini daha da kritik hale gelir |
| 7 | Kefalet, sorumluluk mantığıyla ölçülemez (kredi riski, asıl borçlu bazında kümül) | Ayrı pano olmalı, Faz 3'e bırakılmalı |

---

## 2. Paketten olduğu gibi alınacaklar

Bu kısımlar branştan bağımsız. Hiçbirini yeniden icat etmeyin.

| Unsur | Neden aynen |
|---|---|
| Statik HTML + tek köprü (`api/db.js`) + Supabase, sıfır bağımlılık | Bakım ve devir kolay, maliyet sıfıra yakın |
| Kişi başına parola, imzalı çerez, **kapsamın oturumdan okunması** | Yangın'daki en ciddi açık. İlk günden doğru kurulmalı |
| 10 ölçüm kuralı (birim adda, Σ/Σ havuzlama, dönem kesimi, türetilmiş dipnot, eksen saflığı, sıra no ≠ kategori, kapsam farkı, sunucu yetkisi, ölçek, model maliyeti) | Hepsi Sorumlulukta da geçerli. Bazıları daha ağır basıyor (bkz. §5) |
| Tuzak yöntemiyle test disiplini, kalabalık stub, çağrı sayısı testi | Aynen |
| "Genel bakış" + 8 kriterli değerlendirme panosu, eşiklerin `AYAR` bloğunda tutulması | Yapı aynı kalır, kriterler değişir (bkz. §4) |
| AI yorumu: kullanıcı istediğinde çağrılır, bulgu ≤ 20, hata yutulmaz | Aynen |

---

## 3. Veri modeli revizyonu

### 3.1 Boyutlar: Yangın → Sorumluluk

| Yangın | Sorumluluk karşılığı | Not |
|---|---|---|
| `bolge` (12 bölge) | `urun_kod` + `kanal` + `kio` + `bolge` | Bölge kodlarınız: MRM, GAB, ADZ, OAB, Anadolu 1/2, Güney Anadolu, **KSM** |
| `brans` (Yangın / Deprem / KK / MK) | `urun_kod`: 415 PI · 437 TPL/CGL · 438 EL · 450 PL · 470 FI PI · 474 D&O · Diğer (417, 421, 445, 446, 449, 452, 456, 459) · Kefalet | Kod ve ad her zaman birlikte gösterilir |
| `tarife` (fiyat/baz tarife) | `exposure_tip` + `exposure_deger` (ciro / çalışan / ücret / limit) + `limit` | Bkz. §3.3 |
| `risk_sinifi` (Düşük/Orta/Yüksek) | `hazard_grade` (faaliyet/NACE bazlı 1–5) | Ürün UW risk kartındaki Safety Criticality ölçeğiyle uyumlu olabilir |
| `uyum_bayragi` (75/25 İHLAL) | Çoklu bayrak tablosu (bkz. §3.4) | Tek kolon yetmez |

### 3.2 Minimum tablolar (paketteki 4 tablonun Sorumluluk versiyonu)

```sql
-- 1) uw  → aynı (id, ad, unvan, rol, aktif, parola_hash)

-- 2) uw_kapsam  → boyut DEĞİŞİR: bölge değil ürün (+ isteğe bağlı kanal)
create table uw_kapsam (
  uw_id     text references uw(id),
  urun_kod  text not null,          -- '438','437','450','415','470','474','KEF'
  primary key (uw_id, urun_kod)
);

-- 3) uretim  (aylık, ürün × kanal × bölge)
create table uretim (
  yil int, ay int, urun_kod text, kanal text, kio boolean, bolge text,
  yeni_yenileme text,               -- 'Y' | 'YN'  (ortalama primi ayrı raporlamak için)
  gwp_tl numeric, police_adet int,
  kom_yuzde numeric,                -- BİRİM ADDA: zaten yüzde
  primary key (yil, ay, urun_kod, kanal, kio, bolge, yeni_yenileme)
);

-- 4) teklif  (YENİ — Yangın paketinde yok, sizin en kritik veriniz)
create table teklif (
  akis_no text, teklif_no text primary key, police_no text,   -- police_no='0' → bağlanmamış
  urun_kod text, kanal text, kio boolean, bolge text, acente text,
  sigortali_norm text,              -- hit ratio eşleştirme anahtarı (Akış No yoksa)
  uw_id text, giris_tarih date, yanit_tarih date,
  durum text,                       -- Bağlandı / Kaybedildi / Reddedildi / Açık
  kayip_rakip text, kayip_tip text, -- transfer / fiyat / mevcut şirket yenilemesi
  teklif_prim_tl numeric
);

-- 5) police  (yenileme + portföy, poliçe satırı)
create table police (
  police_no text primary key, urun_kod text, kanal text, kio boolean, bolge text,
  sigortali text, acente text, baslangic date, bitis date,
  gwp_tl numeric, doviz text, gwp_doviz numeric, kur_tarihi date,
  tetikleyici text,                 -- 'OCCURRENCE' | 'CLAIMS_MADE'
  retro_tarih date,
  limit_olay numeric, limit_yillik numeric, muafiyet numeric,
  katman text, attachment numeric,  -- primary / excess; excess ise attachment
  sirket_pay numeric,               -- KESİR 0–1 (koasürans / fronting payımız)
  exposure_tip text, exposure_deger numeric,
  hazard_grade int, faaliyet_kod text, cografi_kapsam text,  -- 'TR' | 'WW_EXC_USCAN' | 'WW'
  fakultatif boolean
);

-- 6) bayrak  (uyum / referral bayrakları AYRI tablo — kural 5)
create table bayrak (police_no text, bayrak text, kaynak text, primary key (police_no, bayrak));

-- 7) hasar  (Faz 2)
create table hasar (
  dosya_no text primary key, police_no text, urun_kod text,
  hasar_tarih date, ihbar_tarih date, kaza_yili int, uw_yili int,
  odenen_tl numeric, muallak_tl numeric, rucu_tl numeric, dava boolean, durum text
);
```

**İki tasarım kararı (Yangın'dan alınan ders, Sorumlulukta daha önemli):**
1. `sirket_pay` ve `limit` kolonlarının **%100 mü, pay mı** olduğu kolon adında yazmalı. Koasüranslı ya da fronting işlerde %100 prim ile pay primi karışırsa GWP 2–5 kat şişer.
2. Döviz poliçeler için TL karşılığı **hangi kurla** (tanzim mi, dönem sonu mu) hesaplandığı tek kural olmalı ve dipnotta yazmalı. D&O, PI ve FI'da USD/EUR payı yüksekse kur etkisi "büyüme" gibi görünür.

### 3.3 Fiyat yeterliliği: tarifenin yerine ne konur?

Yangın'da bağlayıcı bir referans fiyat (tarife) var. Sorumlulukta yok. Üç seçenek:

| Seçenek | Ne gerektirir | Öneri |
|---|---|---|
| A. Teknik fiyat / bağlanan fiyat (rating tool çıktısı) | Her poliçe için teknik prim kaydı | **En doğrusu.** Rating tool çıktısı sistemde saklanıyorsa bunu kullanın |
| B. Exposure oranı trendi: prim/ciro (‰), prim/çalışan, ROL = prim/limit, aynı ürün ve aynı limit bandında YoY | `exposure_deger`, `limit` | **Gerçekçi MVP.** Mutlak yeterliliği değil, yönü ölçer |
| C. Yenileme fiyat değişimi (like-for-like rate change) | Aynı poliçenin geçen yıl ve bu yılki primi, limit ve exposure değişimi | A ile birlikte kullanılmalı. Reasüröre de bu sunulur |

**Öneri:** MVP'de B + C, veri olgunlaşınca A. B'de havuzlama kuralı **ürün ve limit bandı içinde** uygulanmalı. 1 M TL limitli PI ile 50 M USD limitli D&O'yu aynı ROL havuzuna koymak, Yangın'daki 76× hatasının Sorumluluk versiyonudur.

### 3.4 Bayrak seti (her biri ayrı satır, risk eksenine girmez)

| Bayrak | Kaynak | Neden önemli |
|---|---|---|
| `ABD_KANADA` | coğrafi kapsam / ihracat | Treaty istisnası, fakültatif ihtiyacı |
| `YAPTIRIM` | sanctions taraması | Yazılamaz ya da özel onay gerekir |
| `TREATY_DISI` | istisna listesi (asbest, PFAS, siber TPL içinde, ilaç/tıbbi cihaz PL vb.) | Silent exposure |
| `YETKI_ASIMI` | limit/prim > UW yetki matrisi | Referral takibi |
| `RETRO_KESINTI` | claims-made yenilemede retro tarih değişmiş | Kapsam boşluğu, E&O riski |
| `FAKULTATIF` | treaty limiti aşımı | Kapasite ve maliyet |
| `ECL/AGG_TUKENMIS` | yıllık agregatın tükenme oranı | Agregat erimesi |

---

## 4. Pano envanteri: Sorumluluk versiyonu

| Öncelik | Pano | İçerik | Yangın'da var mı |
|---|---|---|---|
| **1** | Genel bakış | 4 KPI + 8 kriterli değerlendirme (aşağıda) | Var (kriterler değişir) |
| **2** | Üretim | GWP, adet, ortalama prim (**yeni ve yenileme ayrı**), ürün × kanal × KIO, **Rate/Volume/Mix ayrıştırması**, reel büyüme | Kısmen |
| **3** | Teklif hunisi | Hit ratio (unique sigortalı, T∩P / T), ürün ve acente kırılımı, TAT medyan + ortalama, SLA (<7 gün; KIO 2 iş günü), açık teklif yaşlandırma | **Yok** |
| **4** | Kaçan iş | Rakip Top-5, kayıp tipi (transfer / fiyat / mevcut şirket yenilemesi) | **Yok** |
| **5** | Yenileme | 90/60/30 gün vade havuzu, retention (adet ve prim), rate change, claims-made retro kontrolü | Var (içerik değişir) |
| **6** | Kanal / KIO | Acente / Broker / KSM, KIO payı (pp), **acente HHI** + Top-3 kaybı senaryosu, ±%30 bölge anomalisi | **Yok** |
| **7** | Limit ve kümül | Limit bandı dağılımı, en büyük 20 limit, treaty kapasite kullanımı, fakültatif listesi, excess katmanlarda attachment | **Yok** |
| **8** | Hasar (Faz 2) | Kaza ve UW yılı bazında H/P, frekans/şiddet, büyük hasar listesi, muallak gelişimi, dava oranı | Var (yöntem değişir) |
| **9** | Pazar (TSB) | Sektör kârlılık panelinizdeki 719 / 720 / 733 / 771 vb. kodlarla pay ve LR, **standart ve DERK'siz** | Var (kapsam uyarısı şart) |
| **10** | Ekip iş yükü | UW bazlı teklif hacmi, medyan TAT, açık dosya. **Kişi yorumu yok, yalnız veri** | "Ekip/izin" var |

### Genel bakış: 8 değerlendirme kriteri (Sorumluluk)

Eşikler `AYAR` bloğunda tutulur, koda gömülmez:

| # | Kriter | Ölçüm | Öneri eşik (yönetici günceller) |
|---|---|---|---|
| 1 | Reel büyüme | (1+nominal)/(1+TÜFE) − 1 | < 0 → kırmızı · 0 ile +%5 arası → sarı |
| 2 | Büyümenin kaynağı | Adet değişimi ile prim değişiminin farkı (rate/volume) | Adet ≤ %0 ve prim > TÜFE ise "büyüme fiyat kaynaklı" uyarısı |
| 3 | Hit ratio | Unique sigortalı bazında, YoY **pp** | −3 pp altı → sarı · −5 pp altı → kırmızı |
| 4 | SLA / TAT | Medyan TAT, SLA dışı oranı | Medyan > 7 gün ya da KIO SLA ihlali > %10 |
| 5 | Fiyat yönü | Like-for-like rate change (ürün ve limit bandı içinde havuzlanmış) | < TÜFE → sarı · < 0 → kırmızı |
| 6 | Kanal yoğunlaşması | Acente HHI + Top-3 payı | HHI > 1.500 sarı · > 2.500 kırmızı |
| 7 | Limit / kapasite | Treaty limiti aşan ya da fakültatif gerektiren poliçe sayısı ve primi, ABD/Kanada exposure payı | Kurum iştahına göre |
| 8 | Veri tazeliği ve kalitesi | Son dolu ay + kritik alan null oranı | Null > %5 → sarı (metodolojinizdeki eşik) |

Faz 2'de 9. kriter eklenir: **olgunlaşmış UW yılları bazında hasar/prim**. Cari yıl H/P'si değerlendirme kutusuna konmamalı. Long-tail'de ilk yıl H/P'si sistematik olarak düşük görünür ve yanlış güven verir.

---

## 5. Ölçüm kurallarına Sorumluluk ekleri

Yangın'daki 10 kurala ek olarak Sorumluluk için **8 yeni tuzak**:

| # | Tuzak | Örnek hata | Kural |
|---|---|---|---|
| S1 | **Hit ratio adet bazında hesaplanır** | Aynı sigortalıya 4 teklif ve 1 poliçe: %25 görünür, doğrusu %100 | Unique sigortalı, T∩P/T. Anahtar: Akış No, yoksa normalize ad. Kullanılan anahtar ekranda yazar |
| S2 | **‰ / % / kesir karışır** | Prim/ciro 0,0012 kesirdir. `yzd` ile basılınca "%0,0" ya da "%0,12" olur | Paketteki `prm()` aynen kullanılır, ROL için `yP()`. Her exposure oranının birimi alan adında yazar |
| S3 | **Oran değişimi % ile yazılır** | Hit ratio %57,8'den %52,6'ya: "−%9 düştü" yazılır | Oranlarda pp, hacimlerde %. Bunun için ayrı yardımcı gerekir: `pp(a, b)` |
| S4 | **Limitler toplanır** | Excess katmanlar ve agregat ile olay başı limitler toplanınca kümül 3–4 kat şişer | Kümül = sigortalı bazında en yüksek **payımıza düşen** katman tavanı. Agregat ile olay başı aynı eksende toplanmaz |
| S5 | **%100 ile pay karışır** | Koasüranslı ve fronting işlerde %100 prim yazılır | `gwp_tl` her zaman şirket payıdır. %100 değer ayrı kolonda tutulur |
| S6 | **Takvim yılı H/P** | Cari yıl H/P %25 çıkar, 3. yılda %85 olur | Kaza ya da UW yılı bazında. Gelişmemiş yıllar taralı gösterilir (paketteki "kısmi ay" mantığı) |
| S7 | **DERK hasar oranına gömülür** | 771'de net LR %285 görünür, DERK hariç tutulduğunda çok farklıdır | Sektör panelinizdeki gibi standart ve DERK'siz iki görünüm. Fark dipnotta yazar |
| S8 | **Dönem kesimi tutar eşiğiyle yapılır** | D&O ve büyük PL yenilemeleri belirli aylarda toplanır. "En yüksek ayın %15'i" kuralı dolu bir ayı kısmi sayabilir | Kesim **veri yükleme tarihi ve kayıt olgunluğu** bazında yapılır. Tutar eşiği yalnız yedek kontrol olur |

Ek olarak, metodolojinizdeki **veri kalitesi düzeltmeleri** `veri.js`'e kural olarak girmeli ve ekranda gösterilmeli:
- `BOLGE` boşsa **KSM** atanır.
- `ACENTE ADI` boşsa aynı Akış No'daki dolu değer kullanılır.
- `POLİÇE NO = 0` olan kayıtlar üretimden hariç, huniye dahil edilir.
- Süre alanında p99 üstü değerler silinmez. Medyan kullanılır ve not düşülür.

---

## 6. İskelet koduna somut revizyonlar

| Dosya | Değişiklik |
|---|---|
| `veri.js` | `pp(a, b)` yardımcısı (puan farkı). `reel(n, tufe)` yardımcısı. `hitRatio(T, P, anahtar)` (unique set kesişimi). `hhi(paylar)`. `rvm()` (rate/volume/mix). Veri kalitesi düzeltme fonksiyonu ve düzeltme sayacı (ekranda gösterilir). `sonDoluAy` → yükleme tarihi bazlı kesim |
| `panel.html` | Değerlendirme kriterleri §4'teki 8 kritere göre yeniden yazılır. Bölge tablosu yerine **ürün × kanal tablosu**. KPI'lar: GWP (nominal ve reel), hit ratio, TAT medyan, yenileme havuzu. 470 satır, Yangın'daki 1.784 satıra yaklaşmadan pano başına ayrı `.js` dosyasına bölünür |
| `AYAR` | `ENFLASYON` + `BUYUME_CARPAN` kaldırılır, yerine `TUFE` gelir (reel büyüme formülü). Yanına `SLA_GUN: 7`, `SLA_KIO_ISGUNU: 2`, `HHI_ORTA: 1500`, `HHI_YUKSEK: 2500`, `NULL_ESIK: 0.05` eklenir |
| `api/db.js` | `kapsamAl` bölge değil **ürün** listesi döndürür. Kefalet kapsamı ayrı tutulur (yalnız Kefalet UW'si ve admin). `pano_ozet` yanına `huni_ozet` ve `yenileme_liste` uçları gelir (sayfalı) |
| `veri-uret.mjs` | Sahte veri Sorumluluk'a göre üretilir. Tuzaklar bilerek konur: aynı sigortalıya çoklu teklif (S1), excess katmanlı sigortalı (S4), koasüranslı iş (S5), USD poliçe, BOLGE ve ACENTE boş satırlar |
| `test/` | Her S-tuzağı için "bilerek boz → kırmızı olmalı" iddiası. Örnek: hit ratio'yu adet bazına çevirince test tam beklenen mesajla kırmızı olmalı |

---

## 7. Faz planı

| Faz | Kapsam | Ön koşul (veri) | Kabul ölçütü |
|---|---|---|---|
| **Faz 0. Veri envanteri** | Hangi alan hangi sistemde, birimi ne, %100 mü pay mı, kur kuralı ne | IT ve aktüerya ile 1–2 toplantı | Her kolon için "birim + kaynak + sahip" tablosu |
| **Faz 1. MVP** | Kimlik doğrulama + Genel bakış + Üretim + Teklif hunisi + Yenileme | Üretim ve teklif extract'ı (çeyrek raporlarınızda kullandığınız Excel'ler yeterli) | Dashboard'daki Q3 rakamları, Q3 çeyrek raporunuzla **birebir** tutar (mutabakat testi) |
| **Faz 2** | Kanal/KIO + Kaçan iş + Limit/kümül + Hasar (UW yılı bazlı) | Poliçe satırında limit ve katman alanları, hasar dosya extract'ı | Kümül hesabı 10 örnek sigortalıda elle teyit edilir |
| **Faz 3** | Kefalet panosu (asıl borçlu ve grup bazlı kümül, teminat vadesi, mali skor), TSB pazar entegrasyonu, AI yorumu | Kefalet portföy extract'ı, TSB dosyası | Kefalet kümülü risk yönetimi raporuyla mutabık |

**Faz 1 kabul ölçütü özellikle önemli.** Dashboard ile çeyrek raporu farklı sayı verirse panoya güven ilk toplantıda kaybolur. Bunun için metrik tanımları (`metodoloji.md`) dashboard koduna **tek kaynak** olarak taşınmalı ve iki ayrı yerde iki ayrı formül yaşamamalı.

---

## 8. Karar gerektiren açık sorular

| # | Soru | Etki | Varsayılan önerim |
|---|---|---|---|
| 1 | Rating tool teknik primi poliçe bazında saklanıyor mu? | Fiyat yeterliliği A seçeneği mümkün mü | Yoksa B + C ile başlanır |
| 2 | Hasar verisine UW yılı ve kaza yılı kırılımıyla erişim var mı? | Faz 2 hasar panosu | Yoksa hasar panosu ertelenir. Takvim yılı H/P **gösterilmez** |
| 3 | Kapsam boyutu yalnız ürün mü, ürün + kanal mı? (ör. KIO işlerini belirli UW'ler mi görür?) | `uw_kapsam` şeması | Ürün bazlı. Admin (siz) tümünü görür |
| 4 | Kefalet bu dashboard'da mı, ayrı mı? | Mimari | Aynı altyapı, ayrı pano, Faz 3 |
| 5 | Döviz poliçelerin TL çevrim kuralı (tanzim kuru mu, dönem sonu mu)? | GWP ve büyüme | Muhasebeyle aynı kural. Dipnotta yazılır |
| 6 | Dashboard ile Yönetim Kokpiti (Planner/Excel) bağlansın mı? | Kapsam şişmesi | **Hayır.** Dashboard ölçer, Kokpit görev yönetir. En fazla değerlendirme kutusunda "Planner'a aktar" satırı üretilir |

---

## 9. Kaçınılması gerekenler

1. **Yangın panosunu "branş adlarını değiştirip" kopyalamak.** Tarife, risk sınıfı ve 75/25 kavramları Sorumlulukta karşılıksız kalır ve ekran boş KPI'larla dolar.
2. **Cari yıl hasar/prim oranını Genel bakışa koymak.** Long-tail'de en yanıltıcı sayıdır.
3. **Tek manşet pazar payı.** 438 ile 474'ün pazar dinamiği zıt yönlerde olabilir (Yangın'daki 701 ile 740 dersinin aynısı). Ürün kırılımı her zaman manşetin yanında durmalı.
4. **UW bazlı iş yükü tablosuna yorum cümlesi eklemek.** Yalnız veri gösterilir (kokpit kuralınız).
5. **Kimlik doğrulamayı "sonra ekleriz" demek.** Yangın ekibinin hâlâ kapatamadığı açık bu. Sorumluluk masasında müşteri ve limit bilgisi en az onlar kadar hassas.
