# TLTrack-Mobile-Bridge

# 🪟 WINDOWS KURULUMU

⚠️ **Önemli Not:** Windows kurulumundaki tüm adımları Başlat menüsüne sağ tıklayıp açacağınız **Windows PowerShell** (veya Terminal) üzerinden yapmalısınız. Klasik Komut İstemi (CMD) bazı komutlarda hata verebilir.

### Adım 1: Android Cihazda Hata Ayıklamayı Açma
1.  **Ayarlar > Telefon Hakkında** yolunu izleyin.
2.  **Derleme Numarası (Build Number)** üzerine 7 kez tıklayarak geliştirici modunu açın.
3.  **Sistem > Geliştirici Seçenekleri** altından **USB Hata Ayıklama**'yı aktif edin.

### Adım 2: ADB Kurulumu (Winget ile)
PowerShell'i açın ve şu komutu çalıştırarak ADB araçlarını indirin:
```powershell
winget install Google.PlatformTools
