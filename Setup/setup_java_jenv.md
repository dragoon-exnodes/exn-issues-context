# ☕ Hướng dẫn cài đặt Java & jenv trên macOS (Chuẩn Flutter, Android Studio)

## ✅ Mục tiêu

- Cài đặt Java 17 (Temurin JDK)
- Cài `jenv` để quản lý nhiều version Java
- Thiết lập `JAVA_HOME` đúng cách (dùng cho Flutter, Android Studio, Gradle, v.v.)

---

## 🛠️ Bước 1: Gỡ Java cũ (nếu có)

```bash
sudo rm -rf /Library/Java/JavaVirtualMachines/*
```

---

## ☕ Bước 2: Cài Java 17 (Temurin .pkg)

1. Truy cập: https://adoptium.net/en-GB/temurin/releases/?version=17
2. Chọn:
   - **Operating system:** macOS
   - **Architecture:**
     - Chip Intel → `x64`
     - Chip Apple Silicon (M1/M2/M3) → `aarch64`
   - **Package type:** Installer `.pkg`
3. Tải về và double-click cài đặt

---

## 🔍 Bước 3: Kiểm tra Java

```bash
java --version
```

> ✅ Kết quả ví dụ:
> ```
> openjdk 17.0.x Temurin
> ```

---

## 🔧 Bước 4: Cài jenv (quản lý Java version)

```bash
brew install jenv
```

Sau đó thêm vào file `~/.zshrc`:

```bash
export PATH="$HOME/.jenv/bin:$PATH"
eval "$(jenv init -)"
```

Rồi reload shell:

```bash
source ~/.zshrc
```

---

## ➕ Bước 5: Thêm Java vào jenv

```bash
jenv add /Library/Java/JavaVirtualMachines/temurin-17.jdk/Contents/Home
```

Kiểm tra version đã thêm:

```bash
jenv versions
```

---

## 🌍 Bước 6: Chọn version Java mặc định

```bash
jenv global 17
```

> (Hoặc `jenv global 17.0.x` tùy version hiển thị)

---

## 📢 Bước 7: Bật plugin `export` để set JAVA_HOME

```bash
jenv enable-plugin export
source ~/.zshrc
```

---

## ✅ Bước 8: Kiểm tra hoàn tất

```bash
java --version
echo $JAVA_HOME
which java
```

> ✅ Kết quả mong muốn:
> - `JAVA_HOME` = `/Users/your_user/.jenv/versions/17`
> - `java` = `~/.jenv/shims/java`

---

## 🛠 Nếu Android Studio không nhận Java

Chạy:

```bash
cd /Applications/Android\ Studio.app/Contents
sudo ln -s jbr jre
```

---

## ✅ Done! Giờ bạn có thể dùng Java trong:

- Android Studio
- Flutter (`flutter doctor`)
- Gradle
- Bất kỳ tool nào cần Java

---

## 🚀 Bonus: Thêm nhiều version Java (nếu muốn)

```bash
brew install openjdk@11
jenv add /opt/homebrew/opt/openjdk@11
jenv global 11
```

→ Có thể đổi version bằng:

```bash
jenv global 17   # hoặc jenv local 17 trong thư mục project
```