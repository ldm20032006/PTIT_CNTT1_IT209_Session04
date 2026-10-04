# Báo cáo Bài 2: Branch – Merge Conflict – 3-Way Merge

## Các lệnh đã thực hiện (tạo nhánh và di chuyển linh hoạt)
```bash
# đang ở main tại f9c8de8
git checkout -b feature-update
# sửa README.md dòng 1 -> nội dung feature-update
git add README.md
git commit -m "Update README on feature-update branch"   # -> 4fcfe35

git checkout main
# sửa README.md dòng 1 -> nội dung main
git add README.md
git commit -m "Update README on main branch"             # -> 862ed1c

git branch -v   # kiểm tra: main 862ed1c, feature-update 4fcfe35
git merge feature-update
```

Kết quả `git merge` báo:
```text
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

## Giải quyết xung đột thủ công 
1. Kiểm tra trạng thái:
   ```bash
   git status
   # both modified: README.md
   ```
2. Mở `README.md`, thấy Git chèn markers:
   ```text
   <<<<<<< HEAD
   # Bản cập nhật chính: README trên nhánh main
   =======
   # Tính năng mới: Cập nhật README trên nhánh feature-update
   >>>>>>> feature-update
   ```
   - `<<<<<<< HEAD`: bắt đầu vùng của nhánh hiện tại (`main`, commit `862ed1c`).
   - `=======`: phân cách 2 phía.
   - `>>>>>>> feature-update`: kết thúc vùng của nhánh được merge (`4fcfe35`).
3. Xử lý thủ công: xóa toàn bộ 3 dòng markers, quyết định nội dung giữ lại.
   Lựa chọn hợp nhất (không mất ý của 2 bên):
   ```text
   # README hợp nhất: Kết hợp nội dung từ main và feature-update
   ```
   Đảm bảo file **không còn** `<<<<<<<`, `=======`, `>>>>>>>`.
4. Đánh dấu đã giải quyết và hoàn thành merge commit:
   ```bash
   git add README.md
   git commit -m "Merge branch 'feature-update' into main - resolve conflict in README.md"
   # -> 58a80d8 (merge commit)
   ```
5. Xác minh không còn markers:
   ```bash
   Select-String -Pattern "<<<<<<<|=======|>>>>>>>" README.md
   # không trả về gì = đạt
   ```

## 5. Cơ chế 3-Way Merge
- `Base` = `f9c8de8` (`# 3. Trường hợp 1 — Reset`)
- `Ours` (HEAD, `main`) = `862ed1c` (`# Bản cập nhật chính...`)
- `Theirs` (`feature-update`) = `4fcfe35` (`# Tính năng mới...`)
- Git so sánh `Base→Ours` và `Base→Theirs`. Vì cả 2 cùng sửa **cùng dòng 1 theo 2 hướng khác nhau**, Git không thể tự quyết → báo conflict, buộc người dùng chọn tay.
- Kết quả: `58a80d8` có **2 parents**: `862ed1c` và `4fcfe35`:
  ```bash
  git rev-list --parents -n 1 58a80d8
  # 58a80d8 862ed1c 4fcfe35
  git cat-file -p 58a80d8  # hiện 2 dòng parent
  ```

## 6. Kiểm tra theo yêu cầu
Lệnh:
```bash
git log --graph --oneline --all
```
Kết quả hiện tại (đạt yêu cầu: `feature-update` tách ra từ `main`, rồi gộp lại bằng merge commit):
```text
* 1d0503a Add .gitignore to ignore sensitive files
*   58a80d8 Merge branch 'feature-update' into main - resolve conflict in README.md
|\
| * 4fcfe35 Update README on feature-update branch
* | 862ed1c Update README on main branch
|/
* f9c8de8 up
* 67cfe80 Add buggy code
* f396621 Add version 2
* 990a956 Initial commit
```
Giải thích đồ thị:
- `f9c8de8` là điểm rẽ nhánh.
- `862ed1c` đi theo `main`, `4fcfe35` đi theo `feature-update` (2 đường song song `|`, `*`).
- `58a80d8` là điểm hội tụ (merge commit, ký hiệu `|\` + `|/`).
- `1d0503a` là commit sau merge (bài 4, thêm `.gitignore`), không ảnh hưởng cấu trúc merge.

Các kiểm tra bổ sung:
```bash
git branch -v
# feature-update 4fcfe35 ...
# * main 1d0503a ...
# master f9c8de8 ...

git status
# On branch main, nothing to commit (trừ file untracked của bài khác: b4/README.md, b5/, homework/)
```
