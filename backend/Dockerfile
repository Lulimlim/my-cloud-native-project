# --- 第一阶段：构建阶段 (Build Stage) ---
# 使用一个包含Maven和Java开发环境的镜像作为基础
FROM maven:3.8.5-openjdk-17 AS builder

# 设置工作目录
WORKDIR /app

# 拷贝 pom.xml 文件，目的是利用Docker的缓存机制
# 只要pom.xml不变，依赖层就不会重新下载
COPY pom.xml .

# 下载所有依赖
RUN mvn dependency:go-offline

# 拷贝所有源代码
COPY src/ ./src/

# 执行打包命令
RUN mvn package -DskipTests


# --- 第二阶段：运行阶段 (Final Stage) ---
# 使用一个非常轻量的、只包含Java运行环境的镜像
FROM openjdk:17-jre-slim

# 设置工作目录
WORKDIR /app

# 从第一阶段(builder)的编译结果中，拷贝出打包好的jar文件
COPY --from=builder /app/target/vhr-0.0.1-SNAPSHOT.jar ./app.jar

# 声明容器对外暴露的端口号（vhr项目默认是8081）
EXPOSE 8081

# 定义容器启动时执行的命令
ENTRYPOINT ["java", "-jar", "app.jar"]
