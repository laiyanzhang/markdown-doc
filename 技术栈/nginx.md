# nginx

标签（空格分隔）： 技术栈

---

## 1.基本概念
- 正向代理与反向代理

|类型	|正向代理	|反向代理|
| -- | -- | -- |
|代理对象	|客户端	|服务器
|客户端感知	|知道代理存在	|不知道代理存在|
|主要用途	|突破访问限制、匿名访问	|负载均衡、安全防护、缓存加速|
|典型场景	|科学上网、公司内网代理	|网站接入、API网关|


- 核心功能
  - 请求转发：将客户端请求转发到后端服务器
  - 负载均衡：在多台服务器间分配流量
  - SSL终端：处理HTTPS加密/解密
  - 缓存加速：缓存静态内容减轻后端压力
  - 安全防护：隐藏后端服务器，防范DDoS攻击
- 特点
  - 高性能：高并发连接处理能力、低资源消耗
  - 稳定性：高负载情况下依然保持稳定运行，可以在不停止服务的情况下进行升级或配置更改
- 原理图解
```
客户端 → Nginx(反向代理) → 后端服务器(实际处理请求)
       ↑                ↑
       外部访问地址     内部服务器地址
```

- 典型代理配置

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/html;  # 静态资源根目录
    index index.html;     # 默认入口文件
    
    location / {
        try_files $uri $uri/ /index.html;  # 单页应用路由处理
        proxy_pass http://backend_server;  # 转发到后端服务器
        proxy_set_header Host $host;      # 传递原始主机头
        proxy_set_header X-Real-IP $remote_addr;  # 传递客户端真实IP
    }
}

upstream backend_server {
    server 192.168.1.100:8080;  # 后端服务器地址
    server 192.168.1.101:8080;  # 可以定义多个实现负载均衡
}
```

## 2.核心指令详解
- 定义后端服务器地址
```nginx
location /api/ {
    proxy_pass http://backend_servers;
    # 注意：尾部有/时，会去除匹配的前缀
    # proxy_pass http://backend/;  # /api/foo → /foo
}
```
- 请求头处理
```nginx
proxy_set_header Host $host;             # 保留原始主机头
proxy_set_header X-Real-IP $remote_addr; # 客户端真实IP
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # 代理链
```
- 超时控制
```nginx
proxy_connect_timeout 5s;    # 连接超时
proxy_send_timeout 10s;      # 发送超时
proxy_read_timeout 30s;      # 读取超时
```
- 缓冲与缓存
```nginx
proxy_buffering on;                  # 启用缓冲
proxy_buffer_size 4k;                # 缓冲区大小
proxy_buffers 8 16k;                 # 缓冲块数量及大小
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m inactive=60m;
proxy_cache my_cache;                # 启用缓存
```


## 3.实际应用场景
- 负载均衡
```nginx
upstream backend {
    least_conn;               # 最少连接算法
    server 10.0.0.1:80 weight=3;  # 权重
    server 10.0.0.2:80;
    server 10.0.0.3:80 backup;    # 备用服务器
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```
- 动静分离
```nginx
server {
    location /static/ {
        root /var/www/static;  # 直接处理静态文件
        expires 30d;          # 设置缓存过期
    }
    
    location / {
        proxy_pass http://app_server;  # 动态请求转发
    }
    
    location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
        expires 30d;          # 缓存过期时间
        add_header Cache-Control "public, no-transform";
        access_log off;       # 关闭日志减少IO
    }
}
```
- API网关
```nginx
location /user-service/ {
    rewrite ^/user-service/(.*) /$1 break;  # URL重写
    proxy_pass http://user_service;
}

location /order-service/ {
    rewrite ^/order-service/(.*) /$1 break;
    proxy_pass http://order_service;
}
```
- 跨域处理
```nginx
location /api/ {
    add_header 'Access-Control-Allow-Origin' '$http_origin';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,Content-Type';
    add_header 'Access-Control-Allow-Credentials' 'true';
    
    if ($request_method = 'OPTIONS') {
        return 204;
    }
    
    proxy_pass http://api_server;
}
```
- 启用http/2
```nginx
server {
    listen 443 ssl http2;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
}
```



## 4.性能优化技巧
- 连接池优化
```nginx
upstream backend {
    keepalive 32;  # 保持长连接
    server 10.0.0.1:8080;
}
```
- 缓冲区优化
```nginx
proxy_buffers 8 16k;
proxy_buffer_size 32k;
```
- 启用Gzip压缩
```nginx
gzip on;
gzip_types text/plain application/json;
```
- 缓存静态内容
```nginx
proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=my_cache:10m inactive=60m;
location / {
    proxy_cache my_cache;
    proxy_cache_valid 200 302 10m;
}
```


## 5.安全配置
- 限制访问
```nginx
location /admin/ {
    allow 192.168.1.0/24;
    deny all;
    proxy_pass http://backend;
}
```
- 速率限制
```nginx
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;

