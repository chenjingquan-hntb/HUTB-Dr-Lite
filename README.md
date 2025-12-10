# HUTB Dr校园网自动登录脚本 lite  (openwrt / python)
###### 这是 湖南工商大学 (HUTB) 校园网 Dr.COM 网页认证 的精简优化版本，专为 小闪存路由器、低资源环境设计。

本脚本基于 [原版 HUTB 自动登录脚本](https://github.com/xmbhjQAQ/HUTB-DrCom-Login "原版 HUTB 自动登录脚本") 改进而来，
完全移除 对 requests 和 pycryptodome 这类内存占用较大的 Python 库依赖，
改为调用系统自带的 curl 与 openssl 命令实现 HTTP 请求与 AES 加密，
在兼容性和资源占用方面表现更好，非常适合存储空间和内存资源有限的设备。

## ✨ 改进亮点

零额外 Python 大库依赖：
无需安装 requests、pycryptodome，节省存储与内存。

系统命令替代实现：
HTTP 请求使用 curl（静默模式，支持 Cookie 持久化）。
AES-128-ECB 加密采用 openssl enc 命令，完全兼容服务端校验。

更好的 OpenWrt 适配：
优先通过内核 ioctl 获取 WAN 接口 IP，兼容没有 ifconfig 的极简环境。
提供 ifconfig 解析作为后备方案。

守护进程模式：每 60 秒检测一次网络，掉线自动重连。
握手流程完整实现：包括 loadConfig → queryPageSet → login 三步，确保通过服务器验证。

####🛠环境要求
硬件：运行 OpenWrt 的路由器（尤其是存储极小的机型）。
软件：
Python 3.x（系统自带或通过 opkg 安装）
系统需安装 curl 和 openssl 可执行文件
不需要安装额外 Python 库，避免了大体积依赖。

## 🚀 安装与配置

### 1. 安装必要工具
   BASH
   opkg update
   opkg install python3 curl openssl
## 2. 下载脚本
   将 lite.py 上传到路由器，例如：

    scp lite.py root@你的路由器IP:/root/ 

### 3. 修改配置
使用vim编辑脚本顶部的配置区域：

```python
	USERNAME = "你的学号" # 校园网账号
    PASSWORD = "你的密码" # 校园网密码
    INTERFACE_NAME = "eth0.2" # 外网接口名，例如 "wan", "eth0.2"
	
	如果不确定接口名，
	可在路由器 SSH 中运行 IFCONFIG 查找分配了 10.X.X.X 内网 IP 的接口。
```

4. 运行测试
```bash
   python3 /root/lite.py
   输出 >>> 登录成功！ <<< 或 状态: 已在线 即表示登录正常。
```

## ⚙️ 开机自启
将脚本放在如 /root/lite.py 的稳定位置。
使用启动项管理
这样路由器开机 30 秒后会自动登录并保持守护运行。

## 📝 常见问题 (FAQ)
> Q: 提示 "curl 或 openssl 缺失"?
A: 请运行 opkg install curl openssl 安装。

> Q: 如何确定 INTERFACE_NAME?
A: 用 ifconfig 查看，找出分配了校园网 IP 的接口名，例如 wan、eth0.2 等。

> Q: 脚本提示一直掉线重连?
A: 检查路由器时间是否准确，并确保 NTP 同步。

## ⚠️ 免责声明
本脚本仅供学习与交流，使用造成的任何后果由用户自行承担，请勿用于违反当地法规的用途。
