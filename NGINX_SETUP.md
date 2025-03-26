# Hướng dẫn cài đặt Nginx cho Marianbay.click

File này cung cấp hướng dẫn chi tiết về cách cài đặt và cấu hình Nginx để phục vụ ứng dụng Marianbay.click.

## 1. Cài đặt Nginx

### Trên Ubuntu/Debian:
```bash
sudo apt update
sudo apt install nginx
```

### Trên CentOS/RHEL:
```bash
sudo yum install epel-release
sudo yum install nginx
```

### Trên macOS:
```bash
brew install nginx
```

## 2. Cấu hình Nginx

File cấu hình Nginx đã được tạo tại `/Users/dovedev/Desktop/Marianbay/nginx.conf`. Bạn cần sao chép file này vào thư mục cấu hình của Nginx.

### Trên Linux:
```bash
sudo cp /Users/dovedev/Desktop/Marianbay/nginx.conf /etc/nginx/nginx.conf
```

### Trên macOS (với Homebrew):
```bash
cp /Users/dovedev/Desktop/Marianbay/nginx.conf /usr/local/etc/nginx/nginx.conf
```

## 3. Cấu hình SSL

Bạn cần tạo hoặc mua chứng chỉ SSL cho tên miền marianbay.click. Sau đó, đặt các file chứng chỉ vào thư mục `/etc/nginx/ssl/` (hoặc thư mục tương ứng trên hệ thống của bạn).

### Tạo thư mục SSL:
```bash
sudo mkdir -p /etc/nginx/ssl
```

### Sử dụng Let's Encrypt (miễn phí):
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d marianbay.click -d www.marianbay.click
```

## 4. Cấu hình DNS

Bạn cần trỏ tên miền marianbay.click đến địa chỉ IP của máy chủ của bạn. Đăng nhập vào trang quản lý DNS của nhà cung cấp tên miền và tạo các bản ghi sau:

- Bản ghi A: marianbay.click -> [Địa chỉ IP của máy chủ]
- Bản ghi A: www.marianbay.click -> [Địa chỉ IP của máy chủ]

## 5. Kiểm tra và khởi động Nginx

### Kiểm tra cấu hình:
```bash
sudo nginx -t
```

### Khởi động Nginx:
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Trên macOS:
```bash
brew services start nginx
```

## 6. Cấu hình tường lửa (nếu cần)

### Trên Ubuntu/Debian:
```bash
sudo ufw allow 'Nginx Full'
```

### Trên CentOS/RHEL:
```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

## Lưu ý quan trọng

1. Đảm bảo rằng các đường dẫn trong file nginx.conf phù hợp với cấu trúc thư mục thực tế của bạn.
2. Thay thế các đường dẫn chứng chỉ SSL bằng đường dẫn thực tế đến chứng chỉ của bạn.
3. Đảm bảo rằng cả front-end và back-end đều đang chạy trên các cổng đã cấu hình (3002 cho front-end và 8082 cho back-end).
4. Nếu bạn thay đổi cổng mặc định, hãy cập nhật lại file cấu hình nginx.conf.

## Kiểm tra

Sau khi hoàn tất cấu hình, bạn có thể truy cập trang web của mình qua địa chỉ https://marianbay.click để kiểm tra xem mọi thứ đã hoạt động chưa.