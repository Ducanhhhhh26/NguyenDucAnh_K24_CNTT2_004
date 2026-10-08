# DevOps Hackathon - Đề 004

## Thông tin sinh viên

- Họ và tên: Nguyễn Đức Anh
- Mã sinh viên: PTIT-HN-105
- Lớp: HN-KS24-CNTT2
- Email: ducanhcualo2403@gmail.com
- Tài khoản Linux: `nguyenducanhk24cntt2`
- GitHub: [Ducanhhhhh26](https://github.com/Ducanhhhhh26)

## Thông tin triển khai

- Chủ đề: Quản lý kho hàng (Inventory)
- Repository: <https://github.com/Ducanhhhhh26/NguyenDucAnh_K24_CNTT2_004>
- Nhánh chính: `main`
- Web root: `/var/www/NguyenDucAnh_K24_CNTT2_004/src`
- File cấu hình Nginx: `/etc/nginx/sites-available/nguyenducanhk24cntt2.conf`
- Cổng Nginx: `8084`
- IP máy chủ: `14.224.184.165`
- URL truy cập: `http://14.224.184.165:8084`

## Cấu trúc thư mục

```text
NguyenDucAnh_K24_CNTT2_004/
├── src/
│   └── index.html
├── nginx/
│   └── nguyenducanhk24cntt2.conf
├── screenshots/
├── README.md
└── .gitignore
```

## Các bước triển khai trên VPS

### 1. Tạo tài khoản Linux

```bash
sudo useradd -m -s /bin/bash nguyenducanhk24cntt2
sudo passwd nguyenducanhk24cntt2
sudo usermod -aG sudo nguyenducanhk24cntt2
id nguyenducanhk24cntt2
```

Đăng nhập lại bằng user mới và kiểm tra:

```bash
whoami
```

### 2. Cài đặt phần mềm

```bash
sudo apt update
sudo apt install -y nginx git ufw curl
sudo systemctl enable nginx
sudo systemctl status nginx
```

### 3. Cấu hình Git

```bash
git config --global user.name "Ducanhhhhh26"
git config --global user.email "ducanhcualo2403@gmail.com"
git config --global --list
```

### 4. Clone mã nguồn vào `/var/www`

```bash
cd /var/www
sudo git clone https://github.com/Ducanhhhhh26/NguyenDucAnh_K24_CNTT2_004.git
sudo chown -R www-data:www-data /var/www/NguyenDucAnh_K24_CNTT2_004
sudo chmod -R 755 /var/www/NguyenDucAnh_K24_CNTT2_004
```

### 5. Cấu hình Nginx

```bash
sudo cp /var/www/NguyenDucAnh_K24_CNTT2_004/nginx/nguyenducanhk24cntt2.conf /etc/nginx/sites-available/
sudo ln -s /etc/nginx/sites-available/nguyenducanhk24cntt2.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

Nếu file cấu hình mặc định gây trùng cổng, có thể gỡ site default:

```bash
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl reload nginx
```

### 6. Mở firewall

```bash
sudo ufw allow 22/tcp
sudo ufw allow 8084/tcp
sudo ufw enable
sudo ufw status numbered
```

### 7. Kiểm tra website

```bash
curl http://localhost:8084
hostname -I
```

Truy cập từ trình duyệt:

```text
http://14.224.184.165:8084
```

## Minh chứng cần nộp

- `screenshots/01-user.png`: kết quả `id` và `whoami`.
- `screenshots/02-packages.png`: trạng thái Nginx, Git config hoặc package đã cài.
- `screenshots/03-git-repo.png`: repository GitHub, commit hoặc push.
- `screenshots/04-nginx-config.png`: file cấu hình Nginx và kết quả `nginx -t`.
- `screenshots/05-browser.png`: website truy cập thành công trên trình duyệt.
- `screenshots/06-final-check.png`: kiểm tra cuối cùng gồm `git status`, `curl`, `ufw status`.
