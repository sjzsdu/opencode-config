---
name: "fscan"
description: "内网综合扫描工具，支持主机存活探测、端口扫描、密码爆破、漏洞检测等。Invoke when user needs to scan intranet, perform penetration testing, or use fscan command."
---

# fscan - 内网综合扫描工具

fscan 是一款内网综合扫描工具，支持一键自动化、全方位漏扫扫描。

## 安装

### 编译安装

```bash
git clone https://github.com/shadow1ng/fscan.git
cd fscan
go build -ldflags="-s -w" -trimpath main.go
```

### Arch Linux

```bash
yay -S fscan-git
# 或
paru -S fscan-fscan
```

## 基本用法

```bash
fscan -h 192.168.1.1/24    # 扫描整个C段
fscan -h 192.168.1.1       # 扫描单个主机
fscan -h 10.0.0.0/8        # 扫描整个A段
```

## 常用参数

| 参数 | 说明 |
|------|------|
| `-h` | 目标主机 (IP/IP段) |
| `-pf` | 端口列表文件 |
| `-p` | 指定端口扫描 (如 -p80,445 或 -p1-1000) |
| `-m` | 扫描模式 (icmp/netbios/port) |
| `-c` | 执行命令 (用于SSH/Redis利用) |
| `-rf` | Redis写公钥文件 |
| `-rs` | RedisShell |
| `-o` | 输出文件 |
| `-wg` | 密码字典文件 |
| `-np` | 不进行存活探测 |
| `-proxy` | HTTP代理 |

## 常用示例

### 1. 基础扫描（全功能）

```bash
fscan -h 192.168.x.x
```
扫描目标并自动检测：MS17-010漏洞、读取网卡信息

### 2. Redis 写公钥

```bash
fscan -h 192.168.x.x -rf id_rsa.pub
```
利用Redis写入公钥实现远程访问

### 3. SSH 命令执行

```bash
fscan -h 192.168.x.x -c "whoami;id"
```
通过SSH执行指定命令

### 4. Web漏洞扫描（配合Xray）

```bash
fscan -h 192.168.x.x -p80 -proxy http://127.0.0.1:8080
```
扫描80端口并使用代理配合Xray的POC进行漏洞检测

### 5. NetBIOS探测

```bash
fscan -h 192.168.x.x -p 139
```
检测NetBIOS信息，识别域控（[+]DC代表域控）

### 6. 完整NetBIOS信息

```bash
fscan -h 192.168.x.x/24 -m netbios
```

### 7. ICMP探测

```bash
fscan -h 192.0.0.0/8 -m icmp
```
探测每个C段的网关和随机IP，统计存活数量

### 8. 指定端口扫描

```bash
fscan -h 192.168.x.x -p 22,3306,3389,5432,6379,8080
```
扫描常见高危端口

### 9. 自定义密码字典

```bash
fscan -h 192.168.x.x -wg password.txt
```
使用自定义字典进行爆破

### 10. 端口列表文件

```bash
fscan -h 192.168.x.x -pf portlist.txt
```
使用自定义端口列表

## 爆破服务支持

- **SSH**
- **SMB**
- **RDP**
- **MySQL**
- **MSSQL**
- **Redis**
- **PostgreSQL**
- **Oracle**
- **FTP**
- **Telnet**

## 漏洞检测

- **MS17-010** (永恒之蓝)
- **WebLogic**
- **Struts2**
- 兼容XRay POC

## Web探测功能

- 自动获取网站标题
- Web指纹识别 (CMS/OA框架)
- Web漏洞扫描

## 输出结果

所有扫描结果会自动保存到文件，默认文件名为 `fscan.txt`

## 免责声明

本工具仅面向合法授权的企业安全建设行为。使用前请确保：
1. 已取得目标系统的授权
2. 符合当地法律法规
3. 仅用于授权的安全测试

请勿对非授权目标进行扫描！
