# Hướng Dẫn Sử Dụng Variable Fonts Trong Flutter Với FontVariation

Date: March 6, 2025
Difficulty Level: Medium
Status: Completed

## Mục đích

Tài liệu này hướng dẫn cách khắc phục vấn đề khi Flutter không nhận diện đúng `fontWeight` và `style` của một variable font (font biến thể) khi nhúng vào ứng dụng. Giải pháp sử dụng thuộc tính `FontVariation` của `TextStyle` để điều khiển các trục (axes) của variable font.

**Ngày cập nhật**: 06/03/2025

**Phiên bản Flutter áp dụng**: Flutter 3.x trở lên

---

## Vấn đề

Khi nhúng một file variable font (ví dụ: `Altform.ttf`) chứa nhiều style (weight từ 100 đến 900, kèm italic) vào Flutter thông qua `pubspec.yaml` như sau:

```yaml
fonts:
  - family: Altform VF
    fonts:
      - asset: assets/fonts/altform-vf/Altform.ttf
        weight: 100
      - asset: assets/fonts/altform-vf/Altform.ttf
        weight: 700
      - asset: assets/fonts/altform-vf/Altform.ttf
        weight: 900
        style: italic
```

Flutter không nhận diện được các `weight` và `style` khác nhau từ cùng một file `.ttf`. Kết quả là ứng dụng chỉ hiển thị weight/style mặc định của font, bất kể giá trị được chỉ định.

---

## Nguyên nhân

- **Variable Fonts**: Đây là loại font chứa nhiều biến thể (weight, italic, width, v.v.) trong một file duy nhất, sử dụng các trục (axes) như `wght` (weight), `ital` (italic).
- **Hạn chế của Flutter**: Flutter không tự động ánh xạ các giá trị `weight` và `style` trong `pubspec.yaml` sang các trục của variable font. Cách khai báo truyền thống chỉ phù hợp với static fonts (font tĩnh).

---

## Giải pháp

Sử dụng thuộc tính `fontVariations` trong `TextStyle` để điều khiển trực tiếp các trục của variable font.

### Bước 1: Khai báo font trong `pubspec.yaml`

Chỉ cần khai báo file font một lần, không cần liệt kê từng weight/style:

```yaml
flutter:
  fonts:
    - family: Altform VF
      fonts:
        - asset: assets/fonts/altform-vf/Altform.ttf
```

- **Lưu ý**: Đảm bảo đường dẫn `assets/fonts/altform-vf/Altform.ttf` chính xác và file đã được thêm vào thư mục `assets`.

### Bước 2: Sử dụng `FontVariation` trong mã Dart

Dùng `TextStyle` với `fontVariations` để chỉ định giá trị cho trục `wght` (weight) hoặc các trục khác (nếu font hỗ trợ).

### Ví dụ cơ bản

```dart
Text(
  'Xin chào Flutter!',
  style: TextStyle(
    fontFamily: 'Altform VF',
    fontVariations: [
      FontVariation('wght', 700.0), // Weight 700
    ],
  ),
),
```

### Ví dụ với danh sách weight động

Tạo một danh sách các `Text` widget với các weight từ 100 đến 900:

```dart
Column(
  children: FontWeight.values.map(
    (weight) => Text(
      'This text has weight $weight',
      style: TextStyle(
        fontFamily: 'Altform VF',
        fontVariations: [
          FontVariation('wght', ((weight.index + 1) * 100).toDouble()),
        ],
      ),
    ),
  ).toList(),
),
```

- **Giải thích**:
    - `FontWeight.values`: Trả về `[w100, w200, ..., w900]`.
    - `weight.index`: Chỉ số từ 0 đến 8.
    - `((weight.index + 1) * 100).toDouble()`: Chuyển thành các giá trị 100, 200, ..., 900.

### Ví dụ kết hợp italic

Nếu font hỗ trợ trục `ital`:

```dart
Text(
  'This text is bold and italic',
  style: TextStyle(
    fontFamily: 'Altform VF',
    fontVariations: [
      FontVariation('wght', 700.0), // Weight 700
      FontVariation('ital', 1.0),   // Italic (1.0 = bật, 0.0 = tắt)
    ],
  ),
),
```

---

## Kiểm tra và tối ưu

### 1. Xác nhận trục hỗ trợ của font

- Dùng công cụ như **FontForge** hoặc **Google Fonts** để kiểm tra các trục mà font hỗ trợ (ví dụ: `wght`, `ital`, `wdth`).
- Nếu font không hỗ trợ trục mong muốn (như `ital`), bạn có thể cần file font riêng cho italic hoặc chuyển sang static fonts.

### 2. Tối ưu hiệu suất

- Tránh lạm dụng `fontVariations` với nhiều giá trị động không cần thiết để giảm tải xử lý.

### 3. Kết hợp với `FontWeight` (tuỳ chọn)

Dùng `fontWeight` làm fallback nếu `fontVariations` không hoạt động:

```dart
Text(
  'Fallback example',
  style: TextStyle(
    fontFamily: 'Altform VF',
    fontWeight: FontWeight.w700,
    fontVariations: [
      FontVariation('wght', 700.0),
    ],
  ),
),
```

---

## Lưu ý

- **Phiên bản Flutter**: Đảm bảo dùng Flutter 3.x trở lên để hỗ trợ tốt variable fonts.
- **Debug**: Nếu font không hiển thị đúng:
    1. Kiểm tra lại đường dẫn file trong `pubspec.yaml`.
    2. Chạy `flutter clean` và `flutter pub get`.
    3. Xác minh file font không bị lỗi bằng công cụ như **FontValidator**.

---

## Phương án dự phòng

Nếu variable font không hoạt động như mong đợi:

1. Tách file `.ttf` thành các static fonts riêng lẻ (dùng **FontSquirrel** hoặc **Transfonter**).
2. Khai báo từng file trong `pubspec.yaml`:

```yaml
fonts:
  - family: Altform VF
    fonts:
      - asset: assets/fonts/altform-vf/Altform-100.ttf
        weight: 100
      - asset: assets/fonts/altform-vf/Altform-700.ttf
        weight: 700
```

---

## Kết luận

Sử dụng `FontVariation` là cách hiệu quả để tận dụng variable fonts trong Flutter mà không cần tách file. Phương pháp này linh hoạt, tiết kiệm dung lượng và phù hợp với các font hiện đại.