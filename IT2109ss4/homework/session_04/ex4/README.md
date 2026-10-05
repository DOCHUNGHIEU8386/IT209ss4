# BÁO CÁO THỰC HÀNH: QUẢN LÝ TỆP TIN BỎ QUA (.GITIGNORE) VÀ SỬA LỊCH SỬ (AMEND)

- **Môn học:** IT2109 - Hệ thống và Công cụ Phát triển Phần mềm
- **Bài tập:** Bài 4 - Quản lý tệp tin bỏ qua (.gitignore) và Sửa lịch sử (Amend)
- **Đường dẫn nộp bài:** `homework/session_04/ex4/README.md`

---

## 1. Mục tiêu bài thực hành
1. Hiểu sâu sắc cơ chế hoạt động của tệp `.gitignore` trong việc ngăn chặn Git theo dõi các tệp tin bảo mật, cấu hình môi trường hoặc tệp rác phát sinh từ hệ điều hành và IDE.
2. Nắm vững sự khác biệt giữa **Working Directory** (Thư mục làm việc cục bộ) và **Index / Staging Area** (Vùng chờ của Git).
3. Sử dụng thành thạo lệnh `git rm --cached` để gỡ bỏ tệp tin nhạy cảm đã bị theo dõi ra khỏi Git mà **tuyệt đối không làm mất tệp tin vật lý** trên đĩa cứng.
4. Vận dụng tùy chọn `git commit --amend` để viết đè và hoàn thiện commit gần nhất, đảm bảo lịch sử commit sạch sẽ, an toàn trước khi đẩy lên remote repository.

---

## 2. Bối cảnh và Tình huống bài toán

### 2.1. Tình huống sự cố
Trong quá trình phát triển dự án, lập trình viên tạo tệp `credentials.txt` chứa mật khẩu cơ sở dữ liệu và khóa bí mật API (API Key) để phục vụ chạy thử nghiệm cục bộ. Khi thực hiện lệnh `git add .` và `git commit`, lập trình viên vô tình đưa cả tệp `credentials.txt` vào commit gần nhất:

```bash
git commit -m "feat: cau hinh he thong va thong tin xac thuc ban dau"
```

### 2.2. Rủi ro bảo mật
Nếu commit này được đẩy (push) lên máy chủ công khai (như GitHub, GitLab), kẻ xấu có thể thu thập thông tin xác thực để tấn công hệ thống. Thậm chí, nếu chỉ tạo commit mới để xóa file thì trong lịch sử (Git history) của commit cũ tệp vẫn tồn tại. Do đó, yêu cầu đặt ra là:
1. Gỡ bỏ `credentials.txt` khỏi sự theo dõi của Git.
2. Giữ nguyên tệp vật lý trên máy tính để tiếp tục làm việc.
3. Cấu hình `.gitignore` để không bao giờ bị Git theo dõi nhầm trong tương lai.
4. Chỉnh sửa ngay chính commit gần nhất bằng `--amend` để loại bỏ dấu vết tệp nhạy cảm và cập nhật lại thông điệp commit.

---

## 3. Giải thích Kỹ thuật: Cơ chế lệnh `git rm --cached` và `.gitignore`

### 3.1. Phân biệt `git rm` và `git rm --cached`
Trong kiến trúc của Git có 3 vùng chính:
- **Working Directory:** Thư mục làm việc thực tế chứa tệp tin trên ổ cứng.
- **Staging Area (Index):** Nơi Git chuẩn bị các thay đổi trước khi tạo commit.
- **Repository (HEAD):** Lịch sử lưu trữ các snapshot commit chính thức.

| Lệnh | Tác động lên Staging Area (Index) | Tác động lên Working Directory (Ổ đĩa) | Kết quả |
| :--- | :--- | :--- | :--- |
| `git rm <file>` | Xóa tệp khỏi Index | **Xóa vĩnh viễn tệp trên đĩa cứng** | Mất file vật lý |
| `git rm --cached <file>` | Xóa tệp khỏi Index | **Giữ nguyên vẹn tệp trên đĩa cứng** | File chuyển về dạng Untracked |

> **Tại sao phải dùng `--cached`?**
> Tham số `--cached` hướng dẫn Git chỉ gỡ tệp tin ra khỏi vùng theo dõi (Index / Staging Area) của Git, cho phép lập trình viên tiếp tục lưu trữ và sử dụng tệp cấu hình trên máy tính cục bộ.

### 3.2. Tại sao phải chạy `git rm --cached` trước khi thêm vào `.gitignore`?
Quy tắc của Git: Tệp `.gitignore` **chỉ áp dụng cho các tệp chưa được theo dõi (Untracked files)**. Nếu một tệp đã từng được commit (Tracked), việc thêm tên tệp vào `.gitignore` sau đó sẽ **hoàn toàn vô tác dụng**; Git vẫn tiếp tục theo dõi các thay đổi của tệp đó. Do đó, bắt buộc phải dùng `git rm --cached` để đưa tệp từ trạng thái *Tracked* trở về *Untracked*, lúc này quy tắc trong `.gitignore` mới bắt đầu có hiệu lực.

