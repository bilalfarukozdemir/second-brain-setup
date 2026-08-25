# Katkı — format ve yayın öncesi kontrol

Bu klasör **kurulum talimatı değil.** Sistemi kurduktan sonra sahada öğrenilenler
buraya düşer. Kurulumla ilgili bir düzeltme varsa yeri `beyin.md`.

## Ne buraya girer

- **Sessiz arıza.** Hata vermeden bozulan bir şey. Bu klasörün asıl varlık sebebi.
- **Yanlış refleks.** İlk akla gelen çözümün zarar verdiği durum (bkz. `no-force-push`).
- **Platform tuzağı.** Bir platformda çalışan, diğerinde sessizce farklı davranan şey.
- **Denendi, bırakıldı.** → `birakilanlar/`

## Ne girmez

- Tek seferlik, sana özel bir arıza (kimseyi ikinci kez ısırmaz)
- "Şu tool'u yazdım" duyurusu — bulgu, tool'un kendisi değil, onu yazdıran acıdır
- Kurulum adımı, öneri, görüş

## Dosya formatı

Bir bulgu = bir dosya. `bulgular/<id>.md`. Dosya adı frontmatter'daki `id` ile aynı.
**Frontmatter ve INDEX satırı İngilizce, gövde Türkçe.**

```markdown
---
id: kebab-case-english-id
date: YYYY-MM-DD
impact: high | medium | low
area: hooks | sync | git | setup | vault | memory | general
platform: all | windows | macos | linux
---

**Belirti:** Dışarıdan ne görünüyordu.
**Sebep:** Gerçekte ne oluyordu.
**Çözüm:** Ne yapıldı.
**Genel ders:** Bu vakadan bağımsız olarak geçerli olan şey.
```

`Genel ders` zorunlu ve en önemli alan. O olmadan dosya bir hata kaydı olur, aktarılabilir
bir bulgu olmaz. Yazamıyorsan bulgu henüz olgunlaşmamıştır.

**`impact` ciddiye alınır.** Bu alan bir seçilim mekanizması: `low` bulgular birikirse
klasör çöplüğe döner. Bir şeyin `high` olması için sessizce ve uzun süre bir şeyi
bozmuş olması gerekir.

Yeni dosya eklerken `INDEX.md` tablosuna da bir satır ekle — İngilizce, tek cümle.
İndeks makine tarafından okunuyor; asıl giriş kapısı o.

## Yayın öncesi kontrol (zorunlu)

Bu repo **public.** Bulgular gerçek bir kasadan çıkıyor ve o kasa kişisel. Push'tan
önce yazdığın dosyada şunların **hiçbiri** olmamalı:

- [ ] Kişi adı (kendi adın dahil), kullanıcı adı, e-posta, telefon
- [ ] Müşteri, işveren, proje adı; herhangi bir ticari rakam veya anlaşma
- [ ] Yerel dosya yolu (`C:\Users\<isim>\...`) — genelleştir: `%USERPROFILE%\...`
- [ ] API anahtarı, token, repo/sunucu adresi, cihaz adı
- [ ] Sağlık, finans, ilişki gibi kişisel veri
- [ ] Üçüncü bir kişinin, rızası olmadan yazılmış bilgisi

Son madde en kolay atlanandır ve en pahalıya patlayandır: kasadaki verinin bir kısmı
seni yazan değil, **çevrendeki insanlara ait.** Bir bulguyu anlatmak için gerçek bir
isme ihtiyaç varsa, o bulgu buraya girmez.

Anonimleştirme yeterli: "kullanıcı", "ikinci araç", "bir proje" gibi. Bulgunun değeri
kimin başına geldiğinde değil, neden olduğunda.

## Akış

Şu an iki kişiyiz, PR seremonisi yok — doğrudan `main`'e push. Tek bulgu = tek dosya
olduğu için çakışma pratikte imkânsız; ortak dosya sadece `INDEX.md` ve oraya tek satır
ekleniyor. Üçüncü kişi katıldığında PR'a geçilir, format değişmez.
