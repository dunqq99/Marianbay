# Hướng dẫn triển khai Marianbay.click trên Ubuntu VPS

File này cung cấp hướng dẫn chi tiết về cách triển khai ứng dụng Marianbay.click trên Ubuntu VPS sử dụng Docker, Docker Compose và Nginx.

## 1. Chuẩn bị môi trường Ubuntu VPS

### Cập nhật hệ thống

```bash
sudo apt update
sudo apt upgrade -y
```

### Cài đặt các gói cần thiết

```bash
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common git
```

### Cài đặt Docker và Docker Compose

```bash
# Cài đặt Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Thêm user hiện tại vào nhóm docker để không cần sudo
sudo usermod -aG docker $USER

# Khởi động Docker
sudo systemctl enable docker
sudo systemctl start docker

# Cài đặt Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## 2. Tải mã nguồn Marianbay

```bash
# Tạo thư mục cho ứng dụng
mkdir -p ~/marianbay
cd ~/marianbay

# Clone repository (thay thế URL bằng repository thực tế của bạn)
git clone https://github.com/yourusername/marianbay.git .
# Hoặc sao chép mã nguồn từ máy local lên VPS bằng scp hoặc rsync
```

## 3. Cấu hình SSL

### Tạo thư mục SSL

```bash
mkdir -p ~/marianbay/ssl
```

### Sử dụng Let's Encrypt để tạo chứng chỉ SSL miễn phí

```bash
# Cài đặt Certbot
sudo apt install -y certbot

# Tạo chứng chỉ SSL (đảm bảo domain đã trỏ đến IP của VPS)
sudo certbot certonly --standalone -d marianbay.click -d www.marianbay.click

# Sao chép chứng chỉ vào thư mục ssl
sudo cp /etc/letsencrypt/live/marianbay.click/fullchain.pem ~/marianbay/ssl/marianbay.click.crt
sudo cp /etc/letsencrypt/live/marianbay.click/privkey.pem ~/marianbay/ssl/marianbay.click.key

# Cấp quyền cho user hiện tại
sudo chown -R $USER:$USER ~/marianbay/ssl
```

## 4. Cấu hình DNS

Đăng nhập vào trang quản lý DNS của nhà cung cấp tên miền và tạo các bản ghi sau:

- Bản ghi A: marianbay.click -> [Địa chỉ IP của VPS]
- Bản ghi A: www.marianbay.click -> [Địa chỉ IP của VPS]

## 5. Cấu hình tường lửa

```bash
# Cài đặt UFW (Uncomplicated Firewall)
sudo apt install -y ufw

# Cấu hình UFW
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Kích hoạt tường lửa
sudo ufw enable
```

## 6. Triển khai ứng dụng

```bash
# Di chuyển đến thư mục dự án
cd ~/marianbay

# Xây dựng và khởi động các container
docker-compose up -d --build
```

## 7. Kiểm tra

Sau khi triển khai, bạn có thể truy cập trang web của mình qua địa chỉ https://marianbay.click để kiểm tra xem mọi thứ đã hoạt động chưa.

## 8. Quản lý ứng dụng

```bash
# Xem logs
docker-compose logs -f

# Khởi động lại ứng dụng
docker-compose restart

# Dừng ứng dụng
docker-compose down
```

## 9. Cập nhật ứng dụng

Khi bạn cần cập nhật ứng dụng:

```bash
# Kéo code mới từ repository
git pull

# Xây dựng lại và khởi động các container
docker-compose up -d --build
```

## 10. Tự động gia hạn chứng chỉ SSL

```bash
# Thêm cronjob để tự động gia hạn chứng chỉ SSL
sudo crontab -e
```

Thêm dòng sau vào file:

```
0 3 * * * certbot renew --quiet && cp /etc/letsencrypt/live/marianbay.click/fullchain.pem /home/yourusername/marianbay/ssl/marianbay.click.crt && cp /etc/letsencrypt/live/marianbay.click/privkey.pem /home/yourusername/marianbay/ssl/marianbay.click.key && docker-compose -f /home/yourusername/marianbay/docker-compose.yml restart nginx
```

## Lưu ý quan trọng

1. Đảm bảo rằng các cổng 80, 443, 3002 và 8082 không bị sử dụng bởi các ứng dụng khác trên máy chủ.
2. Nếu bạn thay đổi cấu hình trong file nginx.conf, bạn cần khởi động lại container nginx: `docker-compose restart nginx`.
3. Đảm bảo rằng bạn đã cấu hình đúng các biến môi trường trong file docker-compose.yml phù hợp với môi trường của bạn.
4. Nếu bạn gặp vấn đề với SSL, hãy kiểm tra lại các file chứng chỉ và đường dẫn trong file nginx.conf.
5. Đảm bảo rằng user www-data có quyền truy cập vào các thư mục cần thiết.

## Khắc phục sự cố

### Kiểm tra logs của Nginx

```bash
docker-compose logs nginx
```

### Kiểm tra cấu hình Nginx

```bash
docker exec -it marianbay_nginx_1 nginx -t
```

### Kiểm tra kết nối đến các container

```bash
# Kiểm tra kết nối đến frontend
docker exec -it marianbay_nginx_1 curl -I http://frontend:3002

# Kiểm tra kết nối đến backend
docker exec -it marianbay_nginx_1 curl -I http://backend:8082
```