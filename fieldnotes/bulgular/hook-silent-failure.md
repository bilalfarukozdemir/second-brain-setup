---
id: hook-silent-failure
date: 2026-08-25
impact: high
area: hooks
platform: all
---

**Belirti:** Oturum başında "geçen oturum" özeti hiç görünmüyor. Hata mesajı da yok,
kanca sorunsuz çalışmış gibi duruyor.

**Sebep:** Kanca hafıza dosyasında `## Session:` başlığını arıyordu, dosyada
`## Oturum:` yazıyordu. İkisi de aynı kişi tarafından, farklı zamanlarda yazılmış;
arada kimse eşleşip eşleşmediğine bakmamış.

**Nasıl fark edildi:** Kanca elle çalıştırılıp çıktısı gözle okundu. Aylardır
böyleymiş — her oturum "aktaracak bir şey yoktu" sanılarak başlamış.

**Çözüm:** Başlık desenini tek yerde tanımla. Kancaya, hiçbir eşleşme bulamadığında
bunu açıkça söyleyen bir satır ekle ("desen bulunamadı" ile "kayıt yok" farklı şeyler).

**Genel ders:** Hafıza kancaları çökmez, **boş döner.** Boş çıktı "aktarılacak bir şey
yoktu" gibi görünür — yani kanca yanlış olduğunu asla söylemez. Kurulmuş olmak
çalışmak değildir: her kancanın çıktısını en az bir kez gözünle oku, ve teşhis
aracına kanca desenlerini hafıza dosyalarının gerçek başlıklarıyla karşılaştıran
bir kontrol koy.
