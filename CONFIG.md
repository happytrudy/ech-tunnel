# JSON 配置文件支持

本项目现已支持 JSON 格式的配置文件。您可以通过 `-config` 参数指定配置文件路径。

## 使用方法

### 客户端模式示例

使用配置文件启动客户端：

```bash
./ech-tunnel -config config.example.json
```

或使用命令行参数（命令行参数优先级高于配置文件）：

```bash
./ech-tunnel -config config.example.json -ip 1.2.3.5
```

### 服务端模式示例

使用配置文件启动服务端：

```bash
./ech-tunnel -config server.config.example.json
```

## 配置文件格式

配置文件采用 JSON 格式，包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `listen` | string | 监听地址 (tcp://..., ws://..., wss://... 或 proxy://...) |
| `forward` | string | 转发地址 (wss://... 或 socks5://...) |
| `ip` | string | 指定解析的IP地址（客户端模式） |
| `cert` | string | TLS证书文件路径（服务端模式） |
| `key` | string | TLS密钥文件路径（服务端模式） |
| `token` | string | 身份验证令牌 |
| `cidr` | string | 允许的来源IP范围（CIDR格式，多个用逗号分隔） |
| `dns` | string | DoH服务器地址 |
| `ech` | string | ECH查询域名 |
| `fallback` | boolean | 是否禁用ECH并回落到普通TLS 1.3 |
| `connections` | integer | 每个IP建立的WebSocket连接数量 |

## 配置优先级

1. **命令行参数** 优先级最高
2. **配置文件参数** 优先级次之
3. **默认值** 优先级最低

如果配置文件中某个字段不存在，则使用默认值。如果命令行指定了参数，则覆盖配置文件中的相应值。

## 示例配置文件

### 客户端配置 (config.example.json)

```json
{
  "listen": "tcp://127.0.0.1:1080,127.0.0.1:1081",
  "forward": "wss://example.com:443",
  "ip": "1.2.3.4",
  "token": "your-auth-token",
  "cidr": "0.0.0.0/0,::/0",
  "dns": "dns.alidns.com/dns-query",
  "ech": "cloudflare-ech.com",
  "fallback": false,
  "connections": 3
}
```

### 服务端配置 (server.config.example.json)

```json
{
  "listen": "wss://0.0.0.0:443/tunnel",
  "forward": "socks5://proxy.example.com:1080",
  "cert": "/etc/certs/server.crt",
  "key": "/etc/certs/server.key",
  "token": "your-auth-token",
  "cidr": "0.0.0.0/0",
  "connections": 3
}
```

## 说明

- 配置文件必须是有效的 JSON 格式
- 如果字段值为空或缺失，将使用默认值或命令行参数值
- 布尔值和整数类型请按 JSON 规范格式编写（`true`/`false` 和纯数字）
