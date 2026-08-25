---
id: obsidian-ghost-notes
date: 2026-08-19
impact: medium
area: vault
platform: all
---

**Belirti:** Dosya gezgininde aynı isim iki kez görünüyor. Kasa kökünde, kimsenin
yazmadığı boş notlar birikiyor.

**Sebep:** Obsidian'da **path'siz bir wikilink** — yani gerçek bir nota değil, bir
klasöre ya da hiç var olmayan bir isme işaret eden `[[300-Projects]]` gibi bir bağ —
tıklandığında Obsidian onu "eksik not" sayıp **kasa kökünde o isimde boş bir dosya
yaratıyor.** Uyarı yok, onay yok; tıklama yeterli.

**Nasıl fark edildi:** Kullanıcı gezginde çift isim gördü. Kazınca 6 hayalet dosya çıktı.
Biri özellikle öğreticiydi: **kurallar dosyasının kendi örnek metninden doğmuştu** —
dosyada wikilink sözdizimini anlatmak için yazılmış `[[wikilink]]` ifadesi, bir kez
tıklanınca `wikilink.md` adında gerçek bir not üretmişti.

**Çözüm:** Hayalet dosyaları sil, sonra kaynağı kurut:
- Wikilink yazarken **gerçek nota** işaret et, klasöre değil.
- Dokümantasyon/örnek metinlerde wikilink sözdizimini **kod bloğu içinde** yaz
  (`` `[[wikilink]]` ``) — kod bloğundaki bağ tıklanabilir olmuyor.
- Ara sıra kasa köküne bak: oraya ait olmayan tek satırlık boş notlar hayalet adayıdır.

**Genel ders:** Bir sistemi anlatan metin, o sistemin **içinde** yaşıyorsa örnekleri
canlıdır. Kural dosyanda "böyle yazılır" diye gösterdiğin şey, gösterdiğin anda
çalışmaya başlayabilir. Bu Obsidian'a özel değil — dokümantasyonun kendisi çalıştırılabilir
olduğunda hep geçerli.
