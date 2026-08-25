---
id: stignore-hides-engine-state
date: 2026-08-25
impact: medium
area: sync
platform: all
---

**Belirti:** Yok. Sorun hiç görünmedi — potansiyel olarak bulundu.

**Sebep:** Motorun durum klasörü (`.state/` — "bu günlük özetlendi mi", "bugün derleme
yapıldı mı" gibi damgaları tutuyor) senkron kapsamındaydı. İki cihaz kullanılıyorsa
A makinesinde atılan "bugün derledim" damgası B makinesine geçer ve B, o günü hiç
işlemeden **sessizce atlar.**

**Çözüm:** Motor state klasörlerini senkron dışına al. Bunlar makineye özeldir;
başka makinede kendileri yeniden oluşur.

**Genel ders:** Senkronlanacak şey **veri**dir, **ilerleme damgası** değil. "Şu iş
yapıldı" kaydı yalnızca onu yapan makine için doğrudur; taşındığında yalana dönüşür.
Aynı mantık lock dosyaları, cache ve `__pycache__` için de geçerli.
