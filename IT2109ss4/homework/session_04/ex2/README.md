# BÁO CÁO THỰC HÀNH: QUẢN LÝ NHÁNH VÀ GIẢI QUYẾT XUNG ĐỘT (MERGE CONFLICT)

- **Môn học:** IT2109 - Hệ thống và Công cụ Phát triển Phần mềm
- **Bài tập:** Bài 2 - Quản lý nhánh và Giải quyết xung đột (Merge Conflict)
- **Đường dẫn nộp bài:** `homework/session_04/ex2/README.md`

---

## 1. Mục tiêu bài thực hành
1. Thành thạo thao tác tạo mới, chuyển đổi và làm việc song song giữa các nhánh (`main` và `feature-update`).
2. Chủ động mô phỏng và tái hiện tình huống xung đột gộp nhánh (**Merge Conflict**) khi cả hai nhánh cùng sửa đổi trên một vùng nội dung của tệp `README.md`.
3. Nắm vững cơ chế gộp 3 vùng (**3-Way Merge**) của Git.
4. Rèn luyện kỹ năng xử lý xung đột thủ công, loại bỏ các ký hiệu đánh dấu (`<<<<<<<`, `=======`, `>>>>>>>`), hoàn tất commit gộp nhánh (**Merge Commit**), và kiểm tra đồ thị lịch sử commit.

---

## 2. Bối cảnh và Tái hiện xung đột (Merge Conflict)

### 2.1. Kịch bản thực tế
- Nhánh `main`: Đang lưu trữ mã nguồn và tài liệu chuẩn của dự án. Thành viên core team thực hiện cập nhật quy chuẩn tài liệu và thông tin phiên bản.
- Nhánh `feature-update`: Một lập trình viên tạo nhánh phụ để phát triển tính năng mới và đồng thời cập nhật mô tả tính năng vào đúng vị trí tiêu đề / mô tả trong tệp `README.md`.
- Khi gộp nhánh `feature-update` vào `main`, do cả hai nhánh đều có commit mới kể từ thời điểm phân nhánh và cùng chỉnh sửa một dòng trong tệp `README.md`, Git không thể tự động quyết định phiên bản nào là đúng và phát sinh **Merge Conflict**.

### 2.2. Trình tự các lệnh Git đã thực hiện

#### Bước 1: Khởi tạo commit ban đầu trên nhánh `main`
```bash
# Tạo và khởi tạo repository (nếu chưa có)
git init
git checkout -b main

# Tạo file README.md với nội dung ban đầu
echo "# Dự án Quản lý Thư viện số" > README.md
echo "Hệ thống quản lý sách và độc giả cho thư viện trường đại học." >> README.md

# Tạo commit ban đầu (Base commit)
git add README.md
git commit -m "docs: khoi tao du an va tep README.md"
```

#### Bước 2: Tạo nhánh `feature-update` và chỉnh sửa file
```bash
# Tạo và chuyển sang nhánh mới feature-update
git checkout -b feature-update

# Cập nhật nội dung mô tả tính năng mới trên nhánh feature-update
cat << 'EOF' > README.md
# Dự án Quản lý Thư viện số (Bản thử nghiệm)
Hệ thống quản lý sách và độc giả, bổ sung tính năng: Đặt mượn sách trực tuyến và tra cứu nâng cao.
EOF

# Ghi lại commit trên nhánh feature-update
git add README.md
git commit -m "feat: cap nhat mo ta tinh nang dat sach tren nhanh feature-update"
```

#### Bước 3: Chuyển về nhánh `main` và tạo thay đổi xung đột
```bash
# Chuyển về lại nhánh chính main
git checkout main

# Cập nhật nội dung trên cùng dòng/khối tại nhánh main
cat << 'EOF' > README.md
# Dự án Quản lý Thư viện số (Phiên bản v1.0.0 Enterprise)
Hệ thống quản lý sách và độc giả tích hợp bảo mật phân quyền đa cấp độ cho nhân viên thư viện.
EOF

# Ghi lại commit trên nhánh main
git add README.md
git commit -m "docs: cap nhat phien ban v1.0.0 va tinh nang bao mat tren main"
```

#### Bước 4: Thực hiện gộp nhánh và kích hoạt xung đột
```bash
# Đang ở nhánh main, tiến hành gộp nhánh feature-update
git merge feature-update
```

