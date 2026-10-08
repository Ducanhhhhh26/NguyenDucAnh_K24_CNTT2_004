# ĐỀ CƯƠNG ÔN TẬP TRỌNG TÂM THI DEVOPS
## (Quản trị Linux VPS - Làm việc với Git - Deploy HTML với Nginx)

---

## 📌 PHẦN 1: QUẢN TRỊ LINUX TRÊN VPS

### 1. Tạo và Quản lý Tài khoản User mới (20 điểm thi)
*Quy tắc: Tạo user ➔ Thêm quyền sudo ➔ Đặt password ➔ THOÁT ROOT & ĐĂNG NHẬP LẠI BY USER MỚI.*

```bash
# 1. Tạo user mới có thư mục home (-m) và bash shell (-s /bin/bash)
sudo useradd -m -s /bin/bash <username>

# 2. Đặt mật khẩu cho user mới
sudo passwd <username>

# 3. Thêm user vào group sudo (Secondary Group)
sudo usermod -aG sudo <username>

# 4. Kiểm tra thông tin user
id <username>
# Kết quả mong đợi: uid=1001(<username>) gid=1001(<username>) groups=1001(<username>),27(sudo)

# 5. BẮT BUỘC: THOÁT ROOT VÀ ĐĂNG NHẬP LẠI
exit
# Đăng nhập lại từ máy cá nhân: ssh <username>@IP_VPS
```


### 2. Thao tác File & Thư mục cơ bản
```bash
# Xem thư mục hiện tại & xem danh sách file (kể cả file ẩn -a, chi tiết -l)
pwd
ls -la

# Tạo thư mục (dùng -p để tạo thư mục lồng nhau nếu chưa có)
mkdir -p ~/devops-hackathon/submission

# Di chuyển thư mục
cd ~/devops-hackathon

# Copy file / thư mục
cp file.txt ~/submission/
cp -r /path/to/folder ~/submission/

# Tìm kiếm file trên hệ thống
find ~ -name "index.html"
find / -name "nginx.conf" 2>/dev/null

# Phân quyền file/thư mục (Đọc R=4, Ghi W=2, Thực thi X=1)
sudo chmod 755 /var/www/html
sudo chown -R www-data:www-data /var/www/my-app
```

---

### 3. Cài đặt Gói phần mềm & Cấu hình UFW Firewall
```bash
# 1. Cập nhật hệ thống
sudo apt update && sudo apt upgrade -y

# 2. Cài đặt các công cụ cần thiết (Git, Nginx, UFW, Node.js...)
sudo apt install -y git nginx ufw nodejs npm

# 3. Cấu hình UFW Firewall
sudo ufw default deny incoming   # Chặn toàn bộ kết nối vào mặc định
sudo ufw default allow outgoing  # Cho phép mọi kết nối ra ngoài
sudo ufw allow 22/tcp            # MỞ PORT SSH (RẤT QUAN TRỌNG, KHÔNG SẼ MẤT KẾT NỐI VPS)
sudo ufw allow 80/tcp            # Mở port HTTP Nginx
sudo ufw allow 8090/tcp          # Mở port Nginx tùy chỉnh (nếu đề yêu cầu 8090)

# 4. Kích hoạt & kiểm tra UFW
sudo ufw enable
sudo ufw status numbered
```

---

## 📌 PHẦN 2: LÀM VIỆC VỚI GIT

### 1. Clone Mã nguồn từ Repository
```bash
# Chuyển về thư mục cá nhân
cd ~

# Clone project từ Git về máy VPS
git clone <URL_REPOSITORY_CỦA_ĐỀ_THI>

# Ví dụ clone về với tên thư mục cụ thể:
git clone https://github.com/example/devops-hackathon.git ~/devops-hackathon

# Vào thư mục project vừa clone
cd ~/devops-hackathon
```

---

### 2. Các Lệnh Git Cơ bản Thường Dùng Khi Làm Bài
```bash
# Kiểm tra trạng thái các file đã thay đổi
git status

# Xem các nhánh (branch) hiện tại
git branch -a

# Chuyển sang nhánh theo yêu cầu đề thi (ví dụ: main, dev, exam)
git checkout main

# Cập nhật code mới nhất từ server
git pull origin main

# Tạo và chuyển sang nhánh mới (nếu đề yêu cầu)
git checkout -b bai-lam-cua-toi

# Thêm file thay đổi vào staging & commit
git add .
git commit -m "Hoàn thành cấu hình HTML và Nginx"

# Push code lên Git (nếu bài thi yêu cầu nộp qua Git)
git push origin bai-lam-cua-toi
```

