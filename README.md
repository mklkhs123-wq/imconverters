# IMConverter — Android App (Kotlin)

**Package name:** `com.menutechnology.imconverter`
**Support email:** shrimenui123@gmail.com

100% offline (except PDF-password library ke liye pehli baar internet chahiye Gradle sync mein — koi AI/tracking/analytics nahi).


## App Flow
**Home Screen** → top header ya "Single Image" card → **Choose Photo** popup (Camera/Pick Image/Photos/Gallery) → **Editor Screen**

## Editor Screen Features
- **Before/After live preview** — dimensions + file size turant update
- **Compress Photo** — Percentage Level (slider) ya File Size (target KB)
- **Resize Resolution** — 3 modes:
  - **Automatic** — compress % ke hisaab se dimensions scale hote hain
  - **Keep Original** — original size, resize nahi hota
  - **Custom** — apna width/height do (maintain aspect ratio option ke saath)
- **Convert Format** — JPG / PDF / PNG / WEBP / **GIF** / **BMP**
  - GIF aur BMP ke liye custom encoder likha gaya hai (Android mein native support nahi hai in formats ka)
  - **PNG** select karo to "Preserve Transparency" checkbox aata hai
  - **JPG** select karo to "Keep EXIF Data" checkbox aata hai (camera info, GPS, date waghera original photo se copy hoti hai)
  - **PDF** select karo to "Protect with Password" checkbox aata hai — asli password-protected PDF banta hai (PDFBox-Android library use ki hai, kyunki Android ka built-in PDF tool encryption support nahi karta)
- **Crop Photo**, **Change Photo**, filename edit, **Confirm (✓)** save

## ⚠️ Zaroori Note — PDF Password Feature
Ye feature ek external library (`com.tom-roush:pdfbox-android`) use karta hai jo **JitPack** se download hoti hai. Iske liye:
1. Pehli Gradle sync ke waqt internet zaroor ON rakho
2. Agar sync fail ho ya "Could not resolve" jaisa error aaye, to `settings.gradle` mein JitPack repository check karo, ya PDF-password wala hissa hata ke bina-password PDF use karo

## GIF Quality Note
GIF ek "palette" format hai (max 256 colors) — normal photos mein thoda banding (color steps) dikh sakta hai. Ye GIF format ki hi limitation hai, koi bug nahi. Simple graphics/screenshots ke liye best result milega.

## Build Karne Ka Tarika (Android Studio)
1. Android Studio open karo → `File → Open` → `ImageConverterApp` folder select karo
2. Gradle sync hone do (internet chahiye — AndroidX + PDFBox library download hongi)
3. Device/emulator select karke **Run ▶** dabao

## Project Structure
```
app/src/main/java/com/example/imageconverter/
├── HomeActivity.kt
├── ChoosePhotoActivity.kt
├── EditorActivity.kt         (single-image editor — sab features yahan hain)
├── ImageEncoders.kt          (custom BMP + GIF encoders)
├── CropActivity.kt + CropOverlayView.kt
├── MainActivity.kt           (batch/multiple images converter)
├── ResultFolderActivity.kt
└── SettingsActivity.kt
```

## 🐛 Bug Fixes (latest update)
1. **Crash on Crop (gallery/camera)** — bade photos (12MP+) load karte waqt app OutOfMemoryError se crash ho rahi thi. Fix: sabhi image-loading jagah par ab automatic downsampling hoti hai (max ~2200px), jo memory crash rokta hai aur processing bhi tez karta hai.
2. **Crop lag/slow** — bitmap loading ab background thread (coroutine) par hoti hai, UI thread block nahi hota, aur loading spinner dikhta hai.
3. **PDF password crash** — Save operation ab poori tarah try-catch aur background thread mein wrapped hai. Agar PDF encryption mein koi error aaye, ab app crash nahi hogi — ek error message dikhega instead.

Agar koi bhi crash phir bhi aaye, Android Studio ke "Logcat" tab mein error dekh ke bata dena — uska stack trace exact fix batane mein madad karega.

