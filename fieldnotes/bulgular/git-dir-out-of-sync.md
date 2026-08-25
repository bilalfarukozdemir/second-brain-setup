---
id: git-dir-out-of-sync
date: 2026-08-25
impact: medium
area: sync
platform: all
---

**Belirti:** Kasa birden fazla cihazda senkronlanıyor (Syncthing vb.) ve senkron
yavaş, telefonda gereksiz yer kaplıyor, ara sıra çakışma dosyaları çıkıyor.

**Sebep:** `.git` klasörü de senkron kapsamındaydı. Git'in nesne veritabanı binlerce
küçük dosyadan oluşuyor; telefonda hiçbir işe yaramıyor (orada git yok) ve iki cihaz
aynı anda commit atarsa senkron aracı git'in iç dosyalarını birleştirmeye çalışıyor —
bu, repo'yu bozabilecek bir durum.

**Çözüm:** `.git`'i senkron dışına al (`.stignore` içine). Ama o zaman versiyon geçmişi
sadece tek makinede kalır — o boşluğu **uzak repo** doldurur (GitHub'a private push).
Yani: notları senkron aracı taşır, geçmişi git taşır. İki mekanizma, iki iş.

**Genel ders:** Dosya senkronu ile versiyon kontrolü aynı problemi çözmüyor, üst üste
bindirilince ikisi de bozuluyor. Hangi klasörün hangi mekanizmaya ait olduğuna baştan
karar ver.
