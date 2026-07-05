# 通用机场签到

> 适用于任何 **SSPANEL** 架构的机场网站自动签到。

## 功能

- 每天定时签到，获取额外流量奖励
- **双模式登录**：Cookie 模式（推荐） 或 邮箱/密码模式
- **可选 Server酱 推送**：签到结果IM通知
- 支持多账号批量签到
- 失败自动重试（最多 3 次，带指数退避）

## 为什么有两种模式？

近年来部分机场（如 ikuuu、v2board 系）陆续在登录页加上了**极验 Geetest / reCAPTCHA** 等交互式验证码，纯密码登录会被拒绝。

| 模式 | 说明 | 推荐场景 |
|------|------|----------|
| **Cookie** 模式 | 浏览器登录后抓取 Cookie 存到 Secret，脚本直接带 Cookie 签到 | 机场开启了验证码，或想一劳永逸 |
| **邮箱/密码** 模式 | 沿用传统 POST 登录 | 机场未开启验证码 |

> 💡 Cookie 有效期通常 7-30 天，过期后按下面"如何抓 Cookie"再操作一次即可。

## 部署步骤

### 1. 右上角 Fork 此仓库

### 2. 配置 Secrets

进入 `Settings` → `Secrets and variables` → `Actions`，点击 **New repository secret**：

| Secret 名 | 是否必须 | 说明 |
|-----------|----------|------|
| `URL` | ✅ | 机场地址，例如 `https://ikuuu.win`（不要带末尾 `/`） |
| `COOKIE` | ⚠️ 二选一 | 浏览器登录后复制的 Cookie 字符串（优先使用，见下方说明） |
| `CONFIG` | ⚠️ 二选一 | 邮箱/密码交替排列，一行一个（旧模式） |
| `SCKEY` | ❌ | Server酱 SendKey（不填则不发推送） |

> `COOKIE` 和 `CONFIG` 至少提供一个。若同时提供两者，**优先走 Cookie 模式**。

### 3. 启用 Actions

去 `Actions` 标签手动 **Run workflow** 测试，通过后项目会按 cron 每天自动运行（默认 06:00 UTC+8）。

---

## 如何抓 Cookie（约 30 秒）

机场开启验证码时，这是签到的必要步骤：

1. Chrome / Firefox 打开机场网站**并登录**（拖动 / 点击完成验证码）
2. 按 **F12** 打开开发者工具 → **Network**（网络）标签
3. 按 **F5** 刷新页面，点击列表中**第一个请求**
4. 右侧 **Headers** 区域找到 `Request Headers` 里的 **`cookie:<_REDACT> 行
5. 右键该行 → **Copy value**
6. 粘贴到 GitHub Secret `COOKIE` 中保存

### 备选方式（Application 标签）

F12 → **Application**（应用程序） → 左侧 **Cookies** → 点击你的机场域名 → 把所有 `name=value` 对用 `; ` 连接起来。

### 多账号

如果你有多个账号，把多个 Cookie 串用 `|||` 分隔后存入同一个 `COOKIE` Secret：

```
PHPSESSID=abc; uid=1; key=xxx|||PHPSESSID=def; uid=2; key=yyy
```

---

## 结果通知

签到成功 / 失败都会通过 Server酱推送到IM（仅当填写了 `SCKEY` 时生效）。

Server酱注册：<https://sct.ftqq.com>

## License

MIT
