# Plan bài làm DevOps - Đề 4

## 1. Thông tin cần chốt trước khi làm

- Tài khoản Linux: `nguyenducanhk24cntt2`
- Hệ điều hành: Ubuntu 22.04 hoặc 24.04
- Repository GitHub: public, nhánh `main`
- Cổng Nginx: dùng cổng riêng từ `8080` trở lên, không dùng cổng `80`
- Web root trên server: `/var/www/<ten-repository>/src`
- File cấu hình Nginx: `/etc/nginx/sites-available/nguyenducanhk24cntt2.conf`
- Địa chỉ truy cập: `http://<IP_may_chu>:<PORT_CA_NHAN>`

## 2. Cấu trúc repository cần nộp

```text
<ten-repository>/
├── src/
│   └── index.html
├── nginx/
│   └── nguyenducanhk24cntt2.conf
├── screenshots/
│   ├── 01-user.png
│   ├── 02-packages.png
│   ├── 03-git-repo.png
│   ├── 04-nginx-config.png
│   ├── 05-browser.png
│   └── 06-final-check.png
├── .gitignore
└── README.md
```

## 3. Giai đoạn 1 - Chuẩn bị môi trường VPS

1. Tạo user Linux mới, có home directory và shell `/bin/bash`.

```bash
sudo useradd -m -s /bin/bash nguyenducanhk24cntt2
sudo passwd nguyenducanhk24cntt2
sudo usermod -aG sudo nguyenducanhk24cntt2
```

2. Kiểm tra user và quyền sudo.

```bash
id nguyenducanhk24cntt2
```

Kết quả cần có:

- Primary group trùng username.
- Secondary group có `sudo`.

3. Thoát tài khoản hiện tại và đăng nhập lại bằng user mới.

```bash
whoami
```

Kết quả cần là `nguyenducanhk24cntt2`. Chụp minh chứng lưu thành `screenshots/01-user.png`.

4. Cài phần mềm cần thiết.

```bash
sudo apt update
sudo apt install -y nginx git ufw curl
sudo systemctl status nginx
sudo systemctl enable nginx
```

5. Cấu hình Git trên máy chủ.

```bash
git config --global user.name "Nguyen Duc Anh"
git config --global user.email "<email_github>"
git config --global --list
```

Chụp minh chứng cài đặt package, trạng thái Nginx và Git config thành `screenshots/02-packages.png`.

## 4. Giai đoạn 2 - Tạo website và quản lý mã nguồn

1. Tạo hoặc clone repository GitHub public theo đúng tên đề bài.

```bash
git clone <GITHUB_REPO_URL>
cd <ten-repository>
```

2. Tạo các thư mục chuẩn.

```bash
mkdir -p src nginx screenshots
```

3. Tạo file `src/index.html` theo đúng yêu cầu chấm điểm.

Yêu cầu bắt buộc:

- Dùng cấu trúc HTML5 chuẩn: `<!DOCTYPE html>`, `<html lang="vi">`, `<meta charset="UTF-8">`, `<meta name="viewport" ...>`.
- Thẻ `<title>` có dạng: `DevOps Hackathon - Đề 004 - Nguyễn Đức Anh`.
- Tiêu đề chính `<h1>` hiển thị chính xác mẫu: `Xin chào đến với Ptit Rikkei Devops Lab - Nguyễn Đức Anh - <Mã sinh viên> - HN-KS24-CNTT2`.
- Có khối thông tin sinh viên dạng bảng hoặc card.
- Có khối thông tin bài thi: đề số `004`, chủ đề `Quản lý kho hàng (Inventory)`, cổng Nginx, IP máy chủ, ngày thi và link repository.
- Có đoạn giới thiệu ngắn 2 đến 3 câu về hệ thống `Quản lý kho hàng (Inventory)`.

Thông tin sinh viên cần đưa vào trang:

- Họ tên: Nguyễn Đức Anh
- Lớp: HN-KS24-CNTT2
- Mã sinh viên: điền đúng mã sinh viên theo tài khoản thi
- Email: điền email đang dùng trên hệ thống hoặc GitHub
- Tài khoản Linux: `nguyenducanhk24cntt2`
- GitHub username: có link tới trang GitHub cá nhân

Nội dung gợi ý cho phần giới thiệu:

```text
Hệ thống Quản lý kho hàng (Inventory) hỗ trợ theo dõi danh sách sản phẩm, số lượng tồn kho và trạng thái nhập xuất. Bài triển khai này mô phỏng trang giới thiệu hệ thống, được quản lý bằng Git/GitHub và phục vụ qua Nginx trên máy chủ Linux.
```

