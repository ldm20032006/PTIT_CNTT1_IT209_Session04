# Bai 4: Quan ly file bo qua (.gitignore) va Sua lich su (Amend)

## 1. Muc tieu
- Cau hinh file an `.gitignore` de tu dong bo qua file nhay cam hoac file rac he thong.
- Go bo file da commit ra khoi cache theo doi cua Git mot cach an toan (khong mat file vat ly).
- Chinh sua thong diep commit gan nhat thong qua tuy chon `--amend`.

## 2. Boi canh
Hoc vien vo tinh commit nham file chua thong tin bao mat `credentials.txt` len Git.
Can:
1. Go bo file nay khoi su theo doi ma khong lam mat file vat ly tren dia cung.
2. Cau hinh de Git bo qua file nay trong tuong lai.
3. Sua lai tin nhan commit gan nhat cho sach se.

Rang buoc: Khong su dung lenh xoa vat ly file tren he dieu hanh, phai giu lai tep tin o thu muc lam viec cuc bo.

Thu muc thuc hien: `b4/` (tuong duong `homework/session_04/ex4/` tren GitHub).

---

## 3. Cac buoc thuc hien

### Buoc 1: Tai hien commit nham
Tao file nhay cam `b4/credentials.txt`:

```text
username=admin
password=SuperSecret123
api_key=AKIA-EXAMPLE-123456
```

Commit nham voi message xau (sai chinh ta, can sua):

```bash
git add b4/credentials.txt
git commit -m "add credentails"
```

Kiem tra:

```bash
git log --oneline -3
# cb9bcf0 add credentails   <- commit loi can sua
# 58a80d8 Merge branch 'feature-update' into main - resolve conflict in README.md
# ...

git ls-files | findstr credentials
# b4/credentials.txt  <- dang bi theo doi (khong mong muon)
```

### Buoc 2: Go file khoi cache bang `git rm --cached`
Lenh quan trong nhat cua bai:

```bash
git rm --cached b4/credentials.txt
```

Ket qua:

```text
rm 'b4/credentials.txt'
```

Giai thich:
- `git rm --cached <file>` chi xoa file khoi **index / staging area** (bo theo doi),
  KHONG xoa file vat ly trong Working Directory.
- Do do file `b4/credentials.txt` van ton tai tren dia cung (kiem tra bang `Test-Path` van `True`).
- Phan biet:
  - `del credentials.txt` / `rm credentials.txt` (OS): xoa vat ly -> VI PHAM rang buoc, mat du lieu.
  - `git rm credentials.txt`: xoa ca khoi index VA khoi dia cung -> khong an toan cho bai nay.
  - `git rm --cached credentials.txt`: chi xoa khoi index, giu file dia cung -> DUNG cho bai nay.

Kiem tra file vat ly van con:

```bash
Test-Path b4/credentials.txt
# True
```

### Buoc 3: Tao `.gitignore` de Git bo qua trong tuong lai
Tao file `b4/.gitignore` voi noi dung:

```gitignore
# Bo qua file nhay cam (Bai 4)
credentials.txt

# File moi truong / bao mat
.env
*.pem
*.key

# File rac he thong
*.log
*.tmp
.DS_Store
Thumbs.db
```

Dong quan trong nhat la `credentials.txt`. Vi `.gitignore` nam trong `b4/`,
pattern nay se bo qua `b4/credentials.txt`.

Kiem tra ignore co tac dung:

```bash
git check-ignore -v b4/credentials.txt
# b4/.gitignore:2:credentials.txt  b4/credentials.txt
```

### Buoc 4: Sua commit gan nhat bang `--amend`
Dua `.gitignore` vao staging, dong thoi giu thay doi `deleted: b4/credentials.txt`
(dang staged tu buoc 2), roi ghi de len commit loi:

```bash
git add b4/.gitignore
git commit --amend -m "Add .gitignore to ignore sensitive files"
```

Ket qua (minh hoa — hash se thay doi sau moi lan `--amend`, quan trong la message):
- Commit cu `cb9bcf0 add credentails` bi thay the bang commit moi
  message `Add .gitignore to ignore sensitive files`.
  Vi du trung gian: `2077bd2 Add .gitignore to ignore sensitive files`
  (chi chua `b4/.gitignore`, KHONG con `b4/credentials.txt`).
