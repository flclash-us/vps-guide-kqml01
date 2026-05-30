# VLESS + Reality 协议详解

Reality 是目前抗封锁能力最强的协议，本文详细介绍原理与配置方法。

## Reality 原理

Reality 的核心思想：**TLS 伪装 + 真实目标**

```
Client > [TLS 伪装] > Server > [解密] > 真实目标
                   ^
           看起来像访问了正常网站
```

优势：
- 无需域名备案
- 伪装真实 TLS 流量
- 抗 DPI 检测
- SNI 域名可填任意网站

## 服务端配置（Xray）

```json
{
  "inbounds": [{
    "port": 443,
    "protocol": "vless",
    "settings": {
      "clients": [{"id": "your-uuid", "flow": "xtls-rprx-vision"}],
      "decryption": "none"
    },
    "streamSettings": {
      "network": "tcp",
      "security": "reality",
      "realitySettings": {
        "show": false,
        "dest": "www.microsoft.com:443",
        "serverNames": ["www.microsoft.com"],
        "privateKey": "your-private-key",
        "shortIds": [""]
      }
    }
  }],
  "outbounds": [{"protocol": "freedom"}]
}
```

## 生成密钥

```bash
xray x25519
# 输出 Private key 和 Public key
```

## 客户端配置（Clash Meta）

```yaml
proxies:
  - name: "VLESS-REALITY"
    type: vless
    server: your-server.com
    port: 443
    uuid: your-uuid
    network: tcp
    tls: true
    flow: xtls-rprx-vision
    client-fingerprint: chrome
    reality-opts:
      public-key: generated-public-key
      short-id: abc123
```

## 抗封锁原理

- 无域名特征: SNI 填正常网站域名
- TLS 指纹: 模拟 Chrome 浏览器 TLS 指纹
- 目标隐藏: 流量看起来像访问微软网站

---

推荐工具：

- [Clash for Windows](https://clashforwindows.site/)
- [ClashMI](https://clashmi.site/)
- [FlClash](https://flclash.us/)
