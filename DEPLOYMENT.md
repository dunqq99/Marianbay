# Hướng dẫn triển khai Marianbay.click

File này cung cấp hướng dẫn chi tiết về cách triển khai ứng dụng Marianbay.click sử dụng Docker, Docker Compose và Nginx.

## 1. Chuẩn bị môi trường

### Cài đặt Docker và Docker Compose

```bash
# Trên Ubuntu/Debian
sudo apt update
sudo apt install docker.io docker-compose

# Trên CentOS/RHEL
sudo yum install docker docker-compose

# Trên macOS (với Homebrew)
brew install docker docker-compose
```

## 2. Cấu hình SSL

Trước khi triển khai, bạn cần chuẩn bị chứng chỉ SSL cho tên miền marianbay.click.

```bash
# Tạo thư mục SSL
mkdir -p ssl
```

Đặt các file chứng chỉ SSL của bạn vào thư mục `ssl` với tên:
- `marianbay.click.crt` (chứng chỉ)
- `marianbay.click.key` (khóa riêng tư)

Nếu bạn chưa có chứng chỉ SSL, bạn có thể sử dụng Let's Encrypt để tạo miễn phí sau khi đã triển khai lên máy chủ.

## 3. Cấu hình DNS

Trỏ tên miền marianbay.click đến địa chỉ IP của máy chủ của bạn:

- Bản ghi A: marianbay.click -> [Địa chỉ IP của máy chủ]
- Bản ghi A: www.marianbay.click -> [Địa chỉ IP của máy chủ]

## 4. Triển khai ứng dụng

```bash
# Xây dựng và khởi động các container
docker-compose up -d --build
```

## 5. Kiểm tra

Sau khi triển khai, bạn có thể truy cập trang web của mình qua địa chỉ https://marianbay.click để kiểm tra xem mọi thứ đã hoạt động chưa.

## 6. Quản lý ứng dụng

```bash
# Xem logs
docker-compose logs -f

# Khởi động lại ứng dụng
docker-compose restart

# Dừng ứng dụng
docker-compose down
```

## 7. Cập nhật ứng dụng

Khi bạn cần cập nhật ứng dụng:

```bash
# Kéo code mới từ repository
git pull

# Xây dựng lại và khởi động các container
docker-compose up -d --build
```

## Lưu ý quan trọng

1. Đảm bảo rằng các cổng 80, 443, 3002 và 8082 không bị sử dụng bởi các ứng dụng khác trên máy chủ.
2. Nếu bạn thay đổi cấu hình trong file nginx.conf, bạn cần khởi động lại container nginx: `docker-compose restart nginx`.
3. Đảm bảo rằng bạn đã cấu hình đúng các biến môi trường trong file docker-compose.yml phù hợp với môi trường của bạn.
4. Nếu bạn gặp vấn đề với SSL, hãy kiểm tra lại các file chứng chỉ và đường dẫn trong file nginx.conf.