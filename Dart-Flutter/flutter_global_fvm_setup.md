# Thiết lập Flutter Global bằng FVM

FVM (Flutter Version Management) giúp bạn cài và quản lý nhiều phiên bản Flutter trên cùng một máy. Hướng dẫn này sẽ giúp bạn thiết lập phiên bản Flutter global (dùng được ở mọi nơi) thông qua FVM mà không cần cài đặt Flutter thủ công.

---

## 1. Cài đặt FVM

Nếu bạn chưa cài FVM, hãy cài đặt bằng cách:

```bash
dart pub global activate fvm
```

Thêm FVM vào PATH nếu cần:

```bash
export PATH="$PATH":"$HOME/.pub-cache/bin"
```

---

## 2. Cài đặt phiên bản Flutter mong muốn

Ví dụ, để cài bản `stable` mới nhất:

```bash
fvm install stable
```

Hoặc cài bản cụ thể:

```bash
fvm install 3.19.0
```

---

## 3. Thiết lập Flutter global

Sau khi cài xong, thiết lập global:

```bash
fvm global stable
```

Hoặc:

```bash
fvm global 3.19.0
```

---

## 4. Cấu hình alias `flutter` cho terminal (Zsh hoặc Bash)

### Với Zsh:

```bash
echo 'alias flutter="fvm flutter"' >> ~/.zshrc
source ~/.zshrc
```

### Với Bash:

```bash
echo 'alias flutter="fvm flutter"' >> ~/.bashrc
source ~/.bashrc
```

---

## 5. Kiểm tra lại

```bash
flutter --version
```

Kết quả sẽ hiển thị version Flutter do FVM quản lý. Bạn có thể sử dụng `flutter` ở bất kỳ đâu trong terminal.

---

## 6. Cập nhật bản Flutter stable mới nhất

Để cập nhật phiên bản Flutter stable mới nhất:

```bash
fvm install stable --force
fvm global stable
```

---

## Gỡ lỗi

- Nếu lệnh `flutter` không hoạt động, hãy kiểm tra alias:
  
  ```bash
  type flutter
  ```

- Nếu dùng script `.sh`, hãy gọi `fvm flutter` thay vì `flutter`.

---

## Tham khảo

- [FVM GitHub](https://github.com/fvmapp/fvm)
- [Flutter SDK Releases](https://docs.flutter.dev/release/whats-new)