---

## 📌 PHẦN 3: DEPLOY TRANG HTML VỚI NGINX

### 1. Tạo Web Root Directory & File HTML
```bash
# 1. Tạo thư mục chứa trang web (ví dụ: /var/www/task-app/ hoặc /var/www/html/)
sudo mkdir -p /var/www/task-app/

# 2. Tạo/Sửa file index.html
sudo nano /var/www/task-app/index.html
```

**Nội dung mẫu file `/var/www/task-app/index.html`:**
```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PTIT DevOps Task Management</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f4f4f9; padding: 40px; text-align: center; }
        .card { background: white; padding: 30px; border-radius: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.1); display: inline-block; }
        h1 { color: #2c3e50; }
        p { font-size: 18px; color: #555; margin: 10px 0; }
    </style>
</head>
<body>
    <div class="card">
        <h1>PTIT DevOps Task Management</h1>
        <p><strong>Họ và tên:</strong> Nguyen Van A</p>
        <p><strong>Mã sinh viên:</strong> B24DCCN000</p>
        <p><strong>Lớp:</strong> K24CNTT2</p>
    </div>
</body>
</html>
```

```bash
# 3. Phân quyền sở hữu thư mục web cho Nginx (www-data)
sudo chown -R www-data:www-data /var/www/task-app
sudo chmod -R 755 /var/www/task-app
```

---

### 2. Cấu hình Nginx Server Block

```bash
# 1. Tạo file cấu hình site mới trong /etc/nginx/sites-available/
sudo nano /etc/nginx/sites-available/<username>-task.conf
```

**Nội dung mẫu file cấu hình Nginx (cho Cổng 8090 hoặc Cổng 80 tùy đề bài):**

```nginx
server {
    listen 8090;             # Port Nginx nghe (ví dụ: 8090 hoặc 80)
    server_name _;           # Chấp nhận mọi IP/Domain

    root /var/www/task-app;  # Đường dẫn tới thư mục chứa index.html
    index index.html;        # File mặc định

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

### 3. Kích hoạt & Kiểm tra Nginx

```bash
# 1. Tạo symlink kích hoạt file cấu hình vào sites-enabled
sudo ln -s /etc/nginx/sites-available/<username>-task.conf /etc/nginx/sites-enabled/

# 2. (Tùy chọn) Xóa bớt file default nếu bị trùng cổng 80:
# sudo rm /etc/nginx/sites-enabled/default

# 3. Kiểm tra cú pháp file cấu hình Nginx (phải báo syntax is ok, test is successful)
sudo nginx -t

# 4. Reload lại dịch vụ Nginx để nhận cấu hình mới
sudo systemctl reload nginx
# Hoặc: sudo systemctl restart nginx

# 5. Cho phép cổng qua Firewall UFW
sudo ufw allow 8090/tcp
sudo ufw status numbered
```

---

### 4. Kiểm tra trang Web sau khi Deploy

Trực tiếp kiểm tra trên VPS bằng lệnh `curl`:
```bash
curl http://localhost:8090
```
Hoặc mở trình duyệt web trên máy cá nhân nhập: `http://<IP_VPS>:8090`

---

## 📌 PHẦN 4: ĐÓNG GÓI VÀ LƯU MINH CHỨNG NỘP BÀI

```bash
# 1. Tạo thư mục submission
mkdir -p ~/devops-hackathon/submission/

# 2. Xuất lịch sử lệnh terminal
history > ~/devops-hackathon/submission/history.log
# (Hoặc: cat ~/.bash_history > ~/devops-hackathon/submission/history.log)

# 3. Copy file cấu hình Nginx làm minh chứng
cp /etc/nginx/sites-available/<username>-task.conf ~/devops-hackathon/submission/

# 4. Kiểm tra lại thư mục nộp bài
ls -la ~/devops-hackathon/submission/
```
