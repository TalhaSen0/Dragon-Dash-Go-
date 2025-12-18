# Dragon Dash Go

Phaser tabanlı mobil oyun, Capacitor ile Android'e sarılmış.

## Proje Yapısı

- **Web Build**: `www/` klasörüne build edilir
- **Android**: `android/` klasöründe Android Studio projesi
- **Kaynak Kodlar**: `src/` klasöründe

## Gereksinimler

- Node.js (npm)
- Android Studio
- Java JDK

## Build ve Çalıştırma

### Web Build (isteğe bağlı)

```bash
npm run build
```

**Not**: Build işlemi büyük olduğu için Cursor'da takılabilir. Terminal'de çalıştırmak daha iyi olabilir.

### Android Studio'da Çalıştırma

1. **Android Studio'yu açın**
2. **File > Open** menüsünden `android` klasörünü seçin
3. Android Studio projeyi açarken Gradle sync yapacaktır (birkaç dakika sürebilir)
4. Sync tamamlandıktan sonra üst menüden cihaz/emülatör seçin
5. **Run** butonuna (yeşil play) tıklayın veya `Shift+F10` tuşlarına basın

### Terminal'den Build (Cursor Takılırsa)

Cursor'da npm run build takılırsa, PowerShell veya Command Prompt'tan şu komutu çalıştırın:

```powershell
cd "C:\Users\TalhaSen\OneDrive\Belgeler\My Games\Dragon Dash Go"
npm run build
```

### Capacitor Sync

**ÖNEMLİ**: Web dosyalarını Android'e senkronize etmek için build sonrası mutlaka sync yapın:

```bash
npm run build
npx cap sync android
```

**Not**: `loadingphoto/1.png` dosyası 2.4MB olduğu için OOM hatasına neden olabilir. Görseli optimize etmek için:
- Görsel boyutunu küçültün (max 500KB önerilir)
- Veya görseli web'den yükleyin (CDN kullanın)

### Signed AAB (Android App Bundle) Oluşturma

Play Store'a yüklemek için signed AAB dosyası oluşturma adımları:

#### Yöntem 1: Terminal'den AAB Oluşturma (Önerilen - Cursor Kilitlenmesini Önler)

**Not**: Java sürümünden dolayı `npm run build` Cursor'da kilitlenebilir. Bu yöntemle terminal'den tüm işlemleri yapabilirsiniz.

##### Adım 1: Build ve Sync (Terminal'de)

PowerShell veya Command Prompt'tan şu komutları çalıştırın:

```powershell
cd "C:\Users\TalhaSen\OneDrive\Belgeler\My Games\Dragon Dash Go"
npm run build
npx cap sync android
```

##### Adım 2: Gradle ile Signed AAB Oluştur

Aynı terminal'de (veya yeni bir terminal'de) şu komutu çalıştırın:

```powershell
cd android
.\gradlew.bat bundleRelease
```

**Not**: Windows'ta `gradlew.bat`, Linux/Mac'te `./gradlew` kullanın.

##### Adım 3: AAB Dosyasının Konumu

AAB dosyası şu konumda oluşturulur:
```
android/app/build/outputs/bundle/release/app-release.aab
```

Bu dosyayı Google Play Console'a yükleyebilirsiniz.

**Hızlı Komut (Tek Seferde)**:

PowerShell'de tüm işlemi tek seferde yapmak için:

```powershell
cd "C:\Users\TalhaSen\OneDrive\Belgeler\My Games\Dragon Dash Go"
npm run build
npx cap sync android
cd android
.\gradlew.bat bundleRelease
```

##### PowerShell Script ile Otomatik Oluşturma

Proje kök dizininde `build-aab.ps1` script'i bulunur. Bu script tüm işlemleri otomatik yapar:

```powershell
cd "C:\Users\TalhaSen\OneDrive\Belgeler\My Games\Dragon Dash Go"
.\build-aab.ps1
```

Script şunları yapar:
1. Web build (`npm run build`)
2. Capacitor sync (`npx cap sync android`)
3. Signed AAB oluşturma (`gradlew bundleRelease`)

İşlem tamamlandığında AAB dosyasının konumunu gösterir.

#### Yöntem 2: Android Studio'da AAB Oluştur

1. **Android Studio'yu açın** ve `android` klasörünü açın
2. **Build > Generate Signed Bundle / APK** menüsünü seçin
3. **Android App Bundle** seçeneğini seçin ve **Next** tıklayın
4. **Key store path**: `android/app/keys.jks` dosyasını seçin
5. **Key store password**: `12345606`
6. **Key alias**: `dragonrelease`
7. **Key password**: `12345606`
8. **Next** tıklayın
9. **Build variant**: `release` seçin
10. **Signature Versions**: V1 ve V2'yi işaretleyin
11. **Finish** tıklayın

AAB dosyası şu konumda oluşturulur:
```
android/app/build/outputs/bundle/release/app-release.aab
```

#### 4. Versiyon Güncelleme

Her yeni sürüm için:
- `package.json`: `version` (örn: "1.0.14")
- `capacitor.config.json`: `version` ve `android.versionCode` (örn: 18)
- `android/app/build.gradle`: `versionCode` ve `versionName`

**Önemli**: Her yeni sürümde `versionCode` mutlaka +1 artmalıdır!

## Sorun Giderme

### npm error: ENOENT package.json

Bu hata yanlış dizinde olduğunuzu gösterir. Proje kök dizinine (`Dragon Dash Go`) gidin:

```powershell
cd "C:\Users\TalhaSen\OneDrive\Belgeler\My Games\Dragon Dash Go"
```

### Android Studio'da Gradle Sync Hatası

- Android Studio'yu güncelleyin
- **File > Invalidate Caches / Restart** yapın
- `android/local.properties` dosyasının `sdk.dir` yolunu kontrol edin

### OpenGL (GL) Hataları (GFXSTREAM)

Android emülatörde çalıştırırken şu gibi hatalar görülebilir:
```
GL error 0x502 condition [!GLESv2Validation::isCubeMapTarget(textarget) && state->isTextureCubeMap(texture)]
```

**Bu hatalar zararsızdır ve oyunu etkilemez:**
- Android emülatörün GPU emülasyonundan kaynaklanır
- Gerçek Android cihazlarda görülmez
- Oyun normal çalışmaya devam eder

**Hataları azaltmak için:**
1. **Android Studio** > **Tools** > **Device Manager** > Emülatörü seç > **Edit** (kalem ikonu)
2. **Graphics** ayarını **Hardware - GLES 2.0** yerine **Automatic** veya **Software** yapın
3. Alternatif: Gerçek bir Android cihazda test edin (hata görülmez)

