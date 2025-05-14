# WebSocket

标签（空格分隔）： 技术栈

---

## 1.理论基础

### 1.协议工作原理
- 工作原理分为三个阶段
  - 握手阶段：客户端通过HTTP Upgrade头发起请求，服务器响应101状态码完成协议切换
  - 数据传输阶段：建立持久连接后，双方可以随时发送数据帧，无需每次建立连接
  - 关闭阶段：通过关闭握手终止连接

### 2.核心作用
- 实时双向通信：突破HTTP请求-响应模式的限制
- 低延迟：建立连接后无需重复握手
- 高效：相比HTTP轮询，减少不必要的数据传输
- 持久连接：一次连接，长期使用


### 3.与HTTP长轮询、SSE的区别
|特性	|WebSocket	|HTTP长轮询	|SSE (Server-Sent Events)|
| -- | -- | --| -- |
|通信方向	|全双工	|半双工	|服务器到客户端单向|
|协议	|独立协议	|HTTP	|HTTP|
|连接性质	|持久连接	|每次请求新连接	|持久连接|
|数据格式	|二进制/文本	|文本(通常)	|文本|
|延迟	|低	|高	|中等|
|适用场景	|实时交互应用	|兼容性要求高场景	|服务器推送场景|


### 4.常见应用场景
- 实时聊天应用
- 实时数据监控/仪表盘
- 多人协作编辑
- 在线游戏
- 实时通知系统



### 5.安全考虑
- 安全隐患
  - 跨站WebSocket劫持(CSWSH)
  - 未加密通信的数据泄露
  - 拒绝服务攻击(DoS)
  - 消息注入攻击
  - 中间人攻击
- 防范措施
  - 使用WSS：始终使用wss://(WebSocket Secure)而非ws://
  - 验证Origin头：服务器端检查Origin头是否在白名单内
  - 输入验证：验证所有接收的数据
  - 速率限制：防止客户端发送过多消息
  - 认证授权：连接时发送认证令牌
- 在握手阶段使用Token验证
```javascript
const socket = new WebSocket('wss://example.com');
socket.onopen = () => {
  socket.send(JSON.stringify({
    type: 'auth',
    token: 'your-auth-token'
  }));
};
```

### 6.性能优化
- 大规模并发连接下可能存在问题
  - 服务器资源消耗大（内存、CPU）
  - 操作系统级别的连接数限制
  - 网络带宽瓶颈
  - 心跳检测带来的额外开销
- 性能优化手段
  - 连接管理：使用连接池，实现合理的连接超时和断开策略
  - 架构优化：使用负载均衡分散连接，考虑使用WebSocket网关(如Socket.io集群)
  - 数据优化：压缩消息内容、使用二进制格式代替文本、实现消息批处理
  - 心跳优化：动态调整心跳间隔、只在空闲时发送心跳
  - 客户端优化：实现退避重连算法、按需连接(非活动时断开)
- 二进制数据传输
```javascript
// 发送二进制数据
socket.binaryType = 'arraybuffer';
socket.onmessage = (event) => {
  const data = new Uint8Array(event.data);
  // 处理二进制数据
};
```


## 2.前端vue搭建
```javascript
class WebSocketService {
  constructor() {
    this.listeners = {};
    this.reconnectDelay = 5000; // 重连延迟 5秒
  }

  connect() {
    if(process.env.NODE_ENV === 'production') this.socket = new WebSocket('/ws');
    else if(process.env.NODE_ENV === 'development') this.socket = new WebSocket(`ws://localhost:3000`);
    
    this.socket.onopen = () => {
      console.log('WebSocket连接创建成功');
    };
    
    this.socket.onmessage = (event) => {
      const data = JSON.parse(event.data);
      if (this.listeners[data.type]) {
        this.listeners[data.type].forEach(callback => callback(data));
      }
    };
    
    this.socket.onclose = (event) => {
      if (event.code === 1000) {
        console.log('WebSocket连接正常关闭');
      } else {
        console.log('WebSocket连接关闭，尝试重连...');
        setTimeout(() => this.connect(), this.reconnectDelay);
      }
    };
    
    this.socket.onerror = (error) => {
      console.error('WebSocket错误:', error);
    };
  }

  send(type, data) {
    if (this.socket && this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(JSON.stringify({ type, data }));
    } else {
      console.error('WebSocket未连接，无法发送消息');
    }
  }

  on(type, callback) {
    if (!this.listeners[type]) {
      this.listeners[type] = [];
    }
    this.listeners[type].push(callback);
  }

  off(type, callback) {
    if (this.listeners[type]) {
      this.listeners[type] = this.listeners[type].filter(cb => cb !== callback);
    }
  }

  close() {
    if (this.socket) {
      this.socket.close(1000);
    }
  }
}

export default new WebSocketService();
```


## 3.后端nodejs搭建
### 1.使用原生ws库 + HTTPS

```javascript
const https = require('https');
const fs = require('fs');
const WebSocket = require('ws');

// 1. 准备SSL证书
const server = https.createServer({
  cert: fs.readFileSync('/path/to/cert.pem'),
  key: fs.readFileSync('/path/to/key.pem')
});

// 2. 创建WebSocket服务器
const wss = new WebSocket.Server({ server });