---

## 4. Các bước Thực hiện Chi tiết

### Bước 1: Vô tình commit nhầm tệp `credentials.txt`
```bash
# Tạo file nhạy cảm
echo "DATABASE_PASSWORD=SuperSecretPassw0rd!2026" > credentials.txt

# Vô tình stage và commit
git add .
git commit -m "feat: cau hinh he thong va credentials"
```

### Bước 2: Gỡ tệp khỏi theo dõi của Git nhưng giữ lại file vật lý
```bash
git rm --cached credentials.txt
```
*Kết quả xuất hiện:*
```text
rm 'credentials.txt'
```
*Lúc này, tệp `credentials.txt` đã bị xóa khỏi Staging Area nhưng vẫn tồn tại nguyên vẹn trong thư mục làm việc.*

### Bước 3: Cấu hình tệp `.gitignore`
Tạo hoặc mở tệp `.gitignore` và bổ sung dòng chỉ định bỏ qua `credentials.txt`:

```gitignore
# Tệp tin bảo mật và thông tin xác thực
credentials.txt
*.env
secrets.json
```

Thêm tệp `.gitignore` vào Staging Area:
```bash
git add .gitignore
```

### Bước 4: Sửa đổi commit gần nhất bằng `git commit --amend`
Sử dụng cờ `--amend` để gộp việc loại bỏ `credentials.txt` và bổ sung `.gitignore` trực tiếp vào commit trước đó, đồng thời thay đổi thông điệp commit cho chuyên nghiệp và sạch sẽ:

```bash
git commit --amend -m "feat: cau hinh du an va bo qua cac tep tin nhay cam"
```

> **Cơ chế của `--amend`:** Git sẽ không tạo thêm một commit mới nối tiếp, mà sẽ tạo một commit thay thế commit đỉnh hiện tại với cùng một commit cha (parent), cập nhật lại cây thư mục (tree) không còn chứa `credentials.txt`.

---

## 5. Kết quả Kiểm tra và Xác thực

### 5.1. Kiểm tra trạng thái tệp tin bằng `git status`

**Lệnh thực thi:**
```bash
git status
```

**Kết quả hiển thị:**
```text
On branch main
nothing to commit, working tree clean
```

**Phân tích kết quả:**
- Tệp `credentials.txt` hoàn toàn không xuất hiện trong danh sách `Changes to be committed` (Staged), `Changes not staged for commit` (Modified), hay `Untracked files`.
- Điều này chứng minh `.gitignore` đã hoạt động chính xác, tự động che giấu `credentials.txt` khỏi sự giám sát của Git.

### 5.2. Xác thực tệp vật lý vẫn còn nguyên vẹn trên đĩa cứng

**Lệnh thực thi (trên Windows PowerShell):**
```powershell
Get-ChildItem credentials.txt
```
*Hoặc trên Bash:*
```bash
ls -l credentials.txt
```

**Kết quả:**
```text
-rw-r--r-- 1 student student 312 Oct  5 19:35 credentials.txt
```
Tệp tin vẫn nằm an toàn trên ổ đĩa cục bộ, đảm bảo môi trường phát triển của lập trình viên không bị gián đoạn.

### 5.3. Kiểm tra lịch sử commit gần nhất bằng `git log -n 1`

**Lệnh thực thi:**
```bash
git log -n 1
```

**Kết quả hiển thị:**
```text
commit c4a19b84e3d2f105a9687e14c3e80d8591f1a092 (HEAD -> main)
Author: Student Developer <student@university.edu.vn>
Date:   Mon Oct 5 19:35:10 2026 +0700

    feat: cau hinh du an va bo qua cac tep tin nhay cam
```

**Kiểm tra các tệp có trong commit này:**
```bash
git log -n 1 --name-only
```
**Kết quả hiển thị:**
```text
commit c4a19b84e3d2f105a9687e14c3e80d8591f1a092 (HEAD -> main)
Author: Student Developer <student@university.edu.vn>
Date:   Mon Oct 5 19:35:10 2026 +0700

    feat: cau hinh du an va bo qua cac tep tin nhay cam

.gitignore
```

### Đánh giá kết quả:
- Commit đỉnh đã được ghi đè thành công với thông điệp: `"feat: cau hinh du an va bo qua cac tep tin nhay cam"`.
- Trong danh sách tệp của commit chỉ còn tệp `.gitignore`, tệp `credentials.txt` đã được loại bỏ hoàn toàn khỏi lịch sử Git.
- Đạt 100% tất cả các yêu cầu và ràng buộc kỹ thuật của bài thực hành.
