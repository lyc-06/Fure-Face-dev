# Fure-Face（映美）

面向面部美学分析与智能推荐的前后端分离平台。用户上传人脸照片后，可以获得面部特征分析、相似度比对、医美方案参考和 AI 咨询服务。

> 本项目的分析结果和医美建议仅供信息参考，不构成医疗诊断或治疗意见。

## 核心功能

- **面部分析**：检测人脸，分析面部比例、对称性和肤质等特征。
- **人脸比对**：对两张人脸进行特征比对并输出相似度结果。
- **智能咨询**：支持文本和图片分析，提供医美领域 AI 问答及流式对话。
- **方案推荐**：根据分析结果展示分级医美方案，支持术前术后对比和年龄模拟。
- **账户管理**：支持注册登录、验证码、个人资料、头像、密码及手机号/邮箱管理。
- **平台能力**：提供 JWT 鉴权、接口限流、历史记录、深色/浅色主题和中英文切换。

## 技术概览

- **前端**：Vue 3、Element Plus、Pinia、原生 Fetch、CSS
- **后端**：Java 21、Spring Boot、Spring Cloud、MyBatis-Plus
- **基础设施**：MySQL、Redis、Nacos、MinIO、Flyway
- **AI 与视觉**：OpenCV/JavaCV、OpenAI 兼容接口、DeepSeek、豆包视觉模型

## 项目结构

```text
frontend/       前端单页面应用、接口封装和页面组件
backend/        Maven 多模块后端：认证、用户、人脸、AI、网关和公共模块
test/           独立功能原型和演示页面
```

## 快速开始

### 环境要求

- JDK 21+
- Maven 3.8+
- MySQL 8.0+
- Redis 7+
- Nacos 2.x
- MinIO

### 启动后端

1. 创建数据库：

```bash
mysql -u root -p -e "CREATE DATABASE IF NOT EXISTS facefure DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
```

2. 启动 Nacos、Redis 和 MinIO，并在 MinIO 中创建 `facefure` bucket。

3. 修改各模块的 `application*.yml`，配置数据库、Redis、Nacos、MinIO、邮件、短信和 AI 服务。配置文件中的 `xxxxxxxxxx` 和 `your-secret-key` 都是占位值，不能直接用于生产环境。

4. 编译并启动服务：

```bash
cd backend
mvn clean install -DskipTests
```

在独立终端中按以下顺序启动：

```bash
mvn -pl facefure-auth spring-boot:run
mvn -pl facefure-user spring-boot:run
mvn -pl facefure-face spring-boot:run
mvn -pl facefure-ai spring-boot:run
mvn -pl facefure-gateway spring-boot:run
```

默认端口：网关 `9000`，认证服务 `8081`，用户服务 `9002`，AI 服务 `9003`；人脸服务端口以其模块配置为准。

### 启动前端

```bash
cd frontend
python -m http.server 8080
```

访问 `http://localhost:8080`。生产环境可使用 Nginx 等静态文件服务器托管 `frontend/`，并将 API 请求代理到网关 `http://localhost:9000`。

## 部署配置

运行完整系统需要以下外部服务：

- **MySQL**：数据库名为 `facefure`。用户和 AI 模块启用 Flyway 后，会自动执行各自 `db/migration` 目录中的迁移脚本。
- **Redis**：认证、用户、AI 和网关限流依赖 Redis，默认地址为 `localhost:6379`。
- **Nacos**：用于服务注册和配置管理，默认地址为 `localhost:8848`。
- **MinIO**：用于保存头像和人脸相关图片，bucket 名称为 `facefure`。
- **邮件/短信**：用户验证码功能需要 QQ SMTP 和阿里云短信配置。
- **AI 服务**：AI 模块需要 OpenAI 兼容接口的 API key；前端使用的 DeepSeek、豆包等密钥也需要单独配置。

生产环境请使用环境变量或外部配置中心管理密钥，不要把数据库密码、JWT 密钥、邮箱密码、短信凭据、API key 或 MinIO 密钥提交到 Git。生产部署还应启用 HTTPS、限制 CORS 来源、关闭调试日志，并为各基础设施服务设置独立账号和强密码。

## 接口文档

后端启动后，可通过 Swagger/Knife4j 查看接口详情：

```text
http://localhost:9000/swagger-ui/
```

主要接口包括登录注册、用户信息、头像上传、AI 对话、人脸检测和人脸比对，具体请求参数以在线接口文档为准。

## 已知限制

- `backend/pom.xml` 当前声明了 `facefure-api` 子模块，但仓库中没有对应目录；需要补充该模块或从父 POM 的 `<modules>` 中移除声明后再执行完整 Maven 构建。
- AI 能力依赖第三方服务，模型、额度、接口兼容性和输出内容由服务商决定。
- 人脸结果受图片质量、光照、姿态和模型能力影响，不应作为唯一决策依据。
- 人脸图片、面部特征和用户资料属于敏感信息，部署方应自行落实访问控制、保留期限和删除机制。

## 开发与贡献

提交代码前建议执行：

```bash
cd backend
mvn clean verify
```

提交 Issue 或 Pull Request 时，请提供运行环境、复现步骤、预期结果和实际结果，并对人脸图片、账号信息和 API 密钥进行脱敏。

## 许可证

本项目采用 [MIT License](LICENSE) 开源。第三方依赖、字体、图标、图片、模型和外部 API 可能受其各自许可证或服务条款约束，请分别确认授权范围。
