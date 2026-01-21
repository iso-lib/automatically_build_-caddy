# Caddy Custom Build Workflow

English | [简体中文](./README.md)

A GitHub Actions workflow for automatically building Caddy server with custom plugins.

## 📋 Features

- ✅ Support custom Caddy versions
- ✅ Manage plugin list via configuration file
- ✅ Multi-platform builds (Linux, macOS, Windows)
- ✅ Multi-architecture support (amd64, arm64)
- ✅ Automatic Release creation
- ✅ Scheduled builds
- ✅ Manual trigger support

## 🚀 Quick Start

### 1. Repository Setup

1. Fork or create a new repository
2. Save the workflow file to `.github/workflows/build-caddy.yml`
3. Create `plugins.txt` file to configure required plugins
4. **Important**: Enable write permissions for Actions
   - Go to repository `Settings` → `Actions` → `General`
   - Find `Workflow permissions`
   - Select `Read and write permissions`
   - Click `Save`

### 2. Configure Plugins

Edit `plugins.txt` file and add your desired plugins:

```plaintext
# DNS plugins
--with github.com/caddy-dns/cloudflare
--with github.com/caddy-dns/alidns

# Other plugins
--with github.com/mholt/caddy-ratelimit
--with github.com/mholt/caddy-l4
```

### 3. Trigger Build

**Option 1: Manual Trigger**
1. Go to the repository's Actions page
2. Select "Build Caddy with Custom Plugins"
3. Click "Run workflow"
4. Choose Caddy version (leave empty for latest)
5. Choose whether to enable multi-architecture build (will compile for Linux/macOS/Windows)

**Option 2: Scheduled Build**
- Automatically builds every Monday at 2 AM UTC (Linux amd64 only)

> **Note**: Modifying `plugins.txt` or pushing code will NOT trigger builds to avoid unnecessary compilations.

## 📦 Build Outputs

### Default Build
- Builds Linux amd64 version
- Generates Artifacts and tar.gz archive
- Creates Release with binaries

### Multi-Architecture Build (Manual Enable)
Supports the following platforms:
- Linux (amd64, arm64)
- macOS (amd64, arm64)
- Windows (amd64)

Check "Enable multi-architecture build" option to compile all platform versions and create a Release.

## 🔧 Configuration Guide

### Workflow Triggers

```yaml
on:
  workflow_dispatch:  # Manual trigger
    inputs:
      caddy_version:
        description: 'Caddy version'
        default: 'latest'
      enable_multiarch:
        description: 'Enable multi-architecture build'
        type: boolean
        default: false
  schedule:           # Scheduled trigger
    - cron: '0 2 * * 1'  # Every Monday at 2 AM UTC
```

**To modify build schedule**:
```yaml
schedule:
  - cron: '0 2 * * 1'    # Every Monday at 2 AM
  - cron: '0 0 1 * *'    # 1st day of each month
  - cron: '0 0 * * 0'    # Every Sunday
```

**To add push trigger**, add:
```yaml
  push:              # Push trigger
    branches:
      - main
    paths:
      - 'plugins.txt'
```

### Custom Configuration

**Modify Go version:**
```yaml
- name: Set up Go
  uses: actions/setup-go@v5
  with:
    go-version: '1.21'  # Change here
```

**Modify build platforms:**
```yaml
strategy:
  matrix:
    include:
      - goos: linux
        goarch: amd64
      # Add more platforms...
```

## 📝 Popular Plugin List

### DNS Providers
- `github.com/caddy-dns/cloudflare` - Cloudflare DNS
- `github.com/caddy-dns/alidns` - Alibaba Cloud DNS
- `github.com/caddy-dns/dnspod` - DNSPod
- `github.com/caddy-dns/route53` - AWS Route53

### Feature Plugins
- `github.com/mholt/caddy-ratelimit` - Rate limiting
- `github.com/mholt/caddy-l4` - Layer 4 proxy
- `github.com/mholt/caddy-dynamicdns` - Dynamic DNS
- `github.com/caddyserver/forwardproxy` - Forward proxy
- `github.com/greenpau/caddy-security` - Security & authentication
- `github.com/mholt/caddy-webdav` - WebDAV support

### Find More Plugins
Visit [Caddy's official download page](https://caddyserver.com/download) or search for `caddy-` related projects on GitHub.

## 📥 Using Compiled Caddy

### Download
Download the appropriate platform file from the Actions page or Releases page.

### Installation & Usage

**Linux/macOS:**
```bash
# Extract
tar -xzf caddy-*.tar.gz

# Grant execute permission
chmod +x caddy

# Move to system path (optional)
sudo mv caddy /usr/local/bin/

# Check version and plugins
caddy version
caddy list-modules
```

**Windows:**
```powershell
# Extract zip file and run directly
.\caddy.exe version
.\caddy.exe list-modules
```

## 🔍 Verify Plugins

Run the following commands to view installed plugins:

```bash
caddy list-modules | grep dns
caddy list-modules | grep security
```

## ⚙️ Advanced Usage

### Specify Plugin Version

Specify version in `plugins.txt`:
```plaintext
--with github.com/caddy-dns/cloudflare@v1.2.3
```

### Add Build Arguments

Modify build command in workflow file:
```bash
xcaddy build $VERSION \
  --with github.com/caddy-dns/cloudflare \
  --with github.com/mholt/caddy-ratelimit
```

## 🐛 Troubleshooting

**Q: Build fails with "GitHub release failed with status: 403"?**
A: This is a permissions issue. Solution:
1. Go to repository `Settings` → `Actions` → `General`
2. Scroll to `Workflow permissions` section
3. Select `Read and write permissions`
4. Check `Allow GitHub Actions to create and approve pull requests` (optional)
5. Click `Save`
6. Re-run the workflow

**Q: What if other build failures occur?**
A: Check the Actions logs, usually it's due to incorrect plugin paths or version incompatibility.

**Q: How to add private plugins?**
A: You need to configure Git authentication by adding the appropriate token in the workflow.

**Q: Build time is too long?**
A: You can use Go module cache or reduce the number of platforms to build.

## 🎯 Workflow Behavior

### Default Build (build job)
- ✅ Compiles Linux amd64 version
- ✅ Creates Release and uploads files
- ✅ Also uploads to Artifacts

### Multi-Architecture Build (build-multiarch job)
- ✅ Only runs when manually triggered with option enabled
- ✅ Uploads all platform files to the same Release
- ✅ Also uploads to Artifacts

## 📄 License

This workflow template follows the MIT License. Caddy itself follows the Apache 2.0 License.

## 🤝 Contributing

Issues and Pull Requests are welcome!

## 🔗 Related Links

- [Caddy Official Website](https://caddyserver.com/)
- [xcaddy Documentation](https://github.com/caddyserver/xcaddy)
- [Caddy Plugin Marketplace](https://caddyserver.com/download)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

## 📊 Build Status

The workflow will:
- Run automatically every Monday at 2 AM UTC
- Can be triggered manually anytime
- Generate artifacts valid for 30 days
- Create permanent releases when enabled

## 💡 Tips

1. **Cache Warnings**: First-time builds will show "Restore cache failed" warnings - this is normal and doesn't affect the build
2. **Quick Builds**: For testing, use default build without multi-arch to save time
3. **Release Management**: Each build creates a unique release tag based on date and version
4. **Plugin Updates**: The scheduled build automatically uses the latest Caddy version and your current plugin list

## 🌟 Star History

If you find this workflow useful, please consider giving it a star!

---

**Maintained by**: Community  
**Last Updated**: 2026-01-21
