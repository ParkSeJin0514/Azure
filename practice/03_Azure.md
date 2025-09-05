# 📘 09.03 Azure
## 🖥 NGINX Reverse Proxy
### VM 생성 후 설정
- Ubuntu Server 22.04 LTS 설치
- NGINX 설치
```bash
sudo apt update
sudo apt install nginx
```
- NGINX 시작 및 활성화
```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```
- NGINX 리버스 프록시 설정 파일 생성 또는 수정
- 새 설정 파일 생성
```bash
sudo vi /etc/nginx/sites-available/reverse-proxy
```
### reverse-proxy 수정
- 설정 내용
```bash
server {
    listen 80;
    server_name <PROXY SERVER IP or DOMAIN>;

    location /api/ {
        proxy_pass http://<PRIVATE IP:PORT>/api;  # 내부 API 서버 주소
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

샘플:
server {
    listen 80;
    server_name 52.149.152.216;

    location /api/ {
        proxy_pass http://10.16.3.4:3000/api/;  # 내부 API 서버 주소
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # CORS 설정 추가
        #add_headier Access-Control-Allow-Origin *;
        #add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        #add_header Access-Control-Allow-Headers 'Origin, X-Requested-With, Content-Type, Accept, Authorization';
    }
}
```
- 심볼릭 링크 생성
```bash
sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
```
- NGINX 설정 파일 검증
```bash
sudo nginx -t
```
- 설정 적용
```bash
sudo nginx -s reload
```






