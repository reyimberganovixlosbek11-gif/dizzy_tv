# Dizzy.tv

Flutter asosidagi kino oqim ilovasi (Android). Firebase Auth (Google login + email/password), Firestore, Cloudflare R2 orqali video oqim.

---

## Loyihani ishga tushirish

```bash
flutter pub get
flutter run
```

## Release APK yasash (Codemagic orqali)

1. GitHub'ga push qiling
2. Codemagic avtomatik build boshlaydi (`codemagic.yaml` orqali)
3. APK `artifacts/` bo'limida paydo bo'ladi

---

## ⚠️ MUHIM: Firebase SHA kalitlari (har doim yangilab turing)

### Muammo nima?

Firebase Google login va boshqa auth xizmatlari uchun APK imzosini tekshiradi.
**Signing keystore o'zgarsa yoki yangi muhit (Codemagic) ishlatilsa — SHA kalitlarini Firebase'ga qo'shmasangiz, Google login ishlaMaydi.**

### Qaysi SHA kalitlari kerak?

| Holat | Keystore | Firebase'ga qo'shish kerakmi? |
|-------|----------|-------------------------------|
| Debug (lokal `flutter run`) | `~/.android/debug.keystore` | Ha (test uchun) |
| Release (Codemagic auto-sign) | Codemagic dashboard'da | **Ha, albatta** |
| Release (o'z keystore'ingiz) | Sizning `.jks` faylingiz | **Ha, albatta** |

---

## SHA-1 / SHA-256 kalitlarini qanday topish

### 1. Debug kaliti (lokal qurilma / test uchun)

Terminal yoki CMD da:

```bash
keytool -list -v \
  -keystore ~/.android/debug.keystore \
  -alias androiddebugkey \
  -storepass android \
  -keypass android
```

Windows'da `~/.android/` o'rniga `C:\Users\<siz>\.android\` yozing.

Natijada `SHA1:` va `SHA256:` qatorlarini ko'rasiz.

---

### 2. Codemagic auto-generated release kaliti

Codemagic o'z keystorini avtomatik yaratishi mumkin. SHA kalitini topish uchun:

1. [codemagic.io](https://codemagic.io) → loyihangizni oching
2. **App settings** → **Distribution** → **Android code signing**
3. Agar **"Automatically manage signing"** tanlangan bo'lsa:
   - **Build logs** → `gradlew signingReport` qatorini qidiring
   - Yoki build artifact sifatida `.keystore` yuklab, quyidagi buyruq bilan SHA oling:
     ```bash
     keytool -list -v -keystore <fayl.keystore> -alias <alias> -storepass <parol>
     ```
4. Agar **o'z keystoringizni** yuklagan bo'lsangiz — o'sha fayldan SHA oling (quyidagi bo'lim).

---

### 3. O'z release keystoringizdan SHA olish

```bash
keytool -list -v \
  -keystore /path/to/your-release.jks \
  -alias <alias_nomi> \
  -storepass <keystore_paroli>
```

`alias_nomi` va `keystore_paroli` — keystore yaratganingizda o'zingiz belgilagan qiymatlar.

---

## Firebase Console'ga SHA qo'shish

1. [console.firebase.google.com](https://console.firebase.google.com) → loyihangiz
2. **Project settings** (tishli g'ildirak belgisi) → **Your apps** → Android ilovangiz
3. **Add fingerprint** → SHA-1 yoki SHA-256 ni yozing → **Save**
4. **`google-services.json`** ni qayta yuklab oling → loyihaga (`android/app/`) qo'ying → GitHub'ga push qiling

> ⚠️ `google-services.json` ni yangilamasdan qo'shilgan SHA ishlaMaydi!

---

## Tekshirish ro'yxati (Google login ishlamasa)

- [ ] Codemagic release keystori SHA-1 Firebase'ga qo'shilganmi?
- [ ] `google-services.json` yangi SHA bilan yangilanganmi?
- [ ] `AndroidManifest.xml` da `INTERNET` va `ACCESS_NETWORK_STATE` ruxsatlari bormi?
- [ ] Firebase Console'da **Authentication → Sign-in method → Google** yoqilganmi?
- [ ] Firebase Console'da **SHA sertifikat barmaqlari** bo'sh emasmi?

---

## Loyiha tuzilmasi

```
lib/
  screens/       # UI ekranlar (home, player, admin, auth)
  services/      # Firebase auth, Firestore, storage xizmatlari
  models/        # Ma'lumot modellari
  widgets/       # Qayta ishlatiladigan UI komponentlar
android/
  app/src/main/AndroidManifest.xml   # Internet ruxsatlari shu yerda
  app/google-services.json           # Firebase konfiguratsiya
codemagic.yaml                       # CI/CD sozlamalari
```
