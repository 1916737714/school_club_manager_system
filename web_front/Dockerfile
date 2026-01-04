# 仅需Nginx基础镜像，无需Node
FROM nginx:alpine

# 1. 复制自定义 Nginx 配置到容器的 Nginx 配置目录
# （覆盖默认的 default.conf，Nginx 启动时会加载这个配置）
COPY ./nginx.conf /etc/nginx/conf.d/default.conf

# 2. 复制本地dist目录到Nginx默认静态文件目录
COPY ./dist/ /usr/share/nginx/html/

# 3. 暴露 8080 端口（而非 80，匹配配置里的监听端口）
EXPOSE 8080