location /api/ {
    limit_req zone=api_limit burst=20;
    proxy_pass http://api_backend;
}
```
- 隐藏头部信息
```nginx
proxy_hide_header X-Powered-By;
server_tokens off;
```



## 6.常见问题排查
- 502 Bad Gateway
  - 检查后端服务是否运行
  - 检查Nginx错误日志：tail -f /var/log/nginx/error.log
  - 调整超时设置
- 413 Request Entity Too Large：`client_max_body_size 20M;  # 增加最大请求体大小`
- WebSocket连接问题
  - 确保配置了Upgrade和Connection头
  - 检查防火墙设置



## 7.nginx命令
- 启动nginx：`start nginx`
- 更新nginx：`nginx -s reload`
- 关闭nginx：`nginx -s quit`
- 查看当前所有nginx进程：`tasklist /fi "imagename eq nginx.exe"`
- 终止当前所有nginx进程：`taskkill /im nginx.exe /f`


## 8.后端跨域处理（推荐）
- 更细粒度的控制：可以基于业务逻辑动态决定是否允许跨域
- 安全性更高：可以结合认证系统做更严格的来源验证
- 减少Nginx配置复杂度：保持Nginx配置简洁，专注于反向代理和负载均衡
- 开发环境一致性：开发时可以直接连接后端服务，无需Nginx代理


## 9.Docker
在 Vue 项目中，通过 Docker 可以实现 自动化构建、部署和运行，无需手动配置 Nginx 或管理服务器环境。

### 自动化核心思路
- 通过 Docker 实现以下自动化：
  - 构建：将 Vue 项目打包成静态文件（dist）。
  - 托管：用 Nginx 镜像托管静态文件，并自动应用配置（nginx.conf）。
  - 运行：一键启动容器，无需手动操作服务器。

### 具体实现步骤
- 准备 Vue 项目，确保项目根目录有：
  - Dockerfile（定义构建流程）
  - nginx.conf（Nginx 配置）
  - docker-compose.yml（可选，简化多容器管理）
- 示例项目结构
```
my-vue-app/
├── dist/                  # 构建后生成（无需手动创建）
├── src/                   # Vue 源码
├── nginx.conf             # Nginx 配置
├── Dockerfile             # Docker 构建文件
└── docker-compose.yml     # （可选）容器编排
```
- 编写 nginx.conf

```nginx
// 配置 Nginx 托管 dist 文件并支持 Vue Router 的 history 模式
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 代理后端 API（可选）
    location /api {
        proxy_pass http://backend:3000;
    }
}
```
- 编写 Dockerfile
```bash
# 阶段1：构建阶段用 Node 镜像打包 Vue 项目
FROM node:16 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 阶段2：运行阶段用 Nginx 镜像托管 dist 文件
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
- 构建并运行容器（手动方式）
```bash
# 1. 构建镜像
docker build -t my-vue-app .

# 2. 运行容器
docker run -d -p 8080:80 my-vue-app

// 访问 http://localhost:8080 即可看到部署的 Vue 应用。
```
- 构建并运行容器（自动化优化）
```
// 编写docker-compose.yml 管理
version: '3'
services:
  frontend:
    build: .
    ports:
      - "8080:80"
    restart: always
    
// 运行：docker-compose up -d
```

### 高级自动化技巧
- 结合 CI/CD（如 GitHub Actions）
```
// 在 .github/workflows/deploy.yml 中配置自动化构建和推送镜像
name: Deploy Vue App
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: docker build -t my-vue-app .
      - run: docker run -d -p 8080:80 my-vue-app
```
- 使用多阶段构建优化镜像
```
// dockerfile
# 阶段1：构建
FROM node:16 AS build
RUN npm install -g pnpm
WORKDIR /app
COPY . .
RUN pnpm install && pnpm run build

# 阶段2：精简运行镜像
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY --from=build /app/nginx.conf /etc/nginx/conf.d/default.conf
```
- 环境变量注入
```
// dockerfile：通过 Docker 传递 Vue 的环境变量
ARG API_URL
ENV VUE_APP_API_URL=$API_URL
```
- 构建时指定变量
```
docker build --build-arg API_URL=https://api.example.com -t my-vue-app .
```

### 验证自动化效果
- 检查容器是否运行：`docker ps`
- 查看日志：`docker logs <container-id>`
- 进入容器调试：`docker exec -it <container-id> sh`


### 常见问题解决
- 问题1：Nginx 返回 404（Vue Router history 模式）
- 解决：确保 nginx.conf 包含 try_files $uri $uri/ /index.html。
- 问题2：端口冲突
- 解决：修改 docker-compose.yml 中的端口映射（如 "3000:80"）。
- 问题3：构建缓慢
- 解决：利用 Docker 层缓存，优先复制 package.json 再安装依赖：
```
// dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```

### 总结
- 通过 Docker 自动化部署 Vue 项目的核心步骤：
  - 编写 `Dockerfile`：分阶段构建（Node 构建 + Nginx 运行）。
  - 配置 `nginx.conf`：处理路由、代理等需求。
  - 一键运行：`docker build` + `docker run` 或 `docker-compose up`。
  - 扩展优化：结合 CI/CD、多阶段构建、环境变量等。
  - 最终实现：代码推送后，自动构建镜像并部署，无需手动操作服务器。




