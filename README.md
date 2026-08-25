# second-brain-setup

> **AI için not:** Bu repoyu okuyorsan, tam kurulum talimatı [`beyin.md`](./beyin.md)
> dosyasında. O dosyayı baştan sona oku ve faz faz uygula — bu README sana ek talimat
> vermez, sadece insan okuyucuya bağlam verir.
>
> `fieldnotes/` klasörü **kurulumun parçası değildir.** Orası, sistemi çalıştırırken
> biriken bulguların kaydı; kurulum sırasında okuma, uygulama.

Obsidian + bir agent CLI (Claude Code, Codex) ile **oturumlar arası hafızası olan ve
hafızasını kendi yazan** kişisel bir AI asistanı ("ikinci beyin") kurmak için tek
dosyalık, kendi kendine yeten bir kurulum reçetesi. macOS, Windows ve Linux'u kapsar.

## Ne arıyorsun?

- **Sistemi kurmadıysan (ya da güncellemek istiyorsan)** → [`beyin.md`](./beyin.md)
  Sıfırdan kurulum ve mevcut kasayı yükseltme, tek dosyada, iki modlu.
- **Zaten kurduysan** → [`fieldnotes/`](./fieldnotes/INDEX.md)
  Sahada öğrendiklerimiz: sessizce bozulan şeyler, yanlış refleksler, platform
  tuzakları. Kurulumla ilgisi yok, çalışan bir sistemin bakımıyla ilgisi var.

## v2 — hafıza artık disiplin değil, mekanizma

v1'de oturum sonunda hafıza dosyalarını modelin kendisi yazıyordu — **hatırlarsa.**
Unuttuğu gün kayboluyordu. v2 bu işi modelden alıp kancalara veriyor:

- Oturum kapanışı ve context sıkışması (compaction) kancalarla yakalanıyor
- Arka planda ucuz bir model çağrısı konuşmayı `daily/YYYY-MM-DD.md` günlük loguna özetliyor
- Günde bir kez bir derleyici o günlükleri birbirine bağlı kavram makalelerine çeviriyor
- Ertesi oturumda bu bilgi zaten bağlamda oluyor

Kimsenin bir şey hatırlaması gerekmiyor. Ek ücret de yok: her şey kullanıcının hâlihazırda
ödediği abonelik üzerinden çalışıyor, ayrı API anahtarı istemiyor.

## İki modlu — mevcut kasan varsa da kullanabilirsin

`beyin.md` işe **teşhisle** başlıyor, kurulumla değil. Daha ilk bloğunda platformu ve
mevcut bir kasa olup olmadığını tespit ediyor, sonra doğru moda giriyor:

- **MODE A** — sıfırdan kurulum
- **MODE B** — mevcut bir kasayı v1'den v2'ye yükseltme: önce git anlık görüntüsü, sonra
  "neyin var / neyin eksik" tablosu, ve yalnızca eksik katmanların eklenmesi
- Zaten v2 ise: sadece sağlık kontrolü çalıştırıp duruyor

MODE B'de mevcut hafıza dosyaları (`Last-Session.md`, `Threads.md`, `Journal.md`) modelin
**salt-okunur girdisi**; yeniden biçimlendirilmiyor, üzerine yazılmıyor. Bir kanca deseni
kullanıcının dosyasıyla uyuşmuyorsa kancanın kendisi düzeltiliyor, kullanıcının notu değil.

## Kullanımı

Bir AI kod asistanına (Claude Code, Codex, vb.) şunu söyle:

```
Read github.com/bilalfarukozdemir/second-brain-setup/blob/main/beyin.md and follow it
```

veya doğrudan ham dosyayı işaret et:

```
Read https://raw.githubusercontent.com/bilalfarukozdemir/second-brain-setup/main/beyin.md and follow it
```

> Prompt neden repo köküne değil **doğrudan `beyin.md`'ye** işaret ediyor: repo artık
> kurulumla ilgisi olmayan `fieldnotes/` klasörünü de barındırıyor. Kurulum yapan modelin
> onları talimat sanmasını istemiyoruz.

