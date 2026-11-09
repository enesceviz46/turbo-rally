# Turbo Rally — Mobil Uygulama Paketi

Bu pakette oyunu telefona uygulama olarak kurmanın iki yolu var.

## 1) En hızlı yol: PWA (5 dakika, ücretsiz)

Oyun zaten kurulabilir bir web uygulaması (PWA) olarak hazır: ikon, tam ekran, çevrimdışı çalışma dahil.

1. `www` klasörünün içindekileri bir **HTTPS** sunucuya yükle. En kolayı:
   - **Netlify Drop**: https://app.netlify.com/drop adresine `www` klasörünü sürükle-bırak.
   - Alternatifler: GitHub Pages, Cloudflare Pages, Vercel.
2. Çıkan adresi telefonda aç:
   - **Android (Chrome):** Oyunda *Ayarlar → Uygulamayı yükle* düğmesi çıkar (veya tarayıcı menüsü → "Uygulamayı yükle").
   - **iPhone/iPad (Safari):** Paylaş → **Ana Ekrana Ekle**.
3. Ana ekranda "Turbo Rally" ikonu belirir, uygulama gibi tam ekran açılır, internet olmadan da oynanır.

> Güncelleme yayınlarken `www/sw.js` içindeki `V='turbo-rally-v1'` değerini artır (v2, v3 …), böylece cihazlar yeni sürümü alır.

## 2) Mağaza uygulaması (Android APK / AAB) — Capacitor

Gerekenler: **Node.js 20+** ve **Android Studio** (içinde JDK ve Android SDK gelir).

```bash
npm install
npm i @capacitor/core @capacitor/cli @capacitor/android
npx cap add android
npm run assets        # resources/icon.png ve splash.png'den ikon + açılış ekranlarını üretir
npx cap sync
npx cap open android  # Android Studio açılır
```

Android Studio'da:
- Telefonu USB ile bağlayıp **Run ▶** → oyun telefonda açılır (test).
- Dikey kilitlemek için `android/app/src/main/AndroidManifest.xml` içinde `<activity ...>` etiketine `android:screenOrientation="portrait"` ekle.
- Yayın için **Build → Generate Signed Bundle / APK** (Play Store için AAB, doğrudan paylaşmak için APK).
- Google Play Console geliştirici hesabı tek seferlik **25 USD**.

Kodlamadan denemek istersen: yukarıdaki PWA adresini https://www.pwabuilder.com sitesine yapıştırıp **Android paketi** indirebilirsin.

## 2-B) Bilgisayara hiçbir şey kurmadan APK (GitHub ile, ücretsiz)

Android Studio kurmak istemezsen APK'yı GitHub'ın bulut makinesi derler:

1. https://github.com adresinde ücretsiz hesap aç → sağ üstte **+ → New repository** → adı `turbo-rally` → **Create repository**.
2. Açılan sayfada **"uploading an existing file"** bağlantısına tıkla. Zip'i bilgisayarında klasöre çıkar, içindeki **her şeyi** (`www`, `resources`, `package.json`, `capacitor.config.json` ve `.github` klasörü) sayfaya sürükle → **Commit changes**.
   - `.github` klasörü gizli sayılıp sürüklenmezse: **Add file → Create new file** de, dosya adına `.github/workflows/android.yml` yaz, bu zip'teki `android.yml` içeriğini yapıştır → **Commit**.
3. Üstteki **Actions** sekmesine gir → soldan **Android APK** → **Run workflow**. Yaklaşık 5-10 dakika sürer.
4. Bitince (yeşil tik) iş satırına tıkla, altta **Artifacts → turbo-rally-apk** dosyasını indir (zip içinden `app-debug.apk` çıkar).
5. APK'yı Android telefona gönder, aç. "Bilinmeyen kaynaklardan yüklemeye izin ver" uyarısı çıkarsa izin ver.

Not: Bu **debug APK**'dır, telefona doğrudan kurulur ama Play Store'a yüklenmez. Play Store için imzalı AAB gerekir (bölüm 2).

## 3) iPhone / iPad (App Store)

Mac + **Xcode** + Apple Developer Program (**99 USD/yıl**) gerekir:

```bash
npm i @capacitor/ios
npx cap add ios
npm run assets
npx cap sync
npx cap open ios      # Xcode açılır, imzala ve Archive et
```

## Notlar

- Oyun kayıtları (altın, araçlar, rekorlar) cihazın içinde saklanır; uygulama silinirse kayıtlar da gider.
- Piksel yazı tipi (Press Start 2P) internetten yüklenir; çevrimdışı ilk açılışta yedek yazı tipi kullanılır, oyun yine çalışır. İstersen yazı tipi dosyasını `www/fonts` içine koyup `index.html`'deki bağlantıyı yerel dosyaya çevirebilirsin.
- Klasörler: `www/` (oyun + manifest + service worker + ikonlar), `resources/` (1024 px ikon ve açılış görseli), `capacitor.config.json` (uygulama kimliği `com.turborally.game`; kendi kimliğinle değiştir).
