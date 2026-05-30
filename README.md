# MuseumAgent Demo - Web 客户端示例

基于 **MuseumAgent Client SDK V2.0** 开发的 Web 客户端示例应用。

## ✨ 特性

- 🚀 **会话自动恢复**：刷新页面后自动重连
- ⚙️ **配置持久化**：配置自动保存和加载
- 🎤 **语音交互**：支持录音、播放、VAD 语音检测
- 💬 **实时对话**：流式文本和语音响应
- 🔧 **函数调用**：支持智能体函数调用
- 🎨 **现代 UI**：简洁美观的用户界面

## 📦 项目结构

```
Demo/
├── lib/                          # SDK 库（服务端项目负责构建SDK）
│   ├── museum-agent-sdk.min.js      # SDK 压缩版 (34KB)
│   ├── museum-agent-sdk.min.js.map  # Source Map
│   └── vad-processor.js             # AudioWorklet 处理器 (5KB) ⚠️
│
├── src/                          # 应用代码
│   ├── components/              # UI 组件
│   │   ├── LoginForm.js        # 登录表单
│   │   ├── ChatWindow.js       # 聊天窗口
│   │   ├── MessageBubble.js    # 消息气泡
│   │   └── SettingsPanel.js    # 设置面板
│   ├── utils/                   # 应用工具
│   │   ├── dom.js              # DOM 工具
│   │   └── audioPlayer.js      # 音频播放器
│   ├── app.js                   # 应用入口
│   └── styles.css               # 样式文件
│
├── res/                          # 资源文件
├── index.html                    # 入口页面
├── ssl_server.py                # 开发服务器
└── start.bat                    # 启动脚本
```

**⚠️ 重要**：`vad-processor.js` 是 AudioWorklet 处理器，录音功能必需。

## 🚀 快速开始

### 1. 启动开发服务器

```bash
# Windows
start.bat

# 或手动启动
python ssl_server.py
```

### 2. 访问应用

打开浏览器访问：`https://localhost:8443`

### 3. 登录

- **服务器地址**：`wss://museum.soulflaw.com:12301`
- **用户名**：`123`
- **密码**：`123`

## 📖 使用说明

### 会话管理

- **首次登录**：输入服务器地址和认证信息
- **自动恢复**：刷新页面后自动重连（会话有效期 24 小时）
- **手动登出**：点击"登出"按钮清除会话

### 配置管理

- 点击右上角 ⚙️ 图标打开设置面板
- 修改配置后自动保存
- 配置包括：
  - 基本配置（TTS、SRS、自动播放等）
  - 智能体角色配置
  - 上下文配置
  - 函数定义
  - VAD 参数

### 语音交互

- 点击 🎤 按钮开始录音
- 支持 VAD 自动检测语音开始和结束
- 点击 ⏹️ 按钮停止录音
- 收到语音回复后自动播放

### 文本交互

- 在输入框输入文字
- 按 Enter 发送（Shift+Enter 换行）
- 实时接收流式文本响应

## 🔧 开发指南

### 使用 SDK

Demo 使用 SDK 的构建产物（UMD 格式）：

```html
<!-- index.html -->
<script src="./lib/museum-agent-sdk.min.js"></script>
<script type="module" src="./src/app.js"></script>
```

```javascript
// src/app.js
const { MuseumAgentClient, Events } = window.MuseumAgentSDK;

// 创建客户端
const client = new MuseumAgentClient({
  serverUrl: 'wss://your-server.com'
});

// 尝试恢复会话
const restored = await client.reconnectFromSavedSession();

if (!restored) {
  // 首次连接
  await client.connect(authData);
  await client.saveSession();
}
```

### 更新 SDK

当 SDK 有更新时：