- Sau khi them bao cao, commit cuoi cung van giu NGUYEN message tren,
  bao gom 4 file: `b4/.gitignore`, `b4/README.md`,
  `homework/session_04/ex4/.gitignore`, `homework/session_04/ex4/README.md`.
- Message da duoc sua tu `add credentails` (sai chinh ta) thanh
  `Add .gitignore to ignore sensitive files` (sach se).

> Luu y: `--amend` viet lai lich su (doi hash commit). Chi dung cho commit chua push
> hoac lam viec local. Neu da push len remote, can `git push --force` co kiem soat.

---

## 4. Kiem tra theo yeu cau

### 4.1. `git status` — file khong con bi theo doi

```bash
git status
```

Ket qua thuc te (sau khi hoan tat, working tree sach ngoai tru file cua bai khac):

```text
On branch main
Untracked files:
        b5/

nothing added to commit but untracked files present (use "git add" to track)
```

Nhan xet:
- `b4/credentials.txt` KHONG xuat hien o dang Staged hay Modified hay Untracked.
  Vi da `rm --cached` + da co `.gitignore`, Git hoan toan bo qua file nay.
  (Luc trung gian co the thay `deleted: README.md...` do bai tap khac trong cung repo,
  nhung sau khi on dinh, `git status` sach va tuyet doi khong thay `credentials.txt`.)
- Kiem tra bo sung:

```bash
git ls-files
# README.md
# b4/.gitignore
# snapshot/image.png
# ...

git check-ignore -v b4/credentials.txt
# b4/.gitignore:2:credentials.txt  b4/credentials.txt -> da bi ignore
```

=> Dat yeu cau: File khong con xuat hien trong danh sach theo doi.

### 4.2. `git log -n 1` — message da sua thanh cong

```bash
git log -n 1
```

Ket qua thuc te (hash minh hoa, hash thuc te co the khac sau moi lan amend
nhung message khong doi):

```text
commit 2efbd1c3790b7ee97d5b9dae0985b33e8e08b4ec
Author: test <test@test.com>
Date:   Fri Oct 2 12:12:04 2026 +0700

    Add .gitignore to ignore sensitive files
```

Chung minh `--amend` thanh cong:
- Truoc amend: `cb9bcf0 add credentails`
- Sau amend: `... Add .gitignore to ignore sensitive files`
  (vi du `2077bd2`, sau khi them bao cao la `2efbd1c` — cung message).
- Hash doi, message sach, commit KHONG con chua `credentials.txt`.

Kiem tra chi tiet commit:

```bash
git show HEAD --stat --oneline
# 2efbd1c Add .gitignore to ignore sensitive files
#  b4/.gitignore                      |  13 ++
#  b4/README.md                       | ...
#  homework/session_04/ex4/.gitignore |  13 ++
#  homework/session_04/ex4/README.md  | ...
#  4 files changed, ... insertions(+)
```

=> Dat yeu cau: Lich su commit gan nhat hien thi thong diep da sua doi.

---

## 5. File nop

Duong dan tren GitHub: `homework/session_04/ex4/`
(Thuc hien local trong `b4/`, noi dung tuong duong va da commit ca 2 noi)

- `.gitignore` — cau hinh bo qua `credentials.txt` (+ `.env`, `*.log`, rac he thong).
  Co 2 ban giong nhau: `b4/.gitignore` va `homework/session_04/ex4/.gitignore`.
- `README.md` (file nay) — giai thich `git rm --cached` + ket qua `git log -n 1`.
  Co 2 ban giong nhau: `b4/README.md` va `homework/session_04/ex4/README.md`.

Tuyet doi khong nop `credentials.txt` vi la file nhay cam va da bi ignore.

## 6. Tong ket lenh

```bash
# 1. Commit nham (tai hien)
git add b4/credentials.txt
git commit -m "add credentails"

# 2. Go khoi cache, giu file vat ly
git rm --cached b4/credentials.txt

# 3. Tao b4/.gitignore co dong credentials.txt
# 4. Amend de xoa file khoi lich su commit gan nhat + sua message
git add b4/.gitignore
git commit --amend -m "Add .gitignore to ignore sensitive files"

# 5. Them bao cao + ban nop homework, giu nguyen message (amend lan 2)
git add b4/README.md homework/session_04/ex4/.gitignore homework/session_04/ex4/README.md
git commit --amend -m "Add .gitignore to ignore sensitive files"

# 6. Kiem tra
git status
git log -n 1
Test-Path b4/credentials.txt
git check-ignore -v b4/credentials.txt
```
