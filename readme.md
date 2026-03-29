# ⚡ CF-Server-Monitor-Pro (Serverless 探针增强版)

基于 Cloudflare Workers 和 D1 数据库构建的轻量级、零成本、高定制化的服务器探针大盘。
完美复刻了商业级探针的核心体验，但无需额外部署任何服务端 VPS！完全白嫖 Cloudflare 的免费 Serverless 资源。

## ✨ 核心特性

- **🆓 零成本服务端**：利用 Cloudflare Workers + D1 数据库，无需购买额外的服务器来运行探针面板。
- **📊 极简前台大盘**：直观的 Grid 布局卡片，全局统计总流量、实时网速、在线/离线节点数。
- **📈 实时详情图表**：点击单台服务器卡片，即可查看基于 Chart.js 的 CPU、内存、磁盘、进程数、TCP/UDP 连接数及网速的实时跳动折线图。
- **🌍 智能地理位置**：依托 Cloudflare 强大的节点网络，自动识别被控端 VPS 的真实地理位置，并显示高清国旗（内置特殊地区的合规展示）。
- **🏷️ 商业级自定义**：支持按地区或用途**分组**；支持自定义展示 VPS 的**价格、到期时间、带宽上限、流量配额**。
- **🛡️ 隐私保护模式**：支持一键切换“公开模式”与“私密模式”。私密模式下，需输入后台密码方可查看大盘。
- **🚀 极简一键安装**：后台自动生成被控端 Bash 一键安装命令。支持 IPv4/IPv6 双栈检测、底层 CPU 时钟精准计算、自动注册 systemd 守护进程守护。

---

## 🛠️ 部署指南

### 第一步：创建 Cloudflare D1 数据库
1. 登录 Cloudflare 控制台，进入 **Workers & Pages** -> **D1 SQL Database**。
2. 创建一个名为 `probe-db` 的数据库。
3. 进入该数据库的 **Console (控制台)**，执行以下 SQL 语句来初始化表结构：

```sql
-- 创建服务器节点表
CREATE TABLE servers (
    id TEXT PRIMARY KEY,
    name TEXT,
    cpu TEXT, ram TEXT, disk TEXT, load_avg TEXT, uptime TEXT, last_updated INTEGER,
    ram_total TEXT, net_rx TEXT, net_tx TEXT, net_in_speed TEXT, net_out_speed TEXT,
    os TEXT, cpu_info TEXT, arch TEXT, boot_time TEXT, ram_used TEXT, swap_total TEXT, 
    swap_used TEXT, disk_total TEXT, disk_used TEXT, processes TEXT, tcp_conn TEXT, udp_conn TEXT, 
    country TEXT, ip_v4 TEXT, ip_v6 TEXT,
    server_group TEXT DEFAULT '默认分组', price TEXT DEFAULT '', expire_date TEXT DEFAULT '', 
    bandwidth TEXT DEFAULT '', traffic_limit TEXT DEFAULT ''
);

-- 创建全局设置表
CREATE TABLE IF NOT EXISTS settings (
    key TEXT PRIMARY KEY,
    value TEXT
);