## 🐛 More Bug Fixes
4. **Crop fails only for gallery/pick-image (not camera)** — gallery se aayi image ka access permission 2-3 screens ke baad expire ho jaata tha (khaaskar MIUI/Xiaomi gallery apps par). Fix: image select hote hi turant apni app ki cache mein copy kar li jaati hai, permission issue permanently solve.
5. **PDF password save failing** — encryption ke liye zaroori BouncyCastle crypto library missing thi, add kar di gayi hai. Agar phir bhi fail ho, ab error message mein exact wajah dikhegi (pehle generic message tha).

## 🐛 "Primary directory Pictures not allowed" Fix
Android ke storage rules PDF/document files ko "Pictures" folder mein jaane nahi dete (wo sirf photos/videos ke liye reserved hai). Isliye ab **PDF files `Download/ImageConverter` folder mein save hoti hain**, images (JPG/PNG/WEBP/GIF/BMP) hamesha ki tarah `Pictures/ImageConverter` mein. Result Folder screen dono jagah check karta hai, sab kuch ek hi jagah dikhega.

## ✨ New: Rotate & Flip in Crop Screen
Crop screen ke bottom panel mein ab 4 naye icon buttons hain: **Rotate Left**, **Rotate Right**, **Flip Horizontal**, **Flip Vertical**. In se image ko crop karne se pehle hi orient kar sakte ho — crop box har rotate/flip ke baad automatically center mein reset ho jaata hai.

## 🎨 Crop Screen Redesign
Crop screen ab naye style mein hai:
- Top par **circular X (close)** aur **✓ (confirm)** buttons — text buttons ki jagah
- Crop box par **corner-bracket markers** (L-shape) — chhote circles ki jagah
- Bottom mein **5 circular icon buttons**: Rotate Left, Rotate Right, Flip Horizontal, Flip Vertical, Reset Crop
- Aspect ratio pills (Free/1:1/4:3/16:9) upar hi hain, thoda subtle

## ✨ New: Save Result Screen
Editor mein **Confirm (✓)** dabate hi ab seedha Home nahi jaate — pehle ek **"Saved" screen** khulta hai:
- Image preview (format badge upar-right, size + dimensions bottom-right overlay ki tarah)
- **Path** aur **Size** details
- 4 action buttons: **Save** (confirmation), **Export** (custom location par copy — Downloads, Google Drive, SD card, kahin bhi), **Share** (seedha WhatsApp/Gmail se), **Delete** (file hata do)

Note: Rating/Premium/Ad wala hissa jaan-boojh kar nahi rakha (humara app free/offline hai, koi monetization nahi).

## 🔔 New: Save Notification
File save hote hi ab **turant ek notification** aati hai — gallery mein jaakar dhundhne ki zaroorat nahi:
- **Single Image save** → notification par tap karte hi wahi image/PDF seedha open ho jaati hai
- **Multiple Images batch convert** → notification par tap karte hi Result Folder screen khulta hai (saari files ki list)

Android 13+ par pehli baar notification permission maangi jaayegi — allow karna zaroori hai, warna notification nahi dikhegi (lekin file phir bhi save ho jaayegi, sirf notification skip hogi).

## ⚙️ New: Full Settings Screen
Home screen ke gear icon se ab ek poora **Settings** screen khulta hai:

**Tools Related**
- **Saved Path** — dikhata hai images kahan (Pictures/ImageConverter) aur PDF kahan (Download/ImageConverter) save hoti hain
- **Photo Speed Up** — ON karne par kam resolution (1400px) par process karta hai, jo tez hai par thodi kam sharp; OFF (default) mein 2200px, better quality
- **Clear Export Paths** — app ke saved export permissions clear karta hai
- **Prevent Duplicate Photos** — ON karne par agar same image dobara save karo, ek heads-up notification/toast aata hai ("ye pehle bhi save kiya tha jaisa lagta hai") — file phir bhi save hoti hai, bas warning milti hai
- **Default Configurations** — sab settings (Dark Mode, Speed Up, Prevent Duplicate) wapas default par reset kar deta hai