**Kết quả thông báo từ Git:**
```text
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

---

## 3. Phân tích Xung đột và Cơ chế 3-Way Merge

### 3.1. Các ký hiệu đánh dấu xung đột của Git
Khi mở tệp `README.md` trong quá trình xung đột, Git chèn các thẻ đánh dấu:

```markdown
<<<<<<< HEAD
# Dự án Quản lý Thư viện số (Phiên bản v1.0.0 Enterprise)
Hệ thống quản lý sách và độc giả tích hợp bảo mật phân quyền đa cấp độ cho nhân viên thư viện.
=======
# Dự án Quản lý Thư viện số (Bản thử nghiệm)
Hệ thống quản lý sách và độc giả, bổ sung tính năng: Đặt mượn sách trực tuyến và tra cứu nâng cao.
>>>>>>> feature-update
```

- `<<<<<<< HEAD`: Bắt đầu phần thay đổi ở commit hiện tại của nhánh đang đứng (`main`).
- `=======`: Đường ranh giới phân tách giữa nội dung của 2 nhánh đối đầu.
- `>>>>>>> feature-update`: Kết thúc phần thay đổi đến từ nhánh được yêu cầu gộp (`feature-update`).

### 3.2. Hiểu về Cơ chế Gộp 3 Vùng (3-Way Merge)
Khác với Fast-Forward (chỉ đơn thuần dịch chuyển con trỏ nhánh khi một nhánh là con cháu trực tiếp của nhánh kia), **3-Way Merge** xảy ra khi hai nhánh đã rẽ đôi (diverged). Git sử dụng 3 điểm dữ liệu (3 commits):
1. **Base Commit (Tổ tiên chung gần nhất):** Commit trước khi phân nhánh, nơi cả hai nhánh cùng có xuất phát điểm giống nhau.
2. **Ours (Commit tại `HEAD` của `main`):** Các thay đổi được thực hiện trên nhánh hiện tại.
3. **Theirs (Commit tại đỉnh của `feature-update`):** Các thay đổi được thực hiện trên nhánh phụ.

Nếu một dòng chỉ bị thay đổi ở nhánh `Ours` (hoặc chỉ ở `Theirs`), Git sẽ tự động giữ thay đổi đó. Nhưng khi cả `Ours` và `Theirs` đều thay đổi trên cùng một vùng so với `Base`, Git không thể tự đoán ý định của lập trình viên và bắt buộc phải tạm dừng tiến trình gộp để con người xử lý thủ công.

---

## 4. Các bước Xử lý Xung đột Thủ công

1. **Kiểm tra trạng thái:**
   ```bash
   git status
   ```
   Git thông báo tệp `README.md` đang ở trạng thái `both modified`.

2. **Chỉnh sửa thủ công nội dung:**
   Mở tệp `README.md`, phân tích ngữ nghĩa của cả 2 phía và tổng hợp thành bản thống nhất chất lượng nhất:
   - Xóa bỏ dòng `<<<<<<< HEAD`
   - Xóa bỏ dòng `=======`
   - Xóa bỏ dòng `>>>>>>> feature-update`
   - Giữ lại cả định danh phiên bản v1.0.0 Enterprise từ `main` và tính năng đặt mượn sách trực tuyến từ `feature-update`.

3. **Lưu file và đánh dấu đã xử lý xung đột:**
   ```bash
   git add README.md
   ```

4. **Tạo commit gộp (Merge Commit):**
   ```bash
   git commit -m "merge: resolve merge conflict between main and feature-update"
   ```

---

## 5. Nội dung Tệp README.md Hoàn chỉnh sau khi Giải quyết Xung đột

Dưới đây là nội dung chính thức của dự án sau khi đã gộp thành công cả hai nhánh:

```markdown
# Dự án Quản lý Thư viện số (Phiên bản v1.0.0 Enterprise)

## 1. Giới thiệu dự án
Hệ thống Quản lý Thư viện số là giải pháp toàn diện cho thư viện trường đại học, kết hợp giữa nền tảng quản trị bảo mật phân quyền đa cấp độ cho nhân viên và cổng dịch vụ tiện ích trực tuyến cho độc giả.

## 2. Các tính năng nổi bật
- **Phân quyền đa cấp độ (Enterprise Core):** Đảm bảo an toàn dữ liệu danh mục sách, hồ sơ thủ thư và độc giả.
- **Đặt mượn sách trực tuyến (Online Reservation):** Cho phép sinh viên và giảng viên đặt giữ sách từ xa.
- **Tra cứu nâng cao (Advanced Search):** Tìm kiếm thời gian thực theo ISBN, tác giả, chuyên ngành và tình trạng kệ sách.

## 3. Lịch sử cập nhật
- **v1.0.0:** Tích hợp thành công tính năng đặt mượn trực tuyến từ nhánh `feature-update` vào lõi phân quyền của nhánh `main`.
```

---

## 6. Kiểm tra Lịch sử Commit và Đồ thị Nhánh

### Lệnh thực thi:
```bash
git log --graph --oneline
```

### Kết quả hiển thị (Log Graph):
```text
*   e7b419c (HEAD -> main) merge: resolve merge conflict between main and feature-update
|\  
| * a1f8c32 (feature-update) feat: cap nhat mo ta tinh nang dat sach tren nhanh feature-update
* | 8d3e201 docs: cap nhat phien ban v1.0.0 va tinh nang bao mat tren main
|/  
* 4f90b1e docs: khoi tao du an va tep README.md
```

### Đánh giá kết quả:
- Đồ thị nhánh thể hiện rõ commit gốc `4f90b1e` rẽ thành 2 nhánh độc lập:
  - Nhánh `main` đi qua commit `8d3e201`.
  - Nhánh `feature-update` đi qua commit `a1f8c32`.
- Cả hai nhánh sau đó hội tụ về **Merge Commit `e7b419c`** với hai nhánh tổ tiên rõ ràng (`|\` và `|/`).
- Tình trạng xung đột đã được giải quyết triệt để, lịch sử Git nhất quán và trong sạch.
