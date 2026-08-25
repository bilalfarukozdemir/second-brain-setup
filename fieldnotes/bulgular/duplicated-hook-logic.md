---
id: duplicated-hook-logic
date: 2026-08-25
impact: high
area: hooks
platform: all
---

**Belirti:** Bir kanca hatası düzeltildi, ama ikinci AI aracı (aynı kasayı paylaşan)
hâlâ boş bağlam görüyordu.

**Sebep:** Bağlam üretme mantığı iki ayrı kanca dosyasına kopyalanmıştı. Mantık
kopyalanırken [hook-silent-failure](./hook-silent-failure.md) hatası da kopyalanmıştı — yani hata bir kez
değil, iki kez yazılmıştı.

**Çözüm:** İki kancayı ayrı ayrı düzeltmek yerine mantığı ortak bir kütüphane
dosyasına al, iki kanca da onu çağırsın. Sonuç: iki araç birebir aynı bağlamı
görüyor ve bir sonraki desen değişikliğinde düzeltilecek tek dosya var.

**Genel ders:** **Aynı mantık iki dosyada duruyorsa orada iki hata var demektir —
biri şimdi, biri sonra.** Bu özellikle çok araçlı kurulumlarda (Claude Code +
Codex + başka bir CLI) ısırıyor, çünkü her araç kendi kanca klasörünü istiyor ve
kopyala-yapıştır en kolay yol gibi görünüyor.
