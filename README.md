## Hướng dẫn deploy web tính tiền điện
### 1. Chạy máy ảo EC2 
sử dụng AWS CLI để thực hiện việc cài đặt các phần mềm, tiện ích cần thiết cho ứng dụng, gồm có:

  Apache server: 
  
    sudo yum update -y
    sudo yum install httpd -y // cài apache
    sudo systemctl start httpd // khởi động apache
    sudo systemctl enable httpd // khởi động apache chạy cùng hệ thống
  Git:
  
    sudo yum install git -y // cài đặt git
    git --version // kiểm tra cài đặt
  PHP và các module liên quan: 
  
    sudo yum install php php-mysqlnd php-pdo php-gd php-mbstring -y
    PHP -v // kiểm tra cài đặt PHP
  Composer:
  
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    
  Clone dự án: di chuyển đến thư mục var/www/http
  
    git clone https://github.com/k3043/e.git
    cd eCalc // vào trong thư mục chính
    composer install // để cài đặt tất cả các thư viện và phụ thuộc có liên quan được quy định trong file composer.json 
Viết file cấu hình để hướng dẫn Apache, đảm bảo máy chủ web hoạt động đúng cách

    sudo vi /etc/httpd/conf.d/app.conf

   Điền vào file vừa tạo
   
    <VirtualHost *:80>
      DocumentRoot /var/www/html/eCalc/public
      ServerName localhost
      <Directory /var/www/html>
          AllowOverride All
          Require all granted
      </Directory>
    </VirtualHost>

  Thiết lập quyền sở hữu và quyền truy cập
  
    sudo chown -R ec2-user:ec2-user /var/www/html/eCalc
    sudo chmod -R 755 /var/www/html/eCalc

  Quay trở lại var/www/http/eCalc

    cp env.example .env
    nano .env // điền thông tin của database để kết nối tới database sau đó lưu và thoát ra ngoài
    cat .env // sau khi lưu và thoát có thể kiểm tra lại thông tin tệp vừa lưu
  Chạy lại server và kiểm tra kết quả
  
    sudo apachectl configtest //kiểm tra cấu hình
    sudo systemctl restart httpd // khởi động lại server

  Đảm bảo EC2 Instance cho phép lưu lượng truy cập từ  HTTP (port 80), copy và paste public Ipv4 hoặc public DNS vào trình duyệt web để kiểm tra kết quả
  
  Nếu gặp lỗi từ chối truy cập, chú ý cấp quyền truy cập tương ứng cho file đó bằng cách sử dụng  sudo chmod -R 755



