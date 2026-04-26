---

# 🛰️ TLTrack-Mobile-Bridge for Windows

Bu proje, Android cihazınızdaki **Torchlight: Infinite** log verilerini anlık olarak Windows bilgisayarınıza çeker ve **TITrack** yazılımı üzerinden analiz etmenizi sağlar.

---

## 🪟 Windows Kurulum Rehberi

> [!IMPORTANT]
> **Önemli Not:** Aşağıdaki tüm adımlar **Windows PowerShell**  üzerinde çalıştırılmalıdır. Klasik Komut İstemi (CMD) bazı değişkenleri (`$env:`) tanımaz ve hata verir.

### Adım 1: Android Cihaz Hazırlığı
1. **Ayarlar > Telefon Hakkında** yolunu izleyin.
2. **Derleme Numarası (Build Number)** üzerine 7 kez tıklayarak Geliştirici Seçeneklerini açın.
3. **Sistem > Geliştirici Seçenekleri** menüsüne girin ve **USB Hata Ayıklama** modunu aktif edin.

### Adım 2: ADB (Android Debug Bridge) Kurulumu
Bilgisayarınızda ADB yüklü değilse, PowerShell'i açın ve şu komutla hızlıca kurun:
```powershell
winget install Google.PlatformTools
```
*(Kurulum sonrası komutun tanınması için PowerShell'i kapatıp tekrar açmanız gerekebilir.)*

### Adım 3: Cihaz Bağlantısı ve Onay
Cihazı kablo ile bağlayın ve şu komutu yazın:
```powershell
adb devices
```
Cihaz ekranında çıkan **"Bu bilgisayara her zaman izin ver"** uyarısını onaylayın. Listede cihaz isminin yanında `device` yazısını gördüğünüzde bağlantı tamamdır.

---

### Adım 3.1: Kablosuz Bağlantı Kurulumu (Opsiyonel)

Kablo kalabalığından kurtulmak ve verileri Wi-Fi üzerinden çekmek isterseniz bu adımı uygulayabilirsiniz. (Bilgisayar ve cihaz aynı Wi-Fi ağına bağlı olmalıdır.)

1.  **Cihaz kablo ile bağlıyken** şu komutu vererek TCP portunu açın:
    ```powershell
    adb tcpip 5555
    ```
2.  Cihazınızın IP adresini bulun (**Ayarlar > Tablet Hakkında > Durum** veya Wi-Fi ayarları içerisinden bakabilirsiniz).
3.  **Kabloyu çekin** ve aşağıdaki komutla IP üzerinden bağlanın (Örn: 192.168.1.50):
    ```powershell
    adb connect 192.168.1.XX:5555
    ```
    *(Not: `192.168.1.XX` yerine kendi cihazınızın IP adresini yazmalısınız.)*
4.  Bağlantıyı doğrulamak için tekrar `adb devices` yazın. Listede IP adresinizi görüyorsanız işlem tamamdır.



---

### Adım 4: Veri Köprüsünü Kurma (Otomatik Akış)
Aşağıdaki komut, Masaüstünüzde otomatik olarak `TLI_Data` klasörü oluşturur ve cihazdaki log dosyasını her 5 saniyede bir bilgisayarınıza çeker. 

**Bu pencereyi işlem boyunca kapatmayın:**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Desktop\TLI_Data"; while ($true) { adb pull "/sdcard/Android/data/com.xd.TLglobal/files/UE4Game/UE_game/UE_game/Saved/Logs/UE_game.log" "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log.tmp" *>$null; Move-Item -Path "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log.tmp" -Destination "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log" -Force; Write-Host -NoNewline "."; Start-Sleep -Seconds 5 }
```

---

## 🚀 TITrack Yazılımının Kurulumu

### 1. Projeyi İndirin
Yeni bir PowerShell penceresi açın:
```powershell
cd $env:USERPROFILE\Desktop
git clone https://github.com/astockman99/TITrack.git
cd TITrack
```

### 2. Python ve Gerekli Kütüphaneler
Eğer `Microsoft Visual C++ 14.0 or greater is required` hatası alıyorsanız veya Python yüklü değilse, [Microsoft Store Python 3.12](https://apps.microsoft.com/detail/9PNRBTZXMB4Z) üzerinden kurulum yapmanız önerilir.

Ardından kütüphaneleri kurun:
```powershell
python -m pip install supabase postgrest fastapi uvicorn[standard]
```

### 3. Veritabanı Hazırlığı (Sadece İlk Kurulumda)
```powershell
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack init
```

---

## 📊 Analizi Başlatma

Yazılımın çalışması için **iki ayrı** PowerShell penceresi açmanız gerekmektedir:

### **Pencere A: Veri İşleyici (Tail)**
Logları anlık olarak okur ve veritabanına işler:
```powershell
cd $env:USERPROFILE\Desktop\TITrack
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack tail "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log"
```

### **Pencere B: Web Arayüzü (Serve)**
Analiz ekranını yayınlar:
```powershell
cd $env:USERPROFILE\Desktop\TITrack
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack serve --no-window
```

---

## ⚙️ Son Ayarlar ve Kullanım

1. Tarayıcınızda şu adrese gidin: `http://127.0.0.1:8000`
2. Sol menüden **Settings** sekmesine tıklayın.
3. **Game Directory** alanına, logların biriktiği klasör yolunu girin:
   * `C:\Users\KullaniciAdiniz\Desktop\TLI_Data`
4. **Cloud Sync** seçeneğini işaretleyerek verileri senkronize edin.

---
