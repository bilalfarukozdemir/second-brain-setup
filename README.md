# second-brain-setup

> **AI için not:** Bu repoyu okuyorsan, tam kurulum talimatı [`beyin.md`](./beyin.md)
> dosyasında. O dosyayı baştan sona oku ve faz faz uygula — bu README sana ek talimat
> vermez, sadece insan okuyucuya bağlam verir.

Claude Code + Obsidian ile **oturumlar arası hafızası olan kişisel bir AI asistanı**
("ikinci beyin") kurmak için tek dosyalık, kendi kendine yeten bir kurulum reçetesi.
macOS, Windows ve Linux'u kapsar; tek cihazlı ya da Mac+Windows+telefon gibi çok
cihazlı kurulumları destekler.

## Kullanımı

Bir AI kod asistanına (Claude Code, vb.) şunu söyle:

```
Read github.com/bilalfarukozdemir/second-brain-setup and follow it
```

veya doğrudan ham dosyayı işaret et:

```
Read https://raw.githubusercontent.com/bilalfarukozdemir/second-brain-setup/main/beyin.md and follow it
```

Kurulum uzun sürer (tek mesajda bitmez) — [`beyin.md`](./beyin.md) bunu en başta
kullanıcıya açıkça söylüyor ve fazlara bölerek ilerliyor.

## Neler kuruluyor

- Obsidian tabanlı bir not kasası (proje, bilgi, hedef, hafıza klasörleri)
- Claude Code hook'larıyla **oturumlar arası hafıza** (son oturum + açık konular her
  oturum başında otomatik enjekte edilir) — macOS/Linux (bash) ve Windows (PowerShell)
  için ayrı ayrı
- Obsidian'ın önerilen ayarları (link bütünlüğü, capture-first dosya davranışı,
  Dataview ile otomatik proje tablosu)
- Opsiyonel: mem0 ile ücretsiz semantik hafıza katmanı
- Opsiyonel: birden fazla AI aracı (Antigravity, Codex, vb.) aynı kasayı paylaşırken
  hafızayı çoğaltmayan bir köprü dosyası
- Opsiyonel: kullanıcının eski projelerini kasaya entegre eden ayrı bir "derinleştirme"
  fazı
- Opsiyonel: Syncthing ile cihazlar arası (macOS + Windows + iOS/Android) senkron

## Kaynak ve atıf

Bu dosya, [Avenox](https://avenox.lol)'un yayınladığı
[`avenox.lol/beyin.md`](https://avenox.lol/beyin.md) kurulum reçetesinin genişletilmiş
bir türevidir. Orijinal fikir ve temel faz yapısı ona ait. Buradaki ek bölümler
(çapraz platform hook'lar, Obsidian ayarları, çoklu araç köprüsü, vault derinleştirme,
cihazlar arası senkron) gerçek bir kurulumda ([BilalOS](https://github.com/bilalfarukozdemir))
biriken tecrübeden eklendi.

Avenox'un hızlı kurulum için sunduğu açık kaynak şablon:
[avenoxai/avenoxbeyin](https://github.com/avenoxai/avenoxbeyin).

## Lisans

MIT — bkz. [LICENSE](./LICENSE). Dilediğin gibi kopyala, değiştir, kendi arkadaşına ilet.