**App Related**
- **Report Bugs / Contact Us** — koi bhi email/notes/WhatsApp app khol ke feedback bhej sakte ho
- **Share** — app ke baare mein message share karo
- **Rate Us** — abhi honest message dikhata hai ("Play Store par nahi hai") — jab publish karoge, real link daal dena
- **Privacy Policy** — in-app dialog, accurate content (offline, no tracking, no ads)

**Jaan-boojh kar skip kiya:** "More apps" aur ad-banner wala hissa — kyunki humare paas doosri apps nahi hain aur app mein ads nahi honi chahiye.

## ✨ New: Choose Photo popup for Multiple Images too
Pehle ye popup (Camera/Pick Image/Photos/Gallery) sirf **Single Image** mode mein aata tha. Ab **Multiple Images** card se bhi yahi popup khulta hai:
- **Camera** → ek photo click karo, seedha selection mein add
- **Pick Image / Gallery** → ab **multi-select** support karte hain (ek saath kai images choose kar sakte ho)
- **Photos** → device support karne par multi-select karta hai
- Select karte hi seedha **Multiple Images converter screen** khulta hai, images pehle se load hoti hui — "Select Images" button dabane ki zaroorat nahi

## 🎨 Multiple Images Screen Redesign
Ab "Multiple Images" screen bhi bilkul Single Image Editor jaisa hi dikhta hai:
- **Before/After** — dono taraf saari images ke thumbnails + total count + total size (live update hota hai settings badalte hi)
- **Compress Photo** — Percentage Level / File Size (per-image target)
- **Resize Resolution** — Automatic / Keep Original / Custom (3-option dialog, single editor jaisa hi)
- **Convert Format** — JPEG/WEBP/PNG/GIF/BMP/PDF (PDF select karne par "Combine into ONE PDF" checkbox)
- **Keep Exif Data** — sirf JPEG format ke liye dikhta hai, (?) icon se explanation
- **Bottom bar**: Crop Photo (sirf jab exactly 1 image selected ho), Add Photos (aur images add karo), Confirm (✓ — batch convert start karta hai)

Batch convert hone ke baad progress bar + status dikhta hai, aur poora hone par **notification** aati hai (jaisa pehle add kiya tha) jisse Result Folder khulta hai.

## 🔐 Release Signing Setup (Play Store ke liye zaroori)

Play Store ko ek **signed** app chahiye. Ye ek baar ka setup hai — uske baad har release build automatically sign ho jaayega.

### Step 1: Keystore banao (sirf ek baar, life mein ek hi baar karna hai)
1. Android Studio mein: **Build → Generate Signed Bundle / APK**
2. **Android App Bundle** select karo → Next
3. "Key store path" ke neeche **Create new...** dabao
4. Ek jagah select karo apna keystore file save karne ke liye (jaise `release-key.jks`) — **isko safe rakhna, kabhi mat kho na**
5. Password set karo (keystore password + key password — same rakh sakte ho ya alag)
6. Alias naam do (jaise `imconverter-key`)
7. Validity **25 saal ya usse zyada** rakho (Play Store minimum 25 saal maangta hai future updates ke liye)
8. Apna naam/company details bhar do (optional fields)
9. **OK** dabao — keystore ban jaayega

### Step 2: keystore.properties banao
1. Project ke root folder mein `keystore.properties.template` file hai — usko copy karke naam do **`keystore.properties`** (same folder mein)
2. Usme apne real values daal do:
   ```
   storeFile=release-key.jks
   storePassword=<aapka password>
   keyAlias=<aapka alias>
   keyPassword=<aapka key password>
   ```
3. Agar keystore file kahin aur rakhi hai (project ke bahar), to `storeFile` mein poora path daalna (jaise `C:\\Users\\naam\\keystores\\release-key.jks`)

**Zaroori:** `keystore.properties` aur `.jks` file **kabhi GitHub/kahin share mat karna** — ye already `.gitignore` mein add hai, safe hai.

### Step 3: Signed App Bundle banao
1. **Build → Generate Signed Bundle / APK** → Android App Bundle
2. Ab keystore details khud-ba-khud fill ho jaayengi (agar Step 2 sahi se kiya)
3. **release** build variant select karo → Finish
4. `.aab` file `app/release/` folder mein ban jaayegi — **yahi file Play Store par upload karni hai**