4. Tạo `README.md` mô tả:

- Thông tin sinh viên.
- Tên user Linux.
- Cổng Nginx sử dụng.
- Web root.
- Link truy cập website.
- Các bước triển khai chính.

5. Commit và push lên GitHub.

```bash
git status
git add .
git commit -m "Initial DevOps deployment structure"
git push origin main
```

Chụp minh chứng repository, commit và push thành `screenshots/03-git-repo.png`.

## 5. Giai đoạn 3 - Deploy website bằng Nginx

1. Copy hoặc clone source về đúng thư mục `/var/www`.

```bash
cd /var/www
sudo git clone <GITHUB_REPO_URL> <ten-repository>
```

Nếu repository đã tồn tại:

```bash
cd /var/www/<ten-repository>
sudo git pull origin main
```

2. Phân quyền thư mục web.

```bash
sudo chown -R www-data:www-data /var/www/<ten-repository>
sudo chmod -R 755 /var/www/<ten-repository>
```

3. Tạo file Nginx server block.

```bash
sudo nano /etc/nginx/sites-available/nguyenducanhk24cntt2.conf
```

Nội dung mẫu:

```nginx
server {
    listen <PORT_CA_NHAN>;
    server_name <IP_may_chu>;

    root /var/www/<ten-repository>/src;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

4. Lưu một bản cấu hình vào repository.

```bash
sudo cp /etc/nginx/sites-available/nguyenducanhk24cntt2.conf /var/www/<ten-repository>/nginx/
```

5. Kích hoạt site và kiểm tra Nginx.

```bash
sudo ln -s /etc/nginx/sites-available/nguyenducanhk24cntt2.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Nếu dùng cổng `80` bị trùng site mặc định thì gỡ default:

```bash
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

Chụp minh chứng file cấu hình và kết quả `sudo nginx -t` thành `screenshots/04-nginx-config.png`.

## 6. Giai đoạn 4 - Mở firewall và kiểm thử

1. Mở SSH và cổng web cá nhân qua UFW.

```bash
sudo ufw allow 22/tcp
sudo ufw allow <PORT_CA_NHAN>/tcp
sudo ufw enable
sudo ufw status numbered
```

2. Kiểm tra trên server.

```bash
curl http://localhost:<PORT_CA_NHAN>
hostname -I
```

3. Kiểm tra trên trình duyệt máy cá nhân.

```text
http://<IP_may_chu>:<PORT_CA_NHAN>
```

Chụp màn hình trình duyệt hiển thị website thành `screenshots/05-browser.png`.

## 7. Giai đoạn 5 - Hoàn thiện minh chứng và nộp bài

1. Cập nhật README với thông tin thật:

- IP máy chủ.
- Cổng cá nhân.
- Đường dẫn web root.
- Tên file Nginx config.
- Link GitHub.
- Link website đã deploy.

2. Đưa file cấu hình và ảnh minh chứng vào repository.

```bash
cd /var/www/<ten-repository>
sudo git status
sudo git add README.md src/index.html nginx/nguyenducanhk24cntt2.conf screenshots/
sudo git commit -m "Complete Nginx deployment evidence"
sudo git push origin main
```

3. Kiểm tra cuối cùng.

```bash
ls -la
ls -la src nginx screenshots
git status
curl http://localhost:<PORT_CA_NHAN>
sudo systemctl status nginx
sudo ufw status numbered
```

Chụp minh chứng tổng hợp thành `screenshots/06-final-check.png`.

## 8. Checklist trước khi nộp

- [ ] Username Linux viết thường toàn bộ: `nguyenducanhk24cntt2`.
- [ ] User có home `/home/nguyenducanhk24cntt2`, shell `/bin/bash`, thuộc group `sudo`.
- [ ] Repository GitHub public và dùng nhánh `main`.
- [ ] Có `src/index.html` và Nginx chỉ phục vụ thư mục `src`.
- [ ] Có `nginx/nguyenducanhk24cntt2.conf` trong repository.
- [ ] Cổng Nginx là cổng riêng từ `8080` trở lên, không dùng `80`.
- [ ] UFW đã allow `22/tcp` và `<PORT_CA_NHAN>/tcp`.
- [ ] `sudo nginx -t` báo thành công.
- [ ] Truy cập được `http://<IP_may_chu>:<PORT_CA_NHAN>` từ trình duyệt.
- [ ] README đã ghi đầy đủ thông tin deploy.
- [ ] Ảnh minh chứng đặt trong `screenshots/` và đánh số đúng thứ tự.
