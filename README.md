# panabit-app-lucky
lucky for panabit

# Panabit Lucky APX 安装包

<p align="center">
  <a href="https://opensource.org/licenses/MIT">
    <img src="https://img.shields.io/badge/License-MIT-yellow.svg" height="40">
  </a>
  <a href="https://github.com/gdy666/lucky">
    <img src="https://lucky666.cn/img/logo.svg" height="40">
  </a>
  <a href="https://www.panabit.com/">
    <img src="https://prod0b43e6f-pic10.ysjianzhan.cn/upload/panabit_1200_9bxx.png" height="40">
  </a>
</p>

在 Panabit 智能应用网关上运行 [Lucky](https://github.com/gdy666/lucky) 的 APX 应用封装包。

## 📋 目录

- [功能特性](#功能特性)
- [快速开始](#快速开始)
- [使用说明](#使用说明)
- [配置管理](#配置管理)
- [升级说明](#升级说明)
- [故障排除](#故障排除)
- [技术细节](#技术细节)
- [致谢](#致谢)
- [许可证](#许可证)

## ✨ 功能特性

### 核心功能

- ✅ **一键安装** - 通过 Panabit Web 界面上传安装，无需 SSH
- ✅ **一键启动** - 安装完成后一键启动 Lucky 服务
- ✅ **架构自适应** - 自动检测系统架构（Linux ARM64/Linux x86_64）
- ✅ **Web 引导页面** - 点击应用显示访问引导和使用说明
- ✅ **配置保护** - 升级时自动保留用户配置，不会丢失设置
- ✅ **进程管理** - 支持启动、停止、启用、停用等完整生命周期管理

### Web 引导页面

- 📍 自动显示 Lucky Web 访问地址
- 🚀 一键打开 Lucky 管理界面
- 📋 一键复制访问地址
- 🔑 显示默认登录凭据
- 💡 提供 WAN 口地址获取参考命令
- ⚠️ MGT 接口访问提醒

## 🚀 快速开始

### 系统要求

- Panabit 智能应用网关系统
- 支持架构：Linux ARM64 或 Linux x86_64
- 需要开启 MGT 接口以访问 Lucky Web 服务

### 安装步骤

1. **下载安装包**
   - 下载 `PanabitApp_lucky_2.19.5.apx` 文件

2. **上传安装**
   - 登录 Panabit Web 管理界面
   - 进入 **系统概况** → **应用商店**
   - 点击右上角 **安装更新**
   - 选择 APX 文件上传并安装

3. **自动启动**
   - 安装完成后 Lucky 服务自动启动
   - 在应用列表中点击 Lucky 应用查看引导页面

### 访问 Lucky

```
地址: http://[Panabit设备IP]:16601
默认用户名: 666
默认密码: 666
```

**重要**: 请确保 Panabit 的 MGT 接口已开启，并允许访问 16601 端口。

## 📖 使用说明

### 通过 Panabit 界面管理

1. **启用应用** - 自动启动 Lucky 服务
2. **停用应用** - 停止 Lucky 服务
3. **查看状态** - 显示应用启用/停用状态
4. **点击应用** - 打开 Web 引导页面

### WAN 口地址获取

在 Lucky 的 DDNS 功能中，如需通过命令获取 WAN 口 IP 地址，可以使用：

```bash
floweye nat listproxy | grep WAN | awk '{print $5}'
```

该命令参考自 [panabit-community/ddns-go-manager](https://github.com/panabit-community/ddns-go-manager) 项目。

## ⚙️ 配置管理

### 配置文件位置

```
/etc/lucky/lucky.conf          # Lucky 主配置文件
/etc/App/lucky/lucky           # 应用状态配置
```

### 默认配置

```json
{
  "Admin": {
    "Username": "666",
    "Password": "666",
    "Port": 16601,
    "WebPath": "/",
    "LogLevel": "info"
  },
  "ConfigVersion": "2.19.5"
}
```

### 修改配置

```bash
# 编辑配置文件
vi /etc/lucky/lucky.conf

# 修改后重启服务
/usr/panabit/app/lucky/appctrl restart
```

## 🔄 升级说明

### 升级时会保留的内容

- ✅ `/etc/lucky/lucky.conf` - Lucky 主配置（包括用户修改的密码、端口等）
- ✅ `/etc/App/lucky/lucky` - 应用状态配置
- ✅ 所有 Lucky 内部的配置和规则

### 升级时会更新的内容

- ✅ Lucky 二进制程序
- ✅ 控制脚本（appctrl、luckyctl）
- ✅ Web 引导页面

### 升级步骤

1. 下载新版本的 APX 文件
2. 通过 Panabit 应用管理界面上传并安装
3. 系统会自动保留现有配置
4. 安装完成后服务自动重启

### 实现原理

```bash
# afterinstall 脚本中的配置保护
if [ ! -f /etc/lucky/lucky.conf ]; then
    # 仅在配置文件不存在时创建默认配置
    # 如果文件已存在（升级场景），则跳过，保留用户配置
fi
```

## 🛠️ 故障排除

### 服务未启动

```bash
# 检查进程
ps aux | grep lucky

# 检查配置文件
cat /etc/lucky/lucky.conf

# 查看二进制文件
ls -la /usr/panabit/app/lucky/bin/lucky

# 手动启动测试
/usr/panabit/app/lucky/bin/lucky -c /etc/lucky/lucky.conf
```

### 无法访问 Web 界面

```bash
# 检查端口监听
netstat -tlnp | grep 16601

# 检查防火墙
iptables -L | grep 16601

# 确认 MGT 接口已开启
```

### 升级后配置丢失

正常情况下不会发生。如果遇到此问题：

```bash
# 检查配置文件是否存在
cat /etc/lucky/lucky.conf

# 如果不存在，重新创建
vi /etc/lucky/lucky.conf
```

## 🔧 技术细节

### 文件结构

```
/usr/panabit/app/lucky/
├── bin/
│   ├── lucky              # Lucky 主程序
│   └── luckyctl           # 服务控制脚本
├── web/
│   ├── html/
│   │   └── main.html      # Web 引导页面
│   └── cgi/
│       └── webmain        # CGI 入口脚本
└── appctrl                # 应用控制脚本（Panabit 接口）

/etc/lucky/
└── lucky.conf             # Lucky 配置文件（升级保留）

/etc/App/lucky/
└── lucky                  # 应用状态文件
```

### 支持的系统架构

| 架构 | 说明 | 检测方式 |
|------|------|---------|
| ARM64 | `aarch64` | `uname -m` |
| Linux x86_64 | `x86_64` | 默认 |

### 脚本说明

| 脚本 | 功能 | 执行时机 |
|------|------|---------|
| `preinstall` | 清理旧版本文件和进程 | 安装前 |
| `afterinstall` | 安装二进制、创建配置 | 安装后 |
| `appctrl` | Panabit 应用控制接口 | 应用管理操作时 |
| `luckyctl` | Lucky 服务控制脚本 | 由 appctrl 调用 |

### 编码说明

为确保在 Panabit 系统中正确显示中文：

- `app.inf` - **GBK 编码**
- `main.html` - **GBK 编码**
- Shell 脚本 - **ASCII/UTF-8 编码**

## 🔐 安全建议

1. **修改默认密码**
   - 首次登录后立即修改默认用户名和密码
   - 使用强密码策略

2. **网络安全**
   - 仅在必要时开启外网访问
   - 配置防火墙规则限制访问来源
   - 定期检查访问日志

3. **定期维护**
   - 检查服务运行状态
   - 备份重要配置
   - 及时更新到最新版本

## 🙏 致谢

本项目基于以下优秀项目构建：

- **[Lucky](https://github.com/gdy666/lucky)** - 软硬路由公网神器，感谢原作者 @gdy666
- **[panabit-community/ddns-go-manager](https://github.com/panabit-community/ddns-go-manager)** - 提供了 Panabit 应用封装的参考实现和 WAN 口地址获取方法

特别感谢以上项目作者的贡献！

## 📄 许可证

本 APX 封装使用 MIT 许可证。

Lucky 项目遵循其原始许可证。

Panabit 是北京派网软件有限公司的品牌/商标，本项目中的引用仅为辅助说明，代码等内容均与官方无关。

## 📝 更新日志

### v2.19.5 (2025-10-26)

- ✅ 初始版本发布
- ✅ 支持 ARM64 和 Linux x86_64 架构
- ✅ 完整的 Web 引导页面
- ✅ 升级时配置保护机制
- ✅ GBK 编码，完美支持中文显示
- ✅ 参考 nginx APX 实现，遵循 Panabit 应用规范

## 📮 反馈与支持

如遇到问题或有建议，欢迎：

1. 提交 [Issue](https://github.com/your-repo/issues)
2. 查看 Lucky 官方文档
3. 检查本文档的故障排除章节

## 🔗 相关链接

- **Lucky 官方项目**: https://github.com/gdy666/lucky
- **Panabit 官网**: https://www.panabit.com/
- **参考项目**: https://github.com/panabit-community/ddns-go-manager

---

**注意**: 本项目为非官方封装，仅供学习和个人使用。使用时请遵守相关法律法规和服务条款。