// 3. 处理WebSocket连接
wss.on('connection', (ws) => {
  console.log('新的客户端连接');
  
  ws.on('message', (message) => {
    console.log('收到消息:', message);
    // 广播给所有客户端
    wss.clients.forEach((client) => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(message);
      }
    });
  });
});

// 4. 启动服务器
server.listen(3000, () => {
  console.log('WSS服务运行在 wss://localhost');
});
```


### 2.使用Express + express-ws库

```javascript
const express = require('express');
const expressWs = require('express-ws');
const https = require('https');
const fs = require('fs');

// 1. 创建Express应用
const app = express();
expressWs(app); // 启用WebSocket支持

// 2. 准备SSL证书
const server = https.createServer({
  cert: fs.readFileSync('/path/to/cert.pem'),
  key: fs.readFileSync('/path/to/key.pem')
}, app);

// 3. 定义WebSocket端点
app.ws('/chat', (ws, req) => {
  console.log('新的聊天连接');
  
  ws.on('message', (msg) => {
    console.log('收到消息:', msg);
    // 简单的回声
    ws.send(`服务器收到: ${msg}`);
  });
});

// 4. 启动服务器
server.listen(3000, () => {
  console.log('Express WSS服务运行在 wss://localhost/chat');
});
```


### 3.本地nodeJS

```javascript
const express = require('express');
const http = require('http');
const WebSocket = require('ws');
const cors = require('cors');
const bodyParser = require('body-parser');

const app = express();
app.use(cors());
app.use(bodyParser.json());

// 常规API路由
app.get('/api/data', (req, res) => {
  res.json({ message: 'Hello from API', timestamp: new Date() });
});

app.post('/api/data', (req, res) => {
  console.log('Received data:', req.body);
  res.json({ success: true, receivedData: req.body });
});

// 创建HTTP服务器
const server = http.createServer(app);

// 设置WebSocket服务器
const wss = new WebSocket.Server({ server });
const clients = new Set();

wss.on('connection', (ws) => {
  console.log('webSocket连接成功');
  clients.add(ws);

  // 处理客户端消息
  ws.on('message', (message) => {
    try {
      const parsedMessage = JSON.parse(message);
      // 根据消息类型处理
      if (parsedMessage.type === 'send') {
        ws.send(JSON.stringify({ type: 'echo-reply', data: parsedMessage.data }));
      }
    } catch (e) {
      console.error('信息处理失败：', e);
    }
  });

  // 心跳检测变量
  let isAlive = true;
  const heartbeatInterval = 30000; // 30秒发送一次 Ping

  // 1. 服务端定时发送 Ping
  const interval = setInterval(() => {
    if (!isAlive) {
      console.log('客户端无响应，主动断开连接');
      ws.terminate(); // 强制关闭连接
      return;
    }

    isAlive = false; // 标记为待检测状态
    ws.ping(); // 发送 Ping 帧
  }, heartbeatInterval);

  // 2. 监听客户端的 Pong 响应
  ws.on('pong', () => {
    isAlive = true; // 收到 Pong，连接健康
    console.log('收到客户端心跳响应');
  });

  // 3. 连接关闭清理
  ws.on('close', () => {
    clearInterval(interval);
    clients.delete(ws);
    console.log('客户端断开连接');
  });
  
});

// 启动服务器
const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`服务器运行在端口${PORT}`);
});
```


## 4.心跳检测
- 定义：WebSocket 的心跳检测是一种保持连接活跃的机制，通过定期发送小型数据包（如 Ping/Pong 帧）来检测连接是否正常
- 核心作用
  - 维持连接活跃，防止超时断开
  - 服务端检测客户端连接是否存活
  - 节省资源，避免僵尸连接
  - 适应移动端网络环境
- 客户端pong：客户端接收到服务端的ping帧时会自动给服务端发送pong帧，客户端默认处理，ping事件不触发；可通过监听message判断是否保持与服务端的连接
- 服务端pong：服务端接收到客户端的pong帧时触发该事件，证明客户端仍存活；否则强制断连



## 5.获取SSL证书
- 自签名证书(开发环境)

```bash
# 生成私钥和自签名证书
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes`
```
- Let's Encrypt(生产环境推荐)

```bash
# 使用certbot获取免费证书
sudo apt-get install certbot
sudo certbot certonly --standalone -d yourdomain.com
```
- 云服务商提供的免费证书：如阿里云、腾讯云等都提供免费SSL证书


## 6.Nginx反向代理

```nginx
server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/fullchain.pem;
    ssl_certificate_key /path/to/privkey.pem;

    location /ws {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## 7.实时协作编辑实现
- 数据同步策略
  - 使用操作转换(OT)或差分同步算法
  - 为每个编辑操作分配唯一ID和时间戳 
- 消息协议设计
```javascript
{
  "type": "edit",
  "operation": "insert", // 或 "delete"
  "position": 42,
  "text": "new text",
  "version": 5,
  "clientId": "abc123",
  "timestamp": 1620000000000
}
```
- 冲突解决
  - 服务器作为单一事实来源
  - 实现版本控制系统
  - 处理离线编辑的合并
- 前端实现
  - 监听编辑器变化事件
  - 节流发送编辑操作(如200ms间隔)
  - 应用远程操作到本地编辑器
- 性能优化
  - 只发送差异而非全部内容
  - 使用WebWorker处理复杂计算