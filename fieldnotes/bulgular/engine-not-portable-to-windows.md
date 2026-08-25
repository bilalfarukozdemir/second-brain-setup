---
id: engine-not-portable-to-windows
date: 2026-08-25
impact: high
area: setup
platform: windows
---

**Belirti:** Yukarı akış motoru (`avenoxai/avenoxbeyin`) Windows'ta olduğu gibi
çalışmıyor.

**Sebep:** İki ayrı bağımlılık:
- Kilitleme için `fcntl` kullanılıyor — POSIX'e özel, Windows'ta yok.
- Kancalar bash script'i.

**Çözüm:** Kopyalanmaz, **portlanır.** Gereken dört değişiklik:
- `fcntl` yerine **dizin tabanlı kilitleme** (`mkdir` atomiktir, her platformda çalışır)
- bash yerine **PowerShell** kancaları
- arka plan çağrısı **konsolsuz** başlatılmalı, yoksa her oturum kapanışında ekranda
  siyah pencere açılıyor
- **UTF-8 çıktı**: PowerShell 5.1 varsayılan olarak sistem kod sayfasını kullanıyor,
  Türkçe karakterler ve emoji bozuluyor. Dosya yazarken kodlamayı açıkça ver.

Bu adımların tamamı `beyin.md` içinde ayrı bir bölümde (PHASE 4W) duruyor ve gerçek bir
Windows kurulumunda test edildi.

**Genel ders:** "Cross-platform" iddiası olan bir motoru kurmadan önce kilitleme,
script dili ve metin kodlaması olmak üzere üç yere bak. Üçü de sessizce bozulur:
kilitleme çakışma anında, kodlama ise sadece Latin alfabesi dışına çıkınca.
