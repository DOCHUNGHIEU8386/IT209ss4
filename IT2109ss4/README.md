# BÀI TẬP VỀ NHÀ: IT2109 - BUỔI 04 (GIT ADVANCED)

Họ và tên học viên / Mã sinh viên: *Học viên IT2109*  
Học phần: **IT2109 - Hệ thống và Công cụ Phát triển Phần mềm**  
Hạn mục: **Bài tập Session 04**

---

## Danh mục bài làm

| Bài tập | Nội dung chính | Đường dẫn thư mục | Tệp tin nộp |
| :--- | :--- | :--- | :--- |
| **Bài 2** | Quản lý nhánh & Giải quyết xung đột (Merge Conflict), Cơ chế 3-Way Merge | [`homework/session_04/ex2/`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex2/README.md) | [`README.md`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex2/README.md) |
| **Bài 4** | Quản lý tệp tin bỏ qua (`.gitignore`) & Sửa lịch sử (`--amend`), `git rm --cached` | [`homework/session_04/ex4/`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex4/README.md) | [`.gitignore`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex4/.gitignore), [`credentials.txt`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex4/credentials.txt), [`README.md`](file:///c:/Users/DELL/OneDrive/Desktop/IT2109ss4/homework/session_04/ex4/README.md) |

---

## Tóm tắt nhanh kết quả thực hiện

### 1. Bài 2: Merge Conflict & 3-Way Merge
- **Kịch bản:** Xung đột trên cùng dòng mô tả trong `README.md` giữa nhánh `main` và nhánh `feature-update`.
- **Xử lý:** Nhận diện và gỡ bỏ thủ công các ký hiệu `<<<<<<< HEAD`, `=======`, `>>>>>>> feature-update`, tích hợp đồng thời tính năng của cả 2 nhánh.
- **Kiểm tra đồ thị nhánh:** Lệnh `git log --graph --oneline` hiển thị cấu trúc 2 nhánh phân kỳ từ commit gốc và hội tụ tại merge commit 2 tổ tiên.

### 2. Bài 4: .gitignore, git rm --cached & git commit --amend
- **Kịch bản:** Vô tình commit nhầm tệp bảo mật `credentials.txt`.
- **Xử lý:** Dùng lệnh `git rm --cached credentials.txt` để bỏ theo dõi trên Git nhưng giữ lại file vật lý trên đĩa cứng; cấu hình `.gitignore` chặn theo dõi tương lai; dùng `git commit --amend` sửa commit đỉnh.
- **Kiểm tra:** `git status` (working tree clean, không theo dõi credentials.txt), `git log -n 1` (commit đỉnh sạch sẽ).
