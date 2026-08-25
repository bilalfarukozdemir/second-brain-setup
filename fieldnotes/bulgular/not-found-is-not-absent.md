---
id: not-found-is-not-absent
date: 2026-08-24
impact: low
area: general
platform: windows
---

**Belirti:** Paket yöneticisi "başarıyla kuruldu" dedi, model programı hiçbir yerde
bulamadı ve "kurulum başarısız" diye rapor etti. Yanlış alarmdı — program kuruluydu.

**Sebep:** Windows'ta MSIX paketleri korumalı bir klasöre (`WindowsApps`) gidiyor ve
normal dosya taraması oraya erişemiyor. Doğru kontrol dosya araması değil,
`Get-AppxPackage` idi.

**Genel ders:** **"Bulamadım" ile "yok" aynı şey değil.** Bir aracın kendi arama
yönteminin göremediği yerler vardır; olumsuz sonucu rapor etmeden önce "bu yöntem
oraya bakabiliyor mu" diye sor. Windows'ta paketleme sistemleri (MSIX, App Execution
Alias) bunu özellikle sık yapıyor.
