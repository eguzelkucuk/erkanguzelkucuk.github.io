---
title: "Merhaba dünya"
date: 2026-09-24 10:30:00 +0300
categories: [Meta]
tags: [tr, jekyll]
description: Bu blog neden var ve nasıl kurulu.
---

Yıllardır bir şeyleri yazmayı düşünüyordum. Jira kayıtlarına, Slack mesajlarına ve `TODO.md`
dosyalarına dağılmış notlar not sayılmıyor — onlar arkeoloji. O yüzden: blog.

## Burada ne olacak

Çoğunlukla işte çözmek zorunda kaldığım ve ikinci kez çözmek istemediğim şeyler:

- .NET ve mikroservis mimarisi kararları, sonuçlarıyla birlikte
- Frontend tarafı — module federation, build hatları, alışıldık sancılar
- Kök nedeni utandırıcı çıkan hata ayıklama hikâyeleri
- Küçük ama işe yarayan araç ipuçları

## İki dil

Yazılar konuya göre **Türkçe** veya **İngilizce**. Her yazıda dil etiketi var; istersen sadece
[`tr`](/tags/tr/) ya da sadece [`en`](/tags/en/) okuyabilirsin.

## Site nasıl çalışıyor

Statik site — veritabanı yok, yama geçilecek sunucu yok:

| Parça | Seçim |
| --- | --- |
| Üretici | [Jekyll](https://jekyllrb.com) |
| Tema | [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) |
| Derleme | GitHub Actions |
| Yayın | GitHub Pages |

Yazı yazmak = `_posts/`{: .filepath } içine bir Markdown dosyası, bir `git push`, gerisini Actions hallediyor.

```bash
git add _posts/2026-09-24-yazim.md
git commit -m "post: yazım"
git push
```

> Dosya adı `YYYY-AA-GG-slug.md` biçiminde olmalı. Uymayan dosyaları Jekyll sessizce yok sayar ve
> ileri tarihli yazılar o tarih gelene kadar yayınlanmaz.
{: .prompt-warning }

Sistem bu kadar. Bakalım ne kadar sürdürebileceğim.
