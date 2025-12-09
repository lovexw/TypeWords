# Cloudflare 部署指南

本指南将帮助您将 TypeWords 项目部署到 Cloudflare 平台。

## 部署选项

### 1. Cloudflare Pages (推荐)
最适合本项目的部署方式，因为 TypeWords 是一个静态单页应用（SPA）。

### 2. Cloudflare Workers
适合需要服务器端逻辑的场景，但本项目主要为静态内容。

### 3. Cloudflare Static Storage
简单的静态文件存储，适合快速部署。

---

## Cloudflare Pages 部署 (推荐)

### 前置条件
- Cloudflare 账户
- GitHub 账户（推荐）
- 项目代码已推送到 GitHub

### 部署步骤

#### 1. 准备项目
确保您的项目根目录有以下关键文件：
- `package.json` ✅
- `vite.config.mts` ✅
- `index.html` ✅
- `src/` 目录 ✅

#### 2. 登录 Cloudflare
1. 访问 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 登录您的账户

#### 3. 创建 Pages 项目
1. 在 Dashboard 中，点击 **"Pages"** 
2. 点击 **"创建应用程序"**
3. 选择 **"连接到 Git"**

#### 4. 连接到 GitHub
1. 点击 **"GitHub"** 
2. 授权 Cloudflare 访问您的 GitHub 账户（如果尚未授权）
3. 选择包含 TypeWords 项目的仓库
4. 点击 **"开始设置"**

#### 5. 配置构建设置
在构建设置页面中配置以下信息：

```
框架预设: Vite
构建命令: npm run build
构建输出目录: dist
Node.js 版本: 18 或更高
```

**具体配置：**
- **项目名称**: `typewords`（或您喜欢的名称）
- **生产分支**: `main`（或您的默认分支）
- **构建设置**:
  - 构建命令: `npm run build`
  - 构建输出目录: `dist`
  - Root 目录: `/` (留空)
- **环境变量**: 
  - `NODE_VERSION`: `18`

#### 6. 高级设置（可选）

如果您需要自定义构建配置，可以创建 `wrangler.toml` 文件：

```toml
name = "typewords"
compatibility_date = "2023-06-01"

# 如果需要特定 Node.js 版本
[env.production.vars]
NODE_VERSION = "18"
```

#### 7. 部署
1. 点击 **"保存并部署"**
2. Cloudflare 将自动：
   - 克隆您的仓库
   - 安装依赖 (`npm install`)
   - 构建项目 (`npm run build`)
   - 部署到全球 CDN

#### 8. 部署完成
- 部署成功后，您将获得一个 `https://your-project.pages.dev` 域名
- 在 **"自定义域"** 选项中可以添加您自己的域名
- 每次推送到指定分支时，Cloudflare 会自动重新部署

---

## 本地测试部署

### 1. 构建项目
```bash
# 安装依赖
npm install

# 构建项目
npm run build
```

### 2. 使用 Cloudflare Pages 本地开发
```bash
# 安装 wrangler CLI
npm install -g wrangler

# 登录 Cloudflare
wrangler login

# 本地开发（如果配置了 worker 功能）
wrangler pages dev dist
```

---

## 环境变量配置

如果您需要配置环境变量，在 Cloudflare Pages 项目设置中：

1. 进入项目 Dashboard
2. 点击 **"设置"** 标签
3. 找到 **"环境变量"** 部分
4. 添加所需的环境变量

**TypeWords 项目目前不需要特殊的环境变量**，因为：
- 所有配置都在前端代码中
- 数据存储在本地浏览器中
- 无需服务器端 API

---

## 自定义域名设置

### 1. 在 Cloudflare Pages 中
1. 进入您的项目 Dashboard
2. 点击 **"自定义域"**
3. 点击 **"设置自定义域"**
4. 输入您的域名（如：`typewords.yourdomain.com`）

### 2. 在您的域名提供商处
设置 DNS 记录：
```
类型: CNAME
名称: typewords (或您选择的子域名)
目标: your-project.pages.dev
```

### 3. 确保域名在 Cloudflare 中
- 如果您的域名托管在 Cloudflare，Cloudflare 会自动检测
- 如果在其他域名提供商，可能需要手动配置

---

## 性能优化

Cloudflare Pages 默认提供：

### 1. 全球 CDN
- 内容自动缓存到全球边缘节点
- 自动压缩（Gzip/Brotli）
- HTTP/2 支持

### 2. 静态文件优化
Cloudflare Pages 自动优化静态资源：
- 图片自动压缩
- CSS/JS 文件压缩
- 缓存头自动设置

### 3. 安全功能
- HTTPS 自动配置
- DDoS 防护
- Web 应用防火墙

---

## 监控和分析

### 1. Cloudflare Analytics
在项目 Dashboard 中查看：
- 页面浏览量
- 访问者数据
- 性能指标
- 带宽使用情况

### 2. 实时日志
- 访问日志
- 错误日志
- 性能分析

---

## 故障排除

### 1. 构建失败
```bash
# 本地测试构建
npm run build

# 检查构建输出
ls -la dist/
```

### 2. 常见问题

**问题**: 页面显示空白
- **解决**: 检查 `vite.config.mts` 中的 `base` 配置，确保使用相对路径 `'./'`

**问题**: 静态资源 404
- **解决**: 确保构建输出目录配置正确

**问题**: 路由不工作
- **解决**: Cloudflare Pages 自动处理 SPA 路由，如果有问题，检查 `_redirects` 文件：

```bash
# 在 dist 目录创建 _redirects 文件
echo "/*    /index.html   200" > dist/_redirects
```

### 3. 部署历史
- 在 **"部署"** 标签中查看部署历史
- 可以回滚到之前的部署版本

---

## 自动化部署

### GitHub 集成
每次推送到指定分支时自动部署：
- `main` 分支 → 生产环境
- 其他分支 → 预览环境

### Pull Request 预览
- 每个 PR 自动创建预览链接
- 方便测试和评审

---

## 备份和恢复

### 1. 源代码备份
- GitHub 仓库作为源代码备份
- 支持标签和发布版本

### 2. 快速恢复
- 任何部署都可以快速回滚
- Cloudflare 保留部署历史

---

## 成本

### Cloudflare Pages 定价
- **免费层**: 
  - 500 次构建/月
  - 100GB 带宽/月
  - 无限制网站
  - 全球 CDN

- **Pro 层**: $20/月
  - 无限构建
  - 无限带宽
  - 高级分析

对于 TypeWords 项目，**免费层完全足够**。

---

## 其他部署选项

### Cloudflare Workers (如果需要服务器端逻辑)
```bash
# 初始化 workers 项目
npm create cloudflare@latest typewords-worker
```

### Cloudflare Static Storage
适用于简单的静态文件托管，但不支持自动构建。

---

## 总结

**推荐部署流程：**
1. ✅ 使用 Cloudflare Pages
2. ✅ 连接到 GitHub 仓库
3. ✅ 配置构建设置（Vite 预设）
4. ✅ 设置自定义域名
5. ✅ 启用自动部署

这样部署后，您的 TypeWords 应用将拥有：
- 🚀 全球快速访问
- 🔒 HTTPS 安全连接
- 📊 详细访问分析
- 🔄 自动部署更新
- 💰 免费托管服务

部署后，您的应用将可以通过 `https://your-project.pages.dev` 或您的自定义域名访问。