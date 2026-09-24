---
# ─────────────────────────────────────────────────────────────
# FRONT MATTER — bu blok zorunlu. Üç tireden üç tireye kadar.
# ─────────────────────────────────────────────────────────────

title: "EF Core'da N+1 sorgusu: 40 saniyeden 300 ms'e"
# Başlık. Tırnak içinde olsun (iki nokta, tire vs. sorun çıkarmasın).

date: 2026-09-25 09:00:00 +0300
# Yayın tarihi. +0300 = Türkiye. İleri tarih verirsen o tarihe kadar görünmez.
# Boş bırakabilirsin, ben doldururum.

categories: [.NET, Performans]
# En fazla 2 seviye. Birincisi ana kategori, ikincisi alt kategori.

tags: [tr, dotnet, ef-core, performans]
# Küçük harf. İlk etiket dil olmalı: tr veya en.

description: EF Core'da lazy loading yüzünden oluşan N+1 sorgusunu nasıl yakaladım, Include ve projection ile nasıl çözdüm.
# 1-2 cümle. Ana sayfada ve Google sonuçlarında bu görünür.

# ── Aşağıdakiler opsiyonel, gerekmiyorsa sil ──
# pin: true              # ana sayfada en üste sabitler
# math: true             # LaTeX formül yazacaksan
# mermaid: true          # diyagram çizeceksen
# image:
#   path: /assets/img/posts/ef-core-n1.png
#   alt: Sorgu profilinin ekran görüntüsü
---

<!-- BURADAN AŞAĞISI YAZININ KENDİSİ.
     H1 (#) KULLANMA — başlık zaten yukarıda. ## ile başla. -->

Sipariş listesi sayfası 40 saniyede açılıyordu. Kod temiz görünüyordu, veri az görünüyordu,
ama sayfa ölüydü. Sorun EF Core'un arkamdan attığı sorgulardaydı.

## Ortam

.NET yazıları hızlı eskiyor — sürümleri yaz:

- .NET 8.0
- EF Core 8.0.10
- SQL Server 2022

## Problem

Ne bozuktu, nasıl fark ettin. Somut sayı ver:

| Ölçüm | Değer |
| --- | --- |
| Sayfa yüklenme | 40,2 sn |
| SQL sorgu sayısı | 1 + 2.000 |
| Dönen satır | 2.000 |

## Neden oluyor

Kodun kendisi masum görünüyor:

```csharp
var orders = await _db.Orders
    .Where(o => o.CreatedAt >= from)
    .ToListAsync();

foreach (var order in orders)
{
    // Her döngüde AYRI bir SQL sorgusu gidiyor
    total += order.Customer.DiscountRate;
}
```
{: file="Services/OrderReportService.cs" }

<!-- Kod bloğunda dil belirt: csharp, json, sql, bash, xml, yaml...
     Dosya adı vermek istersen kod bloğunun hemen altına {: file="..." } ekle. -->

> Lazy loading açıkken bu hatayı derleyici de, code review de yakalamaz.
> Sorgu sayısı veri büyüdükçe artar — testte 10 kayıtla sorun görünmez.
{: .prompt-warning }

<!-- Kutu tipleri: prompt-tip (yeşil), prompt-info (mavi),
     prompt-warning (sarı), prompt-danger (kırmızı) -->

## Denedim, olmadı

Önce ne denedin ve neden yetmedi. Bu bölüm yazıyı değerli yapar — okuyan
aynı çıkmaza girmez.

```csharp
// İlk denemem: Include. N+1 gitti ama sorgu 8 saniye sürdü.
var orders = await _db.Orders
    .Include(o => o.Customer)
    .ToListAsync();
```

Sebep: `Include` tüm `Customer` kolonlarını çekiyor, oysa bana tek alan lazım.

## Çözüm

```csharp
var orders = await _db.Orders
    .Where(o => o.CreatedAt >= from)
    .Select(o => new OrderSummary(
        o.Id,
        o.Total,
        o.Customer.DiscountRate))   // tek sorguya JOIN olarak iniyor
    .AsNoTracking()
    .ToListAsync();
```
{: file="Services/OrderReportService.cs" }

Üç değişiklik:

1. **Projection** — sadece ihtiyacım olan 3 alan
2. **`AsNoTracking()`** — salt okunur sorguda change tracker'a gerek yok
3. Navigation property'ye `Select` içinde eriştim — EF bunu JOIN'e çeviriyor

## Sonuç

| Ölçüm | Önce | Sonra |
| --- | --- | --- |
| Süre | 40,2 sn | 0,3 sn |
| Sorgu sayısı | 2.001 | 1 |
| Bellek | 180 MB | 12 MB |

## Ne zaman işe yaramaz

Dürüst ol — çözümün sınırını yaz:

- Yazma işlemi yapacaksan `AsNoTracking()` kullanma, değişiklikler kaydedilmez
- Çok fazla `Select` iç içe geçerse SQL okunmaz hale gelir
- `Include` her zaman kötü değil; tüm entity gerçekten lazımsa doğru araç

> Sorgu sayısını görmek için EF Core loglarını aç:
> `optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information)`
{: .prompt-tip }

## Kaynaklar

- [EF Core — Efficient Querying](https://learn.microsoft.com/ef/core/performance/efficient-querying)
