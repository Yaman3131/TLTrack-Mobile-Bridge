---

#  TLTrack-Mobile-Bridge for Windows

---

## 🌍 Language / Dil
* [English](#english-version)
* [Türkçe](#türkçe-versiyon)

* [💬 Support & Contact](#support)
---
---
<a name="english-version"></a>
## 🪟 Windows Installation Guide

> [!IMPORTANT]
> **Important Note:** All steps below must be executed in **Windows PowerShell** (. The classic Command Prompt (CMD) does not support certain environment variables (`$env:`) and may cause errors.

### Step 1: Android Device Preparation
1. Go to **Settings > About Phone**.
2. Tap on the **Build Number** 7 times to enable Developer Options.
3. Navigate to **System > Developer Options** and enable **USB Debugging**.

### Step 2: Install ADB (Android Debug Bridge)
If ADB is not installed on your computer, open PowerShell and run:
```powershell
winget install Google.PlatformTools
```
*(You may need to restart PowerShell for the changes to take effect.)*

### Step 3: Device Connection and Authorization
Connect your device via USB cable and run:
```powershell
adb devices
```
On your device screen, check **"Always allow from this computer"** and tap OK. When you see your device listed with the status `device`, the connection is successful.

---

### Step 3.1: Wireless Connection Setup (Optional)

To stream data over Wi-Fi and eliminate cable clutter (Ensure both PC and device are on the same Wi-Fi network):

1. **While the device is still connected via cable**, open the TCP port:
   ```powershell
   adb tcpip 5555
   ```
2. Find your device's IP address (**Settings > About Tablet > Status** or within Wi-Fi settings).
3. **Unplug the cable** and connect via IP:
   ```powershell
   adb connect 192.168.1.XX:5555
   ```
   *(Note: Replace `192.168.1.XX` with your actual device IP.)*
4. Verify the connection by running `adb devices` again. You should see the IP address in the list.

---

### Step 4: Setup the Data Bridge (Auto-Sync)
The following command creates a `TLI_Data` folder on your Desktop and pulls the log file from your device every 5 seconds. 

**Keep this window open during your session:**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\Desktop\TLI_Data"; while ($true) { adb pull "/sdcard/Android/data/com.xd.TLglobal/files/UE4Game/UE_game/UE_game/Saved/Logs/UE_game.log" "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log.tmp" *>$null; Move-Item -Path "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log.tmp" -Destination "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log" -Force; Write-Host -NoNewline "."; Start-Sleep -Seconds 5 }
```

---

## 🚀 TITrack Software Installation

### 1. Clone the Project
Open a new PowerShell window:
```powershell
cd $env:USERPROFILE\Desktop
git clone https://github.com/astockman99/TITrack.git
cd TITrack
```

### 2. Python and Dependencies
If you encounter the `Microsoft Visual C++ 14.0 or greater is required` error or if Python is missing, it is recommended to install [Python 3.12 via Microsoft Store](https://apps.microsoft.com/detail/9PNRBTZXMB4Z).

Then, install the required libraries:
```powershell
python -m pip install supabase postgrest fastapi uvicorn[standard]
```

### 3. Database Initialization (First Time Only)
```powershell
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack init
```

---

## 📊 Running the Analysis

To run the software, you must open **two separate** PowerShell windows:

### **Window A: Data Handler (Tail)**
Monitors logs in real-time and processes them into the database:
```powershell
cd $env:USERPROFILE\Desktop\TITrack
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack tail "$env:USERPROFILE\Desktop\TLI_Data\UE_game.log"
```

### **Window B: Web Interface (Serve)**
Launches the analysis dashboard:
```powershell
cd $env:USERPROFILE\Desktop\TITrack
$env:PYTHONPATH = "$($PWD.Path)\src"
python -m titrack serve --no-window
```

---

## ⚙️ Final Configuration and Usage

1. Open your browser and navigate to: `http://127.0.0.1:8000`
2. Go to the **Settings** tab in the left menu.
3. In the **Game Directory** field, enter the path to your data folder:
   * **Example:** `C:\Users\YourUsername\Desktop\TLI_Data`
4. Make sure to check the **Cloud Sync** box to synchronize your data.
5. The application may prompt for a restart to apply changes.

<img width="3336" height="2100" alt="image" src="https://github.com/user-attachments/assets/886d5739-436f-4c07-9bfe-0e5a71425296" />

---


<a name="türkçe-versiyon"></a>
##  Windows Kurulum Rehberi

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
5. **Sizden yeniden başlatmanızı isteyebilir.
<img width="3336" height="2100" alt="image" src="https://github.com/user-attachments/assets/466f69b1-a144-4f98-8d2c-9adf298e3376" />

---
<a name="support"></a>
## 💬 Support & Contact / Destek ve İletişim

### English
If you have any questions, encounter bugs, or need help with the setup:
* **GitHub:** You can open an [Issue](https://github.com/Yaman3131/TLTrack-Mobile-Bridge-MACOS-and-Windows-/issues) on this repository.
* **Discord:** You can reach me directly at: `Nardona`

---

### Türkçe
Herhangi bir sorunuz olursa, hata ile karşılaşırsanız veya kurulumda yardıma ihtiyaç duyarsanız:
* **GitHub:** Bu depo üzerinden bir [Issue (Sorun)](https://github.com/Yaman3131/TLTrack-Mobile-Bridge-MACOS-and-Windows-/issues) oluşturabilirsiniz.
* **Discord:** Bana doğrudan ulaşabilirsiniz: `Nardona`

---