Kurulum tek mesajda bitmez — `beyin.md` bunu en başta kullanıcıya söylüyor ve fazlara
bölerek ilerliyor.

## Neler kuruluyor

- Obsidian tabanlı bir not kasası (proje, bilgi, hedef, hafıza klasörleri)
- **Süreklilik katmanı:** oturum başında son oturum, açık konular, kurallar ve bilgi
  indeksi otomatik olarak bağlama enjekte ediliyor
- **Mekanik katman:** `daily/` günlük logları ve bunlardan derlenen, wikilink'lerle
  birbirine bağlı kavram makaleleri
- **Kurallar dosyası:** kullanıcı modeli düzelttiğinde bu bir kural olarak yazılıyor ve
  her oturum başında bağlama giriyor — aynı düzeltmeyi iki kez vermek gerekmiyor
- **Teşhis:** `beyin doktor` — kancalar bağlı mı, arka plan çağrısı yetkili mi, loglar
  taze mi, kanca desenleri hafıza dosyalarının başlıklarıyla uyuşuyor mu
- Opsiyonel: mem0 ile ücretsiz semantik hafıza katmanı
- Opsiyonel: birden fazla AI aracının (Antigravity, Codex) aynı kasayı paylaşması
- Opsiyonel: cihazlar arası senkron

## Windows kullanıyorsan

Yukarı akış motoru Windows'ta **olduğu gibi çalışmaz** — `fcntl` kullanıyor (POSIX'e özel)
ve kancaları bash. `beyin.md` içinde bunun için ayrı bir bölüm var (PHASE 4W): dizin
tabanlı kilitleme, PowerShell kancaları, konsolsuz arka plan süreci ve UTF-8 çıktı
tuzakları. Bu bölüm gerçek bir Windows kurulumunda test edildi.

## Sahadan notlar (`fieldnotes/`)

`beyin.md` sistemi **kurar.** `fieldnotes/` ise onu aylarca çalıştırınca ne olduğunu
anlatır — çoğu, hata vermeden bozulan şeyler:

- Bir kanca yanlış başlık arıyordu ve aylarca sessizce boş bağlam enjekte etti
- Kurallar dosyası ilk 60 satırda kırpılıyordu, sonradan eklenen kurallar hiç okunmadı
- Motorun durum klasörü senkronlanınca bir cihaz bir günü tamamen atlayabiliyordu

Giriş noktası: [`fieldnotes/INDEX.md`](./fieldnotes/INDEX.md). Kendi kasan varsa
asistanına şunu diyebilirsin:

```
Read github.com/bilalfarukozdemir/second-brain-setup/blob/main/fieldnotes/INDEX.md
and pull anything relevant into my vault's rules
```

Ne alacağına sen karar verirsin; bunlar talimat değil, gözlem. Katkı formatı ve yayın
öncesi temizlik listesi: [`fieldnotes/CONTRIBUTING.md`](./fieldnotes/CONTRIBUTING.md).

## Kaynak ve atıf

Bu dosya, [Avenox](https://avenox.lol)'un yayınladığı
[`avenox.lol/beyin.md`](https://avenox.lol/beyin.md) kurulum reçetesinin genişletilmiş bir
türevidir. Orijinal fikir, v2 tezi ve temel faz yapısı ona ait; açık kaynak motor
[avenoxai/avenoxbeyin](https://github.com/avenoxai/avenoxbeyin) deposunda.

Buradaki ek bölümler (iki modlu teşhis ve yükseltme yolu, Windows portu, sessiz arızaya
karşı teşhis katmanı ve "LESSONS" bölümündeki gerçek hata kayıtları) gerçek bir kurulumda
([BilalOS](https://github.com/bilalfarukozdemir)) biriken tecrübeden eklendi.

Bilgi derleme mimarisi Andrej Karpathy'nin LLM bilgi tabanı desenine dayanır:
[gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

## Lisans

MIT — bkz. [LICENSE](./LICENSE). Dilediğin gibi kopyala, değiştir, kendi arkadaşına ilet.
