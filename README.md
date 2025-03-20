# Three-Container Server

## 项目介绍
与Chat-Box配合使用，可实现API密钥的隐式分配，保证了API key的隐私性


## 技术栈
- **Node.js**：后端服务器。
- **Express**：Web 框架。
- **Redis**：数据存储。
- **Winston**：日志记录。
- **Node-cron**：定时任务。

## 功能特性
- **密钥分配**：通过 `/api-keys/allocate` 接口分配可用密钥。
- **密钥释放**：通过 `/release` 接口释放已使用的密钥。
- **自动回收**：每小时自动回收过期密钥。
- **日志记录**：使用 Winston 记录所有操作日志。
- **跨域支持**：支持跨域请求（CORS）。
- **Redis 集成**：使用 Redis 存储密钥池和分配记录。

## 开始
部署前阅读

### 依赖
#### 部署Redis

首先，安装编译 Redis 所需的依赖包：
```bash
sudo apt update
sudo apt install build-essential tcl
```

* 下载 Redis
```bash
wget https://download.redis.io/releases/redis-7.0.0.tar.gz
tar xzf redis-7.0.0.tar.gz
cd redis-7.0.0
```

* 编译
```bash
make
#Test
make test
```

* 安装Redis
```bash
sudo make install
```

* 启动
```bash
redis-server
```

* 配置Redis
```bash
sudo nano /etc/redis/redis.conf
```
* bind：绑定 IP 地址（默认为 127.0.0.1）
* port：设置 Redis 端口（默认为 6379）

#### 安装npm依赖
* 安装
```bash
sudo apt update
sudo apt install nodejs npm -y
```

* 初始化
```bash
cd /path/to/your/project/Server
npm init -y
npm install
```

* 运行
```bash
node Server.js
```
### 调整
* 调整cors
```Javascript
// CORS 配置
const corsOptions = {
  origin: [
    "http://localhost:5500", // Live Server 默认端口
    "http://127.0.0.1:5500", // 本地 IP 访问
    "http://127.0.0.1:5501",
    "http://localhost:3000", // 前端独立部署时使用
  ],
  methods: "GET,POST,PUT,DELETE,OPTIONS",
  allowedHeaders: ["Content-Type", "Authorization"],
  credentials: true, //允许跨域请求携带 cookies。
  optionsSuccessStatus: 204,
};
```
* 添加API密钥
```Javascript
// 初始化密钥池
const initializeKeyPool = async () => {
  try {
    logger.info("🔍 检查密钥池...");
    if (redis.status !== "ready") {
      await new Promise((resolve) => {
        redis.once("connect", resolve);
      });
    }
    const exists = await redis.exists("key_pool");

    if (!exists) {
      logger.info("⚙️ 正在初始化密钥池");
      await redis.hmset(
        "key_pool",
        "Your key here",
        JSON.stringify({ max: 100, used: 0 }),
        "Your key here",
        JSON.stringify({ max: 100, used: 0 }),
        "Your key here",
        JSON.stringify({ max: 100, used: 0 })
      );
      logger.info("✅ 密钥池初始化完成");
    }
  } catch (err) {
    logger.error("❌ 初始化失败", { error: err.stack });
    process.exit(1);
  }
};
```

## 许可证

根据 MIT 许可证分发。打开 [LICENSE.md](LICENSE.md) 查看更多内容。

## 开发日志

*[ai-chatbox标签的模拟实现]([https://blog.csdn.net/2401_87362551/article/details/146158267?spm=1001.2014.3001.5501](https://blog.csdn.net/2401_87362551/article/details/146168935?spm=1001.2014.3001.5502))

