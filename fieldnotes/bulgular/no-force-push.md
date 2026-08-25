---
id: no-force-push
date: 2026-08-20
impact: medium
area: git
platform: all
---

**Belirti:** `git push` reddedildi — `non-fast-forward`. Uzak repo'da yerelde
görünmeyen commit'ler var.

**Yanlış refleks:** `--force`. Hata mesajı acil görünüyor, force onu anında
susturuyor — ve karşı taraftaki commit'leri sessizce siliyor.

**Doğru sıra:** Önce `git fetch`, sonra **reddedilme sebebine bak.** Bu vakada uzaktaki
commit'ler kullanıcının kendi hesabından gelen zararsız değişikliklerdi (README, FUNDING).
`rebase` + `push` ile temizce çözüldü, hiçbir şey kaybolmadı.

**Genel ders:** AI asistanına git yetkisi verdiğinde en riskli an bu — hata mesajı bir
"engel" gibi görünüyor ve engeli aşmanın en kısa yolu yıkıcı olan. **Geri alınamaz bir
komuta gitmeden önce, engelin ne olduğunu gerçekten oku.** Kurallar dosyana bunu bir
madde olarak yazmaya değer.
