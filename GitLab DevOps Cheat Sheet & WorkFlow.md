
# 🚀GITLAB & GIT CHEAT SHEET (Beginner)

## Mindset Notes - Các nguyên tắc cơ bản quan trọng !!!

1. **Private Key là sinh mạng:** Không bao giờ copy file `id_ed25519` (không đuôi) hoặc bất cứ file `PrivateKey` nào ra khỏi thư mục `~/.ssh/`. KHÔNG BAO GIỜ PUSH NÓ LÊN GIT.
2. **Máy tính không nói dối:** Nếu `git push` báo "Everything up-to-date" mà trên GitLab chưa có file -> Nghĩa là bạn chưa `commit`.
3. **Branching (Nhánh):** Sau này khi thành thạo, hạn chế code thẳng trên `main`. Hãy tập thói quen:
    - Tạo nhánh: `git checkout -b ten-tinh-nang-moi`
    - Làm việc trên nhánh đó.
    - Push nhánh đó lên và tạo Merge Request.
4. ...
## 1. Cấu hình & Định danh (Setup)
⚠️*Làm 1 lần duy nhất khi cài lại máy hoặc đổi máy mới.*

| Hành động        | Câu lệnh (Command)                                                                                    | Ghi chú quan trọng                                                                                                                                                                                         |
| ---------------- | ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Khai báo tên     | `git config --global user.name "Ten Cua Ban"`                                                         | Tên sẽ hiện trong lịch sử commit.                                                                                                                                                                          |
| Khai báo email   | `git config --global user.email "email@domain.com"`                                                   | Phải khớp email đăng ký GitLab.                                                                                                                                                                            |
| Tạo SSH Key      | `ssh-keygen -t ed25519 -C "email@domain.com"` hoặc <br>```ssh-keygen -t rsa -b 2048 -C "<comment>"``` | Chạy `ssh-keygen -t`với loại khóa và một comment tùy chọn để giúp xác định khóa sau này. Một tùy chọn phổ biến là sử dụng địa chỉ email của bạn làm comment. comment này sẽ được bao gồm trong tệp `.pub`. |
| Xem Public Key   | ```cat ~/.ssh/id_ed25519.pub```                                                                       | Copy chuỗi này ném lên GitLab Settings.                                                                                                                                                                    |
| Test kết nối     | ```ssh -T git@gitlab.com```                                                                           | Thấy "Welcome..." là thành công.                                                                                                                                                                           |
**Kiểm tra xem bạn có cặp khóa SSH hiện có hay không.**
1. Vào thư mục chính của bạn.
2. Hãy vào `.ssh/`thư mục con. Nếu `.ssh/`thư mục con không tồn tại, có thể bạn không ở trong thư mục chính hoặc bạn chưa từng sử dụng `ssh`trước đây. Trong trường hợp thứ hai, bạn cần [tạo một cặp khóa SSH](https://docs.gitlab.com/user/ssh/#generate-an-ssh-key-pair).
3. Kiểm tra xem có tệp nào có một trong các định dạng sau tồn tại hay không:

| Thuật toán                               | Khóa công khai      | Khóa riêng tư   |
| ---------------------------------------- | ------------------- | --------------- |
| ED25519 (ưu tiên)                        | `id_ed25519.pub`    | `id_ed25519`    |
| ED25519_SK                               | `id_ed25519_sk.pub` | `id_ed25519_sk` |
| ECDSA_SK                                 | `id_ecdsa_sk.pub`   | `id_ecdsa_sk`   |
| RSA (kích thước khóa tối thiểu 2048 bit) | `id_rsa.pub`        | `id_rsa`        |
| DSA (đã lỗi thời)                        | `id_dsa.pub`        | `id_dsa`        |
| ECDSA                                    | `id_ecdsa.pub`      | `id_ecdsa`      |
**Cấu hình SSH để trỏ đến một thư mục khác.**
Nếu bạn không lưu cặp khóa SSH của mình trong thư mục mặc định, hãy cấu hình ứng dụng SSH client để trỏ đến thư mục nơi lưu trữ khóa riêng tư.

1. Mở cửa sổ dòng lệnh và chạy lệnh này:	
```shell
	eval $(ssh-agent -s)
	ssh-add <directory to private SSH key>
```
2. Lưu các cài đặt này vào `~/.ssh/config`tệp. Ví dụ:
```conf
# GitLab.com
Host gitlab.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/gitlab_com_rsa

# Private GitLab instance
Host gitlab.company.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/example_com_rsa
```
Để biết thêm thông tin về các thiết lập này, hãy xem [`man ssh_config`](https://man.openbsd.org/ssh_config)trang trong hướng dẫn cấu hình SSH.

Khóa SSH công khai phải là duy nhất đối với GitLab vì chúng được liên kết với tài khoản của bạn. Khóa SSH của bạn là mã định danh duy nhất bạn có khi đẩy mã bằng SSH. Nó phải được ánh xạ duy nhất đến một người dùng duy nhất.
## 2. Quy trình làm việc hàng ngày (Daily Workflow)
*Đây là vòng lặp bạn sẽ làm hàng nghìn lần. Hãy thuộc nằm lòng.*

⚠️**Quy tắc vàng:** Luôn chạy `git status` trước khi làm bất cứ điều gì để biết mình đang đứng ở đâu.

### Bước 1: Kiểm tra trạng thái

```bash
git status
```

* _Màu đỏ:_ File mới hoặc file sửa chưa được theo dõi (Untracked).
- _Màu xanh:_ File đã sẵn sàng để đóng gói (Staged).
- _Clean:_ Không có gì mới.

### Bước 2: Gom file (Add)

```bash 
git add .
```
* Dấu `.` nghĩa là lấy TẤT CẢ thay đổi hiện tại.

### Bước 3: Đóng gói (Commit)

```bash
git commit -m "Mô tả/update việc vừa làm"
```
- Ví dụ: `"Update backup script"`, `"Add new PDF documents"`.
- **Không bao giờ** commit mà không có message (`-m`).

### Bước 4: Đẩy lên Server (Push)

```bash
git push origin main
```
- Đưa các gói hàng (commits) từ máy lên kho chứa online (GitLab).


## 3. Quản lý Remote (Chuyển nhà, Clone code)
_Dùng khi bạn clone từ GitHub về nhưng muốn đẩy lên GitLab, hoặc đổi tên dự án._

| **Tình huống**           | **Câu lệnh**                           | **Giải thích**                                    |
| ------------------------ | -------------------------------------- | ------------------------------------------------- |
| **Xem địa chỉ hiện tại** | `git remote -v`                        | Để biết code đang trỏ về đâu (GitHub hay GitLab). |
| **Xóa địa chỉ cũ**       | `git remote remove origin`             | Cắt đứt liên hệ với kho cũ.                       |
| **Thêm địa chỉ mới**     | `git remote add origin [SSH-LINK]`     | Kết nối với kho mới (GitLab của bạn).             |
| **Sửa địa chỉ (nhanh)**  | `git remote set-url origin [SSH-LINK]` | Thay thế trực tiếp URL cũ bằng cái mới.           |

## 4. Các tình huống Nâng cao (Troubleshooting) <update liên tục>
_Những lệnh dùng khi gặp lỗi hoặc cần xử lý mạnh tay._

### Troubleshoot: "Fetch first" / "Refusing to merge unrelated histories"

- **Nguyên nhân:** Lịch sử trên GitLab và máy tính không khớp nhau (thường do tạo Project có sẵn file README).
- **Giải pháp (Chỉ dùng khi bạn muốn Code ở máy tính đè bẹp Code trên Server):**

```bash
git push -u origin main -f
```

- ⚠️ **Cảnh báo:** `-f` (Force) sẽ xóa sạch lịch sử trên nhánh `main` của GitLab. Chỉ dùng cho project cá nhân hoặc khi mới khởi tạo.

### Troubleshoot: Push xong không thấy file mới đâu?

- **Nguyên nhân:** Quên `add` và `commit` những file mới copy vào folder.
- **Giải pháp:**
    1. Copy file vào đúng thư mục dự án.
    2. `git status` (Thấy màu đỏ).
    3. `git add .` -> `git commit` -> `git push`.
### Troubleshoot: Permission denied (publickey)

- **Nguyên nhân:** Máy tính chưa nhận SSH Key hoặc sai Key.
- **Giải pháp:**

```bash
eval "$(ssh-agent -s)"    # Bật trình quản lý key
ssh-add ~/.ssh/id_ed25519 # Nạp key vào
ssh -T git@gitlab.com     # Test lại
```

