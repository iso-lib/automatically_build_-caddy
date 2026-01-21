# Caddy 自定义编译工作流

这个 GitHub Actions 工作流可以自动编译带有自定义插件的 Caddy 服务器。

## 📋 功能特性

- ✅ 支持自定义 Caddy 版本
- ✅ 通过配置文件管理插件列表
- ✅ 支持多平台编译（Linux、macOS、Windows）
- ✅ 支持多架构（amd64、arm64）
- ✅ 自动创建 Release 发布
- ✅ 定时自动构建
- ✅ 手动触发构建

## 🚀 快速开始

### 1. 设置仓库

1. Fork 或创建新仓库
2. 将工作流文件保存到 `.github/workflows/build-caddy.yml`
3. 创建 `plugins.txt` 文件配置需要的插件

### 2. 配置插件

编辑 `plugins.txt` 文件，添加你需要的插件：

```plaintext
# DNS 插件
--with github.com/caddy-dns/cloudflare
--with github.com/caddy-dns/alidns

# 其他插件
--with github.com/mholt/caddy-ratelimit
--with github.com/mholt/caddy-l4
```

### 3. 触发构建

**方式一：手动触发**
1. 进入仓库的 Actions 页面
2. 选择 "Build Caddy with Custom Plugins"
3. 点击 "Run workflow"
4. 选择 Caddy 版本（留空使用最新版）
5. 选择是否启用多平台编译（勾选后会编译 Linux/macOS/Windows 多个平台）

**方式二：定时自动触发**
- 每周一凌晨 2 点自动构建最新版本（仅 Linux amd64）

> **注意**：修改 `plugins.txt` 或推送代码不会触发构建，避免不必要的编译。

## 📦 构建产物

### 默认构建
- 构建 Linux amd64 版本
- 生成 Artifact 和 tar.gz 压缩包

### 多平台构建（手动勾选启用）
支持以下平台：
- Linux (amd64, arm64)
- macOS (amd64, arm64)
- Windows (amd64)

勾选 "Enable multi-architecture build" 选项后会编译所有平台版本并创建 Release。

## 🔧 配置说明

### 工作流触发条件

```yaml
on:
  workflow_dispatch:  # 手动触发
    inputs:
      caddy_version:
        description: 'Caddy version'
        default: 'latest'
      enable_multiarch:
        description: 'Enable multi-architecture build'
        type: boolean
        default: false
  schedule:           # 定时触发
    - cron: '0 2 * * 1'  # 每周一凌晨2点（UTC时间）
```

**修改定时构建时间**：
```yaml
schedule:
  - cron: '0 2 * * 1'    # 每周一凌晨2点
  - cron: '0 0 1 * *'    # 每月1号凌晨
  - cron: '0 0 * * 0'    # 每周日凌晨
```

**如需添加推送触发**，可以添加：
```yaml
  push:              # 代码推送触发
    branches:
      - main
    paths:
      - 'plugins.txt'
```

### 自定义配置

**修改 Go 版本：**
```yaml
- name: Set up Go
  uses: actions/setup-go@v5
  with:
    go-version: '1.21'  # 修改这里
```

**修改构建平台：**
```yaml
strategy:
  matrix:
    include:
      - goos: linux
        goarch: amd64
      # 添加更多平台...
```

## 📝 常用插件列表

### DNS 提供商
- `github.com/caddy-dns/cloudflare` - Cloudflare DNS
- `github.com/caddy-dns/alidns` - 阿里云 DNS
- `github.com/caddy-dns/dnspod` - DNSPod
- `github.com/caddy-dns/route53` - AWS Route53

### 功能插件
- `github.com/mholt/caddy-ratelimit` - 速率限制
- `github.com/mholt/caddy-l4` - Layer 4 代理
- `github.com/mholt/caddy-dynamicdns` - 动态 DNS
- `github.com/caddyserver/forwardproxy` - 正向代理
- `github.com/greenpau/caddy-security` - 安全认证
- `github.com/mholt/caddy-webdav` - WebDAV 支持

### 查找更多插件
访问 [Caddy 官方插件列表](https://caddyserver.com/download) 或搜索 GitHub 上的 `caddy-` 相关项目。

## 📥 使用编译好的 Caddy

### 下载
从 Actions 页面或 Releases 页面下载对应平台的文件。

### 安装使用

**Linux/macOS:**
```bash
# 解压
tar -xzf caddy-*.tar.gz

# 赋予执行权限
chmod +x caddy

# 移动到系统路径（可选）
sudo mv caddy /usr/local/bin/

# 查看版本和插件
caddy version
caddy list-modules
```

**Windows:**
```powershell
# 解压 zip 文件后直接运行
.\caddy.exe version
.\caddy.exe list-modules
```

## 🔍 验证插件

运行以下命令查看已安装的插件：

```bash
caddy list-modules | grep dns
caddy list-modules | grep security
```

## ⚙️ 高级用法

### 指定插件版本

在 `plugins.txt` 中指定版本：
```plaintext
--with github.com/caddy-dns/cloudflare@v1.2.3
```

### 添加构建参数

修改工作流文件中的构建命令：
```bash
xcaddy build $VERSION \
  --with github.com/caddy-dns/cloudflare \
  --with github.com/mholt/caddy-ratelimit
```

## 🐛 常见问题

**Q: 构建失败怎么办？**
A: 检查 Actions 日志，通常是插件路径错误或版本不兼容。

**Q: 如何添加私有插件？**
A: 需要配置 Git 认证，在工作流中添加相应的 token。

**Q: 编译时间过长？**
A: 可以使用 Go 模块缓存，或减少构建的平台数量。

## 📄 许可证

本工作流模板遵循 MIT 许可证。Caddy 本身遵循 Apache 2.0 许可证。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 🔗 相关链接

- [Caddy 官网](https://caddyserver.com/)
- [xcaddy 文档](https://github.com/caddyserver/xcaddy)
- [Caddy 插件市场](https://caddyserver.com/download)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
