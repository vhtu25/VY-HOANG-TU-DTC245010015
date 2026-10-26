Đồ án Quản trị Hệ thống - Đề 3: Website Quảng bá Sản phẩm (WordPress)

Thông tin sinh viên:

Họ và tên: Vy Hoàng Tú

Mã sinh viên: DTC245010015

Repository: VY-HOANG-TU-DTC245010015

1. Danh sách các bước đã triển khai (Theo yêu cầu đồ án)

Dự án được thực hiện tuần tự và quản lý mã nguồn chặt chẽ trên GitHub, chia làm các giai đoạn tương ứng với các commit:

Quản lý mã nguồn: Tạo repo VY-HOANG-TU-DTC245010015 chứa toàn bộ cấu hình Docker Compose, Nginx, Prometheus.

Triển khai ứng dụng: Tích hợp Website (WordPress official image) + Database (MySQL) + DB Tool (phpMyAdmin).

Cấu hình Nginx (Commit 1): Thiết lập Nginx làm Reverse Proxy, bảo vệ các dịch vụ phía sau và áp dụng security headers cơ bản.

Tích hợp Giám sát (Commit 2 - Mục 4): Triển khai Prometheus, cAdvisor và Grafana để thu thập metrics và giám sát hệ thống container.

Hệ thống Log tập trung (Commit 3): Triển khai Loki và Promtail, cho phép truy vấn log của toàn bộ hệ thống bằng LogQL thông qua Grafana.

Hardening (Bảo mật): Áp dụng Network isolation (mạng nội bộ Docker), giới hạn port expose, và security headers.

Báo cáo: Hoàn thiện file Word báo cáo chi tiết cấu trúc, cách hoạt động và ảnh minh chứng.

2. Yêu cầu hệ thống (Prerequisites)

Hệ điều hành: Ubuntu Linux.

Công cụ: Docker và Docker Compose (version 2.x).

Khuyến nghị RAM trống tối thiểu 3GB.

3. Hướng dẫn khởi chạy (Deployment)

Bước 1: Clone repository

git clone https://github.com/vhtu25/VY-HOANG-TU-DTC245010015.git
cd VY-HOANG-TU-DTC245010015


Bước 2: Khởi chạy hệ thống bằng Docker Compose
(Lưu ý: Để tránh nghẽn tài nguyên (Deadlock), hệ thống được chia thành 3 nhóm để khởi động)

Nhóm 1: Hệ thống Web & Database

docker compose up -d db wordpress phpmyadmin nginx


Nhóm 2: Hệ thống Giám sát (Mục 4)

docker compose up -d prometheus grafana cadvisor


Nhóm 3: Hệ thống Log tập trung

docker compose up -d loki promtail


4. Hướng dẫn Demo và Truy cập các thành phần

Sau khi hệ thống khởi chạy (Started), sử dụng IP của máy ảo (ví dụ: http://192.168.153.128) để truy cập.

4.1. Demo Ứng dụng & CSDL (Mục 2 & 3)

Website WordPress: Truy cập qua Nginx tại http://192.168.153.128

phpMyAdmin: Truy cập tại http://192.168.153.128:8081

4.2. Demo Mục 4: Giám sát hệ thống (Prometheus + Grafana)

Mục này cung cấp giải pháp giám sát toàn diện cho container, web server và database.

Grafana Dashboard: http://192.168.153.128:3000 (User/Pass mặc định: admin / admin).

Prometheus: http://192.168.153.128:9090

Truy vấn PromQL cơ bản (Tại giao diện Explore của Grafana -> Nguồn Prometheus):

Xem RAM sử dụng: container_memory_usage_bytes

Xem trạng thái sống của hệ thống: up

Xem CPU usage: rate(container_cpu_usage_seconds_total[1m])

4.3. Demo Hệ thống Log (Loki + Promtail)

Truy cập Grafana (Port 3000) -> Chọn menu Explore -> Đổi Data source thành Loki.

Các câu lệnh LogQL demo:

Log tổng hợp Docker: {job="docker_containers"}

Log Web Server (Nginx): {job="docker_containers"} |= "nginx"

Log Ứng dụng (WordPress): {job="docker_containers"} |= "wp_app"

Log CSDL (MySQL): {job="docker_containers"} |= "mysql_db"

5. Kiến trúc Mạng & Bảo mật (Hardening)

Network Isolation: Tất cả container giao tiếp nội bộ qua bridge network wp-network. Database (MySQL) bị ẩn hoàn toàn khỏi internet.

Reverse Proxy: Mọi traffic web đều phải đi qua Nginx.

Security Headers: Nginx được cấu hình để ẩn phiên bản (server_tokens off;) nhằm tránh lỗi lộ lọt thông tin.
