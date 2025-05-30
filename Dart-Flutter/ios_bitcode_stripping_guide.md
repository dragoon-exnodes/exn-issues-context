
# 📦 iOS Bitcode Stripping Guide for Flutter Plugins

This guide documents how to strip bitcode from third-party iOS frameworks (especially `.xcframework`) in a Flutter plugin or app to successfully upload to TestFlight or the App Store.

---

## ❗ Problem

When submitting your Flutter iOS app to TestFlight, you may encounter this error:

```
Invalid Executable. The executable 'Runner.app/Frameworks/XYZ.framework/XYZ' contains bitcode.
```

This happens when the included framework was built with bitcode, which Apple no longer allows for app submissions.

---

## ✅ Solution Overview

We need to strip the bitcode from the final built frameworks **before archiving**.

### 🛠 Step-by-step

---

### 1. Create a Strip Script

Create a file in the iOS folder of your app (not the plugin):

**`ios/strip_bitcode.sh`**
```bash
#!/bin/bash

echo "🔧 Starting bitcode strip process..."

APP_PATH="${TARGET_BUILD_DIR}/${EXECUTABLE_FOLDER_PATH}"
echo "📂 App path: $APP_PATH"

BINARIES=(
  "$APP_PATH/Frameworks/CryptoSwift.framework/CryptoSwift"
  "$APP_PATH/Frameworks/JOSESwift.framework/JOSESwift"
  "$APP_PATH/Frameworks/NuveiSimplyConnectSDK.framework/NuveiSimplyConnectSDK"
)

for bin in "${BINARIES[@]}"; do
  if [ -f "$bin" ]; then
    echo "🚫 Stripping bitcode from $bin"
    xcrun bitcode_strip -r "$bin" -o "$bin"
  else
    echo "❌ Not found: $bin"
  fi
done
```

Make it executable:

```bash
chmod +x ios/strip_bitcode.sh
```

---

### 2. Add Script to Xcode Build Phases

1. Open `ios/Runner.xcworkspace` in Xcode
2. Select your project > `Runner` target
3. Go to **Build Phases**
4. Click ➕ and choose **New Run Script Phase**
5. Move it to the **bottom**
6. Paste:

```bash
bash "${PROJECT_DIR}/strip_bitcode.sh"
```

---

### 3. Clean and Rebuild

```bash
flutter clean
flutter pub get
cd ios
pod install
flutter build ipa
```

---

### 4. Validate Result

After build, check that bitcode was stripped:

```bash
otool -l build/ios/ipa/Payload/Runner.app/Frameworks/CryptoSwift.framework/CryptoSwift | grep __LLVM
```

No output = ✅ bitcode removed

---

### ⚠️ Optional: dSYM Warning

You might still see this warning:

```
Upload Symbols Failed: dSYM missing for XYZ.framework
```

This does **not block** the upload — only affects crash log symbolication.

---

## 📌 Tips

- Don't run the strip script manually — it relies on Xcode's build environment.
- Always check `.framework` or `.xcframework` binaries for bitcode before submission.

---

## ✅ Summary

| Step | Description |
|------|-------------|
| Strip Script | Custom shell script removes bitcode from built frameworks |
| Build Phase | Run the script **after build** in Xcode |
| Validate | Use `otool` to confirm bitcode is stripped |

---

Enjoy smooth uploads to TestFlight 🚀
