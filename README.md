# Namaz Saatleri

Bulunulan konuma göre namaz vakitlerini gösteren, kıble pusulası ve vakit hatırlatma bildirimleri içeren Flutter uygulaması. **iOS ve Android**'de çalışır.

## Özellikler

- 🕌 **Namaz vakitleri** — İmsak'tan Yatsı'ya 6 vakit, Diyanet (Türkiye) hesaplama yöntemiyle **cihaz üzerinde** hesaplanır. Vakit hesaplaması için internet gerekmez; internet yalnızca şehir adının gösterilmesi (geocoding) için kullanılır.
- 📍 **Konuma göre** — GPS ile bulunduğunuz şehri algılar; son konum önbelleğe alınır, çevrimdışıyken de çalışır.
- ⏳ **Geri sayım** — Sıradaki vakte kalan süre canlı olarak gösterilir.
- 🧭 **Kıble pusulası** — Manyetometre sensörüyle kıble yönünü gösterir; hizalanınca titreşimle bildirir.
- 🔔 **Vakit hatırlatmaları** — Yerel bildirimlerle (iOS: UNUserNotificationCenter, Android: AlarmManager) vakit girince veya seçilen dakika kadar önce hatırlatır. Sunucu yok; bildirimler cihazda önceden planlanır.

## Mimari

Hiçbir arka uç (backend) bağımlılığı yoktur; uygulama tamamen kişinin telefonunda çalışır.

```
lib/
├── main.dart                        # Uygulama girişi, tema, sekmeli gezinme
├── screens/
│   ├── home_screen.dart             # Vakit listesi, geri sayım, bildirim ayarları
│   └── qibla_screen.dart            # Kıble pusulası
└── services/
    ├── prayer_service.dart          # Vakit + kıble hesabı (adhan, Diyanet yöntemi)
    ├── location_service.dart        # GPS + şehir adı + önbellek
    └── notification_service.dart    # Yerel bildirim planlama
```

## Geliştirme

```bash
flutter pub get
flutter test        # birim testleri
flutter analyze     # statik analiz
flutter run         # bağlı cihazda/simülatörde çalıştır
```

## Dağıtım (Deployment)

### Android
```bash
flutter build apk --release          # APK: build/app/outputs/flutter-apk/
flutter build appbundle --release    # Play Store için AAB
```

### iOS (yalnızca Xcode kurulu macOS'ta)
```bash
flutter build ipa --release          # App Store / TestFlight için
# veya cihaza doğrudan: flutter run --release -d <cihaz-id>
```
> Not: Cihaza kurulum ve App Store dağıtımı için Xcode'da imzalama (Signing & Capabilities) ayarlanmalıdır.

## Pusula Doğruluğu

- Kıble açısı, Kâbe'ye (21.4225°K, 39.8262°D) büyük daire başlangıç açısı olarak hesaplanır ve **gerçek kuzeye** göredir. Bilinen şehir değerleriyle birim testlerle doğrulanır.
- **iOS** pusulası `trueHeading` (gerçek kuzey) bildirir; ek düzeltme gerekmez.
- **Android** pusulası manyetik kuzeyi baz aldığından, WMM-2025 modeliyle (geomag) manyetik sapma düzeltmesi uygulanır.
- Donanım pusulası çevredeki metal/mıknatıslardan etkilenebilir; uygulama içinde 8 çizerek kalibrasyon önerilir.

## Atıflar

- Ezan sesi: ["The Adhan - Muslim Call to Prayer" — Aaqib Azeez](https://commons.wikimedia.org/wiki/File:The_Adhan_-_Muslim_Call_to_Prayer_-_Aaqib_Azeez.mp3), [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.tr). Bildirim sesi sınırları nedeniyle ilk 28 saniyesi kullanılmıştır (kırpılmış türev de aynı lisansla dağıtılır).
- Amiri fontu: [Amiri Project](https://github.com/aliftype/amiri), SIL Open Font License 1.1.

## Sürümleme (Versioning)

[Semantic Versioning](https://semver.org/lang/tr/) kullanılır: `MAJOR.MINOR.PATCH`

- Her sürüm `pubspec.yaml` içindeki `version:` alanında tutulur (`0.1.0+1` → sürüm `0.1.0`, derleme no `1`).
- Her yayın GitHub'da `vX.Y.Z` etiketiyle (tag) işaretlenir ve [CHANGELOG.md](CHANGELOG.md) güncellenir.
- Eski bir sürüme dönmek için:
  ```bash
  git tag -l                 # mevcut sürümleri listele
  git checkout v0.1.0        # istenen sürüme dön
  ```

## Yapılacaklar (TODO)

- [ ] **v1.2.0'ı App Store incelemesine gönder** — Yeni sureler (Duhâ, İnşirah, Tîn, Tekâsür, Asr, Elif Lâm Mîm, Âmene'r-Resûlü) build 13 ile TestFlight'ta; App Store Connect'te 1.2.0 taslağı hazır. What's New metinleri (TR/AR/EN) yazılıp build bağlanacak ve gönderilecek.
- [ ] **Google Play yayını** — Uygulama Android'de çalışıyor ancak Play Store'da henüz yayınlanmadı. AAB derlemesi, Play Console kaydı, mağaza metinleri (3 dil, "reklam yok" vurgusuyla) ve ekran görüntüleri gerekiyor.
- [ ] **Self-hosted runner doğrulaması** — Tag push → yerel derleme → TestFlight otomasyonu (`~/actions-runner-vakte`, `.github/workflows/testflight.yml`) kurulu ama servis modunda keychain (errSecInternalComponent) hatası nedeniyle hiç başarıyla çalışmadı; oturum modunda (`./run.sh`) doğrulanacak. Şimdilik yüklemeler elle yapılıyor.
- [ ] **App Store Connect Support URL güncelle** — Depo `namaz-saatleri` → `vakte` olarak yeniden adlandırıldı; ASC'deki destek adresi eski URL'yi gösteriyor (yönlendirme çalışıyor). Bir sonraki sürüm gönderiminde güncellenecek.
- [ ] **Mağaza ekran görüntülerini yenile** — Mevcut görüntüler eski arayüzden; iki sekmeli İbadet bölümü ve yeni içerikle güncel build'den yeniden alınabilir.
