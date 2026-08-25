---
id: rules-file-truncated
date: 2026-08-25
impact: high
area: hooks
platform: all
---

**Belirti:** Kurallar dosyasına yeni kural ekleniyor, model onları uygulamıyor.
Dosya doğru, kanca çalışıyor, yine de kural yokmuş gibi davranıyor.

**Sebep:** Kanca dosyanın yalnızca ilk 60 satırını bağlama enjekte ediyordu. Dosya
büyüdükçe alttaki kurallar sessizce görünmez oluyordu.

**Çözüm:** Satır sınırını karakter sınırına çevir (satır uzunlukları çok değişken)
ve **kırpma olduğunda bunu çıktıda açıkça söyle**. Kırpıldığını söylemeyen bir sınır,
sınır değil sessiz veri kaybıdır.

**Genel ders:** Bir kurallar dosyasının yapabileceği en kötü şey, dolu görünüp yarısını
teslim etmektir — çünkü kimse "kural dosyam var, neden çalışmıyor" diye sınıra bakmaz.
Daha genel hâli: **kendi kurduğun denetim mekanizmasının da denetlenmesi gerekiyor.**
Sessiz arızaya karşı kurulan katman, kendisi sessizce arızalanabilir.
