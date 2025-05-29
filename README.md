# MQTT Service with Docker

## Giới thiệu
MQTT Service là một dịch vụ sử dụng giao thức MQTT (Message Queuing Telemetry Transport) để truyền nhận dữ liệu giữa các thiết bị. Dự án này cung cấp môi trường MQTT đóng gói sẵn trong Docker để dễ dàng triển khai.

## Yêu cầu hệ thống
- Docker
- Docker Compose

## Hướng dẫn cài đặt

### Phương pháp 1: Sử dụng Eclipse Mosquitto với Docker Compose (Khuyến nghị)

1. Clone repository này về máy của bạn:
```bash
git clone https://github.com/your-username/mqtt-server.git
cd mqtt-server
```

2. Tạo cấu trúc thư mục cần thiết:
```bash
mkdir -p mosquitto/config
mkdir -p mosquitto/data
mkdir -p mosquitto/log
```

3. Tạo file cấu hình Mosquitto:
```bash
cat > mosquitto/config/mosquitto.conf << EOF
listener 1883
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
allow_anonymous true
EOF
```

4. Tạo file docker-compose.yml:
```bash
cat > docker-compose.yml << EOF
version: '3'
services:
  mosquitto:
    image: eclipse-mosquitto:latest
    container_name: mqtt-broker
    ports:
      - "1883:1883"
    volumes:
      - ./mosquitto/config:/mosquitto/config
      - ./mosquitto/data:/mosquitto/data
      - ./mosquitto/log:/mosquitto/log
    restart: always
EOF
```

5. Khởi chạy MQTT broker:
```bash
docker-compose up -d
```

6. Kiểm tra trạng thái:
```bash
docker-compose ps
```

### Phương pháp 2: Sử dụng Docker đơn giản

Nếu bạn không cần cấu hình phức tạp, chỉ cần chạy lệnh sau:

```bash
docker run -d --name mqtt-broker \
  -p 1883:1883 \
  eclipse-mosquitto:latest
```

### Bảo mật với xác thực người dùng

1. Cập nhật file cấu hình `mosquitto/config/mosquitto.conf`:
```
listener 1883
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
allow_anonymous false
password_file /mosquitto/config/password
```

2. Tạo file password và thêm người dùng:
```bash
docker exec -it mqtt-broker mosquitto_passwd -c /mosquitto/config/password username
# Nhập password khi được yêu cầu
```

3. Khởi động lại dịch vụ:
```bash
docker-compose restart
```

## Tài liệu tham khảo
- [Eclipse Mosquitto](https://mosquitto.org/)
- [MQTT Protocol](https://mqtt.org/)
- [Mosquitto Docker Hub](https://hub.docker.com/_/eclipse-mosquitto)
- [Setup Mosquitto With Docker](https://github.com/sukesh-ak/setup-mosquitto-with-docker)