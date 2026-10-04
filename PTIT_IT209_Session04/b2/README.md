# README hợp nhất: Kết hợp nội dung từ main và feature-update

## Mục tiêu

Di chuyển lịch sử local lùi lại 1 commit nhưng không làm mất mã nguồn hiện tại.

## Yêu cầu

- `HEAD` phải lùi lại 1 commit.
- Nội dung của commit lỗi vẫn phải được giữ trong Working Directory.
- File phải xuất hiện dưới dạng `Modified`.
- Không sử dụng `git reset --hard`.

---

## Bước 1: Kiểm tra lịch sử trước khi reset

```bash
git log --oneline -n 5
```

Kết quả:

```text
3f49681 (HEAD -> master) Add buggy code
f396621 Add version 2
990a956 Initial commit
```

## Bước 2: Reset lùi lại 1 commit

Sử dụng:

```bash
git reset --mixed HEAD~1
```

Có thể viết ngắn:

```bash
git reset HEAD~1
```

> `--mixed` là chế độ mặc định của `git reset`.

Sau khi chạy, Git thông báo:

```text
Unstaged changes after reset:
M       app.txt
```

## Bước 3: Kiểm tra lịch sử

```bash
git log --oneline -n 5
```

Kết quả:

```text
f396621 (HEAD -> master) Add version 2
990a956 Initial commit
```

Commit `3f49681 Add buggy code` không còn nằm trên lịch sử hiện tại của nhánh `master`.

---

# Thực hiện Revert

## Bước 1: Revert commit lỗi

Có thể sử dụng:

```bash
git revert HEAD
```

Hoặc chỉ định commit ID:

```bash
git revert abc1234
```

Git sẽ mở trình soạn thảo để xác nhận commit message.

Mặc định Git tạo message:

```text
Revert "Add buggy code"
```

## Bước 2: Xác nhận commit trong Vim

Nếu Git mở giao diện Vim như sau:

```text
Revert "Add buggy code"

This reverts commit abc1234.

# Please enter the commit message for your changes.
```

Không cần sửa nội dung.

Nhấn:

```text
Esc
```

Sau đó gõ:

```text
:wq
```

Nhấn:

```text
Enter
```

`wq` có nghĩa là:

- `w` = write, lưu file.
- `q` = quit, thoát Vim.