```bash
# 方式 1：从 SDK 目录自动更新（推荐）
cd ../../sdk
update-demo.bat

# 方式 2：手动复制
cd ../../sdk
npm run build
cp dist/museum-agent-sdk.min.js ../web/Demo/lib/
cp dist/museum-agent-sdk.min.js.map ../web/Demo/lib/
cp src/managers/vad-processor.js ../web/Demo/lib/

# 方式 3：从 CDN 引用（修改 index.html）
<script src="https://unpkg.com/museum-agent-client-sdk@2.0.0/dist/museum-agent-sdk.min.js"></script>
```

**注意**：更新时必须同时复制 `vad-processor.js`，否则录音功能将无法工作。

## 📚 SDK 文档

详细的 SDK 文档请查看：
- **SDK 仓库**：`../../sdk/`
- **API 文档**：`../../sdk/README.md`
- **构建指南**：`../../sdk/BUILD.md`
- **更新日志**：`../../sdk/CHANGELOG.md`

## 🎯 核心功能

### 1. 会话自动恢复 ✅

刷新页面后自动重连，无需重新登录。

```javascript
// 应用启动时自动恢复
const client = new MuseumAgentClient({ ... });
const restored = await client.reconnectFromSavedSession();

if (restored) {
  // 会话恢复成功
  this.showChatView();
} else {
  // 首次登录
  this.showLoginView();
}
```

### 2. 配置持久化 ✅

所有配置自动保存到本地，下次启动自动加载。

```javascript
// 更新配置（自动保存）
client.updateConfig('requireTTS', false);

// 批量更新
client.updateConfigs({
  requireTTS: false,
  enableSRS: true
});
```

### 3. 安全存储 ✅

会话数据和敏感信息加密存储。

### 4. 实时通信 ✅

WebSocket 全双工通信，支持流式响应。

### 5. 音频处理 ✅

内置录音、播放、VAD 语音检测（基于 AudioWorklet）。

**技术特点：**
- ✅ 使用 AudioWorkletNode（现代 Web Audio API）
- ✅ 音频处理在独立线程运行，不阻塞主线程
- ✅ 更低的延迟和更好的性能
- ✅ 符合 Web 标准，未来兼容性更好

## 🔒 安全性

- ✅ 会话数据加密存储
- ✅ 敏感信息加密传输
- ✅ XSS 防护
- ✅ 输入验证

## 📊 性能

- **SDK 体积**：34KB（压缩后）+ 5KB（AudioWorklet 处理器）
- **加载时间**：<100ms
- **内存占用**：<10MB
- **音频处理**：独立线程，不阻塞 UI
- **支持浏览器**：
  - Chrome 66+ / Edge 79+（完全支持）
  - Firefox 76+（完全支持）
  - Safari 14.1+（完全支持）
  - Opera 53+（完全支持）
  - 全球覆盖率：~94%

## 🌐 浏览器兼容性

### ✅ 完全支持

| 浏览器 | 最低版本 | 推荐版本 | 说明 |
|--------|---------|---------|------|
| Chrome | 66+ | 90+ | 完全支持，性能最佳 |
| Edge | 79+ | 90+ | 基于 Chromium，与 Chrome 一致 |
| Firefox | 76+ | 100+ | 完全支持 |
| Safari (macOS) | 14.1+ | 16.0+ | 完全支持 |
| Safari (iOS) | 14.5+ | 16.0+ | 完全支持，需 HTTPS |
| Opera | 53+ | 最新 | 基于 Chromium |

### ⚠️ 部分支持

- **Safari 14.0 及以下**：不支持 AudioWorklet，录音功能无法使用
- **旧版 Android 浏览器**：可能不支持

### ❌ 不支持

- **Internet Explorer**：完全不支持（已停止维护）
- **Chrome < 66**：缺少 AudioWorklet 支持
- **Firefox < 76**：缺少 AudioWorklet 支持

### 📱 移动端

- **Android Chrome 66+**：✅ 完全支持
- **iOS Safari 14.5+**：✅ 完全支持（需 HTTPS）
- **微信内置浏览器**：⚠️ 可能有限制，建议在外部浏览器打开