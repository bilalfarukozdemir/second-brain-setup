---
id: windows-desktop-is-onedrive
date: 2026-08-20
impact: low
area: setup
platform: windows
---

**Belirti:** Kullanıcı "dosyayı masaüstüne koydum" diyor, model `%USERPROFILE%\Desktop`
altında arıyor ve bulamıyor.

**Sebep:** Windows'ta OneDrive kurulu olduğunda Masaüstü klasörü çoğu zaman
`%USERPROFILE%\OneDrive\Desktop` altına yönlendirilmiş oluyor. Eski yol da diskte
duruyor ama boş — yani hata vermiyor, sadece boş çıkıyor.

**Çözüm:** İki yola da bak, ya da bilinen klasör yolunu registry'den oku. Kullanıcının
kasası bu yolun altındaysa bunu kurulum sırasında kurallar dosyasına yaz — her oturumda
yeniden keşfedilmesin.

**Genel ders:** "Masaüstü", "Belgeler", "Ana klasör" gibi isimler platformlar ve
kurulumlar arasında sabit değil. Kullanıcının söylediği yer ile diskteki yol farklı
olabilir; sabit yol varsaymak yerine bir kez tespit edip kalıcı hafızaya yaz.
