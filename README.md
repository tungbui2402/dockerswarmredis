# Dockerswarmredis
## Docker swarm
Docker Swarm là một công cụ điều phối container chạy trên nền tảng Docker. Nó giúp người dùng tạo và quản lý một cụm nút Docker. Phân cụm trong Docker là một khái niệm quan trọng trong việc cung cấp khả năng dự phòng bằng cách cho phép Docker Swarm chuyển đổi dự phòng nếu một hoặc nhiều nút trong cụm bị lỗi.

Docker Swarm sử dụng API Docker tiêu chuẩn để giao tiếp với các công cụ khác như Docker Engine. Nó chỉ định các vùng chứa cho các nút công nhân một cách thông minh và đảm bảo tối ưu hóa tài nguyên bằng cách lên lịch khối lượng công việc của vùng chứa để chạy trên (các) nút phù hợp nhất.

### Thiết lập
3 server:
- Máy chủ: 10.0.5.100
- Máy con 1: 10.0.5.101
- Máy con 2: 10.0.5.102

Mỗi máy yêu cầu phải cài docker.

### Tiến hành
B1: Thiết lập host trên các máy bằng lệnh `nano /etc/hosts`:
```
swarm-manager          10.0.5.100
worker-node-1          10.0.5.101
worker-node-2          10.0.5.102
```
Sau khi thêm các máy vào thì ở mỗi máy chạy lệnh ping ip 2 máy còn lại để kiểm tra kết nối
B2: Cài docker ở trên mỗi máy: https://github.com/tungbui2402/docker2/blob/main/README.md
B3: Tạo docker swarm cluster:
- Để tạo docker swarm chạy command:
```
sudo docker swarm init --advertise-addr 10.0.5.100
```
- Khi Docker Swarm đã được khởi tạo, lệnh nối các nút công nhân với cụm sẽ được hiển thị trên thiết bị đầu cuối.Tiếp theo, đăng nhập lại vào từng note trên máy server và dán lệnh để tham gia cụm.
Ví dụ như token là `SWMTKN-1-1k397e5o52cae0yipopqcu9werjcwuss1exbyj4635rrjjl723-7ocx56uhb7p1ri7h2u6ynxyno`
```
sudo docker swarm join --token SWMTKN-1-1k397e5o52cae0yipopqcu9werjcwuss1exbyj4635rrjjl723-7ocx56uhb7p1ri7h2u6ynxyno 10.128.0.57:2377
```
- Output: `This node joined a swarm as a worker`
- Xác nhận rằng tất cả các nút đã tham gia cụm như sau:
```
sudo docker note ls
```
![Screenshot (55)](https://github.com/tungbui2402/dockerswarmredis/assets/129025623/90cca6fa-edfd-4810-b889-aeb45fab97e3)

B4: Test:
Sử dụng docker swarm để cài đặt nginx:
- Trên máy chủ, dùng lệnh:
```
sudo docker service create --name web-server --publish 8080:80 nginx:latest
```
- Tiếp theo, xác minh trạng thái dịch vụ ứng dụng đã triển khai:
```
sudo docker service ls
```
- Tạo bản sao trên các máy server
```
sudo docker service scale web-server=3
```
- Sau đó kiểm tra tình trạng trên máy server:
```
sudo docker service ls
```
Kiểm tra bằng cách vào ip 3 máy và thêm `:8080` vào sau ip.

## Docker Redis
