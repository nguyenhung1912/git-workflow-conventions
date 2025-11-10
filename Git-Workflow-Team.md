# Quy trình làm việc với Git Flow cho Team

Tài liệu này hướng dẫn quy trình làm việc chuẩn khi sử dụng Git cho dự án của team. Yêu cầu mọi thành viên đọc kỹ và tuân thủ để đảm bảo code được quản lý nhất quán và giảm thiểu xung đột.

## Mục lục

1. [Cài đặt lần đầu](#1-cài-đặt-lần-đầu)
2. [Các nhánh chính & Quy ước](#2-các-nhánh-chính--quy-ước)
3. [Quy ước đặt tên nhánh](#3-quy-ước-đặt-tên-nhánh)
4. [Quy trình làm việc (Workflow)](#4-quy-trình-làm-việc-workflow)
5. [Hướng dẫn viết Commit Message](#5-hướng-dẫn-viết-commit-message)
6. [Xử lý sự cố thường gặp](#6-xử-lý-sự-cố-thường-gặp)
7. [Sau khi PR được duyệt (Dọn dẹp)](#7-sau-khi-pr-được-duyệt-dọn-dẹp)

---

## 1. Cài đặt lần đầu

Nếu đây là lần đầu tiên làm việc với Git, cần cấu hình thông tin cá nhân. Đây là bước bắt buộc.

```bash
# Clone repository của dự án về máy
git clone <đường-dẫn-repo>

# Đi vào thư mục dự án vừa clone
cd <tên-thư-mục-dự-án>

# Cấu hình thông tin cá nhân (chỉ cần làm 1 lần)
git config --global user.name "Tên Của Bạn"
git config --global user.email "email@cua-ban.com"

# Kiểm tra lại thông tin
git config --list
```

## 2. Các nhánh chính & Quy ước

Chúng ta có 2 nhánh chính bất khả xâm phạm và các nhánh để phát triển tính năng:

### `master` (hoặc `main` - ở đây là `master`)

- **Mục đích**: Nhánh chứa code đã chạy ổn định, sẵn sàng để triển khai lên môi trường Production (cho người dùng cuối).
- **Quy tắc**: TUYỆT ĐỐI KHÔNG commit hay push trực tiếp lên nhánh này. Mọi thay đổi phải đi qua Pull Request từ nhánh `dev`.
- Nhánh này không được có commit code lỗi.

### `dev`

- **Mục đích**: Nhánh phát triển (Integration). Đây là nơi hợp nhất tất cả các tính năng mới từ team để kiểm thử chung.
- **Quy tắc**: Không push trực tiếp. Mọi tính năng mới phải được merge vào `dev` thông qua Pull Request.

### `feature/...` (Nhánh tính năng)

- **Mục đích**: Nhánh cá nhân để phát triển một tính năng cụ thể (ví dụ: login, payment, ...).
- **Quy tắc**: Nhánh này được tạo từ `dev` và sẽ được merge lại vào `dev` khi hoàn thành.

## 3. Quy ước đặt tên nhánh

Để dễ dàng quản lý, chúng ta thống nhất cách đặt tên nhánh feature như sau:

**Cú pháp**: `yourname/type-feature-description`

- **yourname**: Tên của bạn (viết liền, không dấu).
- **type**: Loại công việc (`feat` cho tính năng mới, `fix` cho sửa lỗi).
- **feature-description**: Mô tả ngắn về tính năng.

**Ví dụ**:

- `nguyenhung/feat-login-page`
- `abc/fix-payment-bug`

## 4. Quy trình làm việc (Workflow)

Đây là vòng lặp công việc sẽ thực hiện mỗi khi bắt đầu một tác vụ mới.

### Giai đoạn 1: Bắt đầu một tính năng mới

Khi nhận một task mới, làm theo các bước sau:

```bash
# 1. Chuyển về nhánh dev
git checkout dev  # (trường hợp đã có nhánh)
git checkout -b dev  # (trường hợp chưa có nhánh)

# 2. Luôn kéo code mới nhất từ dev trên remote về
git pull origin dev

# 3. Tạo nhánh feature mới từ dev
# (Thay tên nhánh bằng tên theo quy ước ở Mục 3)
git checkout -b nguyenhung/feat-login-page
```

Bây giờ đã có nhánh của riêng mình và có thể bắt đầu code.

### Giai đoạn 2: Làm việc hàng ngày (Code & Commit)

Trong khi làm việc trên nhánh feature của mình:

```bash
# 1. Thêm file vừa chỉnh sửa vào "staging" (vùng tạm trong git để chờ commit)
# LƯU Ý: Hạn chế dùng "git add ."
# Hãy add từng file cụ thể để kiểm soát commit
git add src/controllers/index.php
git add src/views/login.php

# 2. Commit công việc đã làm với nội dung rõ ràng
# (team mình thống nhất nội dung commit là tiếng anh nhé)
git commit -m "feat: hoàn thành giao diện trang login"

# 3. Push code lên remote để sao lưu và cho team thấy
git push origin nguyenhung/feat-login-page
```

**CHÚ Ý**: Commit nhỏ, commit thường xuyên. Đừng để code cả ngày mới commit một lần.

### Giai đoạn 3: Hoàn thành tính năng & Tạo Pull Request (PR)

Khi đã code xong tính năng và đã tự test kỹ trên máy local, hãy chuẩn bị tạo Pull Request (PR) để merge vào `dev`.

```bash
# 1. Commit và push tất cả công việc cuối cùng
git add .
git commit -m "feat: hoàn thành tính năng login"
git push origin nguyenhung/feat-login-page

# 2. Quay về nhánh dev và kéo code mới nhất
# (Để làm gì? Có thể có thành viên trong team đã merge code mới vào dev)
git checkout dev
git pull origin dev

# 3. Quay lại nhánh feature
git checkout nguyenhung/feat-login-page

# 4. Đồng bộ code mới nhất từ dev vào nhánh của bạn
git merge dev
```

- **Nếu có CONFLICT**: Git sẽ báo. Xem [Mục 6](#6-xử-lý-sự-cố-thường-gặp) để biết cách xử lý.
- **Nếu không có conflict** (hoặc sau khi đã giải quyết):

```bash
# 5. Chạy lại web/app để TEST LẦN CUỐI
# (Đảm bảo code của bạn không phá vỡ code mới của team)

# 6. Push phiên bản cuối cùng đã merge lên remote
git push origin nguyenhung/feat-login-page
```

### Bước cuối: Tạo Pull Request trên GitHub

1. Vào repository trên GitHub.
2. Sẽ thấy một thông báo đề xuất tạo Pull Request cho nhánh bạn vừa push.
3. Click **"Compare & Pull Request"**.
4. Chọn: **Base**: `dev` ← **Compare**: `nguyenhung/feat-login-page`
5. Viết Tiêu đề và Mô tả rõ ràng (mô tả bạn đã làm gì, có ảnh hưởng gì không) - không cần để trống phần này cũng được.
6. Tag reviewer (Leader) vào để review code.
7. Click **"Create Pull Request"**.

## 5. Hướng dẫn viết Commit Message

Để giữ lịch sử commit sạch sẽ, hãy tuân thủ quy ước **Conventional Commits**:

**Cú pháp**: `type: mô tả công việc`

- `feat:` - Thêm tính năng mới
- `fix:` - Sửa lỗi
- `docs:` - Thay đổi tài liệu, README
- `style:` - Chỉnh sửa code style: thụt lề, dấu chấm phẩy,... không ảnh hưởng logic
- `refactor:` - Tái cấu trúc code, không thêm/sửa tính năng
- `test:` - Thêm/sửa test cases

**Ví dụ**:

- `feat: thêm chức năng đăng ký người dùng`
- `fix: lỗi sai mật khẩu khi đăng nhập`
- `docs: cập nhật file README.md`

## 6. Xử lý sự cố thường gặp

### 6.1. Khi có Merge Conflict

Xung đột (conflict) xảy ra khi bạn `git merge dev` (Giai đoạn 3) hoặc khi 2 người cùng sửa 1 file.

```bash
# 1. Git sẽ báo file nào bị conflict. Dùng lệnh sau để xem:
git status

# 2. Mở file bị conflict (ví dụ: "index.html")
# Sẽ thấy các dấu:
# <<<<<<< HEAD (Đây là code của BẠN)
# =======
# (Đây là code từ nhánh dev)
# >>>>>>> dev

# 3. Nhiệm vụ của bạn:
# Xóa các dấu <<< === >>>
# và CHỈNH SỬA code trong file đó thành phiên bản cuối cùng chính xác nhất
# (kết hợp cả code của bạn và của dev).

# 4. Sau khi sửa xong và lưu file:
git add .
git commit -m "fix: resolve merge conflicts"

# 5. Tiếp tục quy trình (push,...)
```

### 6.2. Khi quên Pull trước khi Code (hoặc code dở thì cần Pull)

Đang code dở nhưng sực nhớ ra cần pull code mới từ `dev` về.

```bash
# 1. Lưu lại công việc đang làm dở
git stash

# 2. Chuyển sang dev và kéo code mới
git checkout dev
git pull origin dev

# 3. Quay lại nhánh của bạn
git checkout nguyenhung/feat-login-page

# 4. Đồng bộ với dev
git merge dev

# 5. Lấy lại code đang làm dở lúc nãy
git stash pop

# 6. (Có thể) bạn sẽ gặp conflict. Hãy giải quyết như Mục 6.1
```

## 7. Sau khi PR được duyệt (Dọn dẹp)

Sau khi Pull Request của bạn đã được Leader duyệt và merge vào `dev`:

```bash
# 1. Quay về nhánh dev
git checkout dev

# 2. Kéo code mới nhất (bao gồm cả PR vừa merge của bạn)
git pull origin dev

# 3. Xóa nhánh feature ở máy local vì đã xong
git branch -d nguyenhung/feat-login-page

# 4. (Tùy chọn) Xóa nhánh feature trên remote
git push origin --delete nguyenhung/feat-login-page
```

---

**Lưu ý**: Hãy luôn tuân thủ quy trình này để đảm bảo code được quản lý tốt và giảm thiểu xung đột trong team! 🚀
