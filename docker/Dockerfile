# 第1阶段:构建应用程序
FROM --platform=$BUILDPLATFORM golang:1.25 AS build

WORKDIR /app

# 1. 先复制依赖文件
COPY go.mod go.sum ./

# 2. 下载依赖(这层会被缓存)
RUN go mod download

# 3. 再复制源代码
COPY . .

# 4. 编译
RUN go build -o dmp main.go 


# 第2阶段:运行时环境，基于基础镜像
FROM dmp-base:latest

WORKDIR /root
ENV HOME=/root

# 从构建阶段复制必要的文件
COPY --from=build /app/dmp /root/dmp
COPY --from=build /app/docker/entry-point.sh /root/entry-point.sh

RUN chmod +x /root/dmp /root/entry-point.sh

# 环境变量
# - 平台暴露端口,默认为80
ARG DMP_PORT=80
ENV DMP_PORT=${DMP_PORT}
# 是否以容器方式启动
ENV DMP_IN_CONTAINER=1

# web端口
EXPOSE 80/tcp

# 设置容器启动时执行的脚本
ENTRYPOINT ["/root/entry-point.sh"]
