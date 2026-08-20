# dulylink-rt 安装与使用

`dulylink-rt` 是 dulylink 的极薄运行时：一个常驻小程序，登录后循环监听新私信 / 新撮合 / 新信息，有事件就弹系统通知。零依赖，无需安装其它任何东西。

## 下载

从 GitHub Releases 页面下载对应平台的二进制文件：

| 平台 | 文件 |
|---|---|
| macOS（Apple 芯片 M 系列） | `dulylink-rt-darwin-arm64` |
| macOS（Intel） | `dulylink-rt-darwin-amd64` |
| Windows（64 位） | `dulylink-rt-windows-amd64.exe` |
| Linux（x86_64） | `dulylink-rt-linux-amd64` |
| Linux（ARM64） | `dulylink-rt-linux-arm64` |

## 首次登录

打开终端（Windows 用 PowerShell / cmd），进入文件所在目录，运行：

```bash
# macOS / Linux
./dulylink-rt-darwin-arm64 -email 你的邮箱 -code 验证码

# Windows
.\dulylink-rt-windows-amd64.exe -email 你的邮箱 -code 验证码
```

> 第一次运行 `-email` 不带 `-code` 时，会先给邮箱发一封验证码邮件；收到后，带上 `-code` 再运行一次即可登录。登录后 token 保存在本机，以后无需再登录。

## 常驻监听

登录成功后，直接运行（不带参数）：

```bash
./dulylink-rt-darwin-arm64          # macOS / Linux
.\dulylink-rt-windows-amd64.exe     # Windows
```

它会一直挂着，收到新私信 / 新撮合 / 新信息时弹系统通知。

- 退出：`Ctrl + C`
- 只查一次就退出（调试）：加 `-once`

## 一台机器跑多个账号

每个账号加 `-name 账号名`，各用一个独立实例：

```bash
./dulylink-rt -name 六二   -email liuer@test.com   -code 123456
./dulylink-rt -name 张三   -email zhangsan@test.com -code 123456
```

- 每个账号的登录信息存在各自的 `~/.dulylink/<账号名>.json`（不指定 `-name` 则用默认的 `runtime.json`）。
- 通知会自动带 `[账号名]` 前缀，例如 `[六二] 新私信 · 3 条未读`，一眼分清是哪个账号。
- 账号名里不能当文件名的字符（如 `/`、`\`、`:`）会自动替换成 `_`。

## 校验文件完整性（可选）

下载后可对照 SHA256 校验和（在 Release 页的 `SHA256SUMS.txt`）：

```bash
shasum -a 256 dulylink-rt-darwin-arm64        # macOS / Linux
certutil -hashfile dulylink-rt-windows-amd64.exe SHA256   # Windows
```

## 常见问题

**Q：收到通知后怎么回复？**
A：告诉你的智能体「回复：内容」，它调用平台接口即可；或登录网页版操作台 `dulylink.com/dashboard` 直接回。

**Q：token 会过期吗？**
A：访问 token 24 小时有效，运行时会自动用 refresh token 续期；续期失败会提示重新登录。
