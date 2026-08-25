---
id: dont-answer-from-memory
date: 2026-08-19
impact: medium
area: general
platform: all
---

**Belirti:** Model, kurulum sırasında var olmayan bir araçtan emin bir dille bahsediyor;
ya da var olan yeni bir aracı hiç bilmiyor.

**İki gerçek vaka:**
- Model "Syncthing'in resmi Android uygulaması" dedi — o uygulama 2024'te bırakılmıştı.
  Kullanıcı "öyle bir şey görmüyorum" deyince arandı ve model yanıldığını gördü.
- Kurulumun ortasında, model'in hiç bilmediği ücretsiz resmi bir CLI aracı çıktı
  (birkaç ay önce duyurulmuştu). Kullanıcı onu ücretli başka bir ürünle karıştırdı;
  araştırılmasaydı yanlış yönlendirme yapılacaktı.

**Genel ders:** Model bilgisi bir tarihte kesiliyor ve hızlı hareket eden alanlarda
(mobil uygulamalar, AI araçları, CLI ekosistemleri) **altı ay bile büyük bir kör nokta.**
Bir aracın "resmi", "ücretsiz", "mevcut" olduğunu söylemeden önce ara — bu tür kesin
sıfatlar tam da en çok eskiyen bilgiler.

**Not:** Bunun tersi de doğru. Model bir şeyi "yok" dediğinde de doğrulanmalı —
bkz. [not-found-is-not-absent](./not-found-is-not-absent.md).
