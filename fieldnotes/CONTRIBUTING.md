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

Yeni dosya eklerken `INDEX.md` tablosuna da bir satır ekle — İngilizce, tek cümle.
İndeks makine tarafından okunuyor; asıl giriş kapısı o.

## `birakilanlar/` formatı

Denenip ya da değerlendirilip **yapılmamış** şeyler. Bir kayıt = bir dosya,
`birakilanlar/<id>.md`.

```markdown
---
id: kebab-case-english-id
date: YYYY-MM-DD
verdict: dropped
area: tooling | workflow | sync | vault | general
---

**Aday:** Ne değerlendirildi (varsa link).
**Neden bakıldı:** Hangi ihtiyacı karşılayacaktı.
**Neden bırakıldı:** Gerçek gerekçe.
**Genel ders:** Bu vakadan bağımsız olarak geçerli olan şey.
```

"Kötüydü" bir gerekçe değil. Çoğu aday **kötü olduğu için değil, uymadığı için**
bırakılır — gerekçeyi öyle yaz. Başkasının emeğini küçültmeden, neden senin durumuna
oturmadığını anlat.

Bir "hayır" kaydı, bir "evet" kadar değerli: yazılmazsa aynı aday altı ay sonra
sıfırdan tekrar değerlendirilir.

## Seçilim

**`impact` ciddiye alınır.** Bu alan bir seçilim mekanizması: `low` bulgular birikirse
klasör çöplüğe döner. Bir şeyin `high` olması için sessizce ve uzun süre bir şeyi
bozmuş olması gerekir.


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

## Akış — fork + PR

Repo public, yani **katkı için collaborator olmana gerek yok.**

1. Repoyu fork'la.
2. Bulgunu `fieldnotes/bulgular/<id>.md` olarak ekle, `INDEX.md`'ye tek satır yaz.
3. PR aç. Her PR tek bulgu olsun — konusu ayrı olan şeyler ayrı PR.
4. Merge'ü repo sahibi yapıyor.

**Neden doğrudan push değil:** İncelemeden geçmeyen bir bulgu klasörü zamanla birikme
yığınına dönüşüyor. PR, `impact` alanının insan tarafı — birinin "bu gerçekten `high`
mı, bu ikinci kez birini ısırır mı" diye bakması gerekiyor. Ayrıca **yayın öncesi
temizlik listesi** ancak ikinci bir çift gözle işe yarıyor; kendi metnindeki kendi
müşterinin adını görmüyorsun.

Tek bulgu = tek dosya olduğu için çakışma pratikte imkânsız; ortak dosya sadece
`INDEX.md` ve oraya tek satır ekleniyor.
