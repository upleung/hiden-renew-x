# 🚀 hiden-renew-x

**Hiden☁ 自动化续期进阶方案 | 支持微信 / TG 双通道推送**

本项目专为应对 **Hiden☁** 严苛的人机验证与多账号风控机制而设计。采用 **“算网分离 + 隐私脱敏 + 自动同步”** 架构：利用 GitHub Actions 的云端算力执行反检测浏览器自动化，同时通过独享代理节点将出口流量伪装为原生静态 IP，并配备全局日志脱敏网关，全方位保障账号与节点安全。

---

## ✨ 核心特性 (Features)

- **🛡️ 反检测破盾内核**：搭载 `Patchright` 反指纹检测浏览器内核与底层 DOM 穿透点击机制，稳定通过 Cloudflare Turnstile 验证与整页安全拦截。
- **🔐 Discord OAuth 授权登录**：原生支持最新的 `DISCORD_TOKEN` API 授权回调登录，同時兼容传统凭证与通知账号展示。
- **🌐 算网分离 (原生 IP 伪装)**：内置 `sing-box` 代理引擎，支持将请求通过个人独享节点（VLESS / VMess / Trojan / Hysteria2 / SOCKS5）转发，规避公共机房 IP 风控。
- **🔒 全局日志隐私脱敏网关**：接管底层日志输出，自动将出口 IP、Server ID、账单 UUID、`client_id`、`state`、`code` 及微信 OpenID 尾号全部掩码为 `***`，公开仓库亦无惧隐私泄露。
- **📱 微信 / TG 双通道直连推送**：支持 Telegram 机器人通知与**微信官方测试号（支持双微信号同时接收）**原生直连推送，告别第三方中转平台的数据泄露风险。
- **🔄 上游自动同步与热补丁**：内置每周定时同步工作流，自动拉取上游最新破盾逻辑并无损注入微信双推与日志脱敏补丁，同时支持 `ALL_SECRETS` 动态变量全量透传。
- **⏱️ 智能保活与记录清理**：自动更新时间戳防止 Actions 长期无活动休眠，并在每次运行后自动清理旧的工作流历史记录，保持面板整洁。

---

## 🛠️ 环境变量配置 (Secrets)

在仓库的 `Settings` → `Secrets and variables` → `Actions` → `New repository secret` 中添加以下配置：

### 🔑 1. 核心登录凭证
| Secret 名称 | 是否必填 | 说明 | 获取方式 / 备注 |
| :--- | :---: | :--- | :--- |
| `DISCORD_TOKEN` | ✅ 必填 | Discord 账号授权 Token | 站点已强制要求使用 Discord 登录，用于自动完成 OAuth 授权 |
| `EMAIL` | ⭕ 建议 | Hiden☁ 关联邮箱 | 仅用于在 TG / 微信推送通知中脱敏展示账号标识（如 `ab****yz@gmail.com`） |
| `PASSWORD` | ❌ 可选 | 登录密码 | 备用字段（可留空） |
| `COOKIE_VALUE` | ❌ 可选 | Remember_web Cookie | 备用字段（可留空） |

<details>
<summary>👉 <b>如何获取 <code>DISCORD_TOKEN</code>？（点击展开）</b></summary>

1. 使用浏览器无痕模式或常用环境登录网页版 [Discord](https://discord.com/app)。
2. 按 `F12` 打开开发者工具，切换到 **Network (网络)** 面板。
3. 在筛选框中输入 `/api`，然后刷新页面或随便点击一个频道。
4. 点开任意一条请求，在右侧 **Request Headers (请求标头)** 中找到 `Authorization` 字段，其对应的值即为你的 `DISCORD_TOKEN`。
</details>

### 🌐 2. 独享代理配置 (强烈建议)
| Secret 名称 | 是否必填 | 说明 | 支持协议格式 |
| :--- | :---: | :--- | :--- |
| `NODE_LINK` | ⭕ 强烈建议 | 个人独享代理节点分享链接 | 支持 `vless://`、`vmess://`、`trojan://`、`hysteria2://`、`tuic://`、`anytls://`、`socks5://`（需与平时登录 Hiden☁ 的 IP 保持一致） |

### 🔔 3. 消息推送配置 (微信 / TG 双通道)
| Secret 名称 | 是否必填 | 说明 | 获取方式 |
| :--- | :---: | :--- | :--- |
| `TG_BOT_TOKEN` | ❌ 可选 | Telegram Bot Token | 找 `@BotFather` 创建机器人获取 |
| `TG_CHAT_ID` | ❌ 可选 | Telegram Chat ID | 找 `@userinfobot` 获取个人或群组 ID |
| `WECHAT_APPID` | ❌ 可选 | 微信测试号 `appID` | 登录 [微信公众平台接口测试帐号](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index) 获取 |
| `WECHAT_APPSECRET` | ❌ 可选 | 微信测试号 `appsecret` | 同上页面顶部获取 |
| `WECHAT_OPENID` | ❌ 可选 | 第 1 个接收微信号的 `OpenID` | 微信扫描测试号二维码关注后，在右侧「用户列表」中获取 |
| `WECHAT_OPENID2` | ❌ 可选 | 第 2 个接收微信号的 `OpenID` | 支持同时推送给第 2 个微信号（单人使用可不填） |
| `WECHAT_TEMPLATE_ID` | ❌ 可选 | 微信消息模板 ID | 新增测试模板后生成的长字符模板 ID（配置格式见下方） |

<details>
<summary>👉 <b>附：微信测试号消息模板配置格式（点击展开）</b></summary>

在微信测试号后台的「模板消息接口」中点击 **新增测试模板**：
- **模板标题**：`Hiden⛅续期`
- **模板内容**（请严格按以下格式填写）：
```text
通知类型：{{title.DATA}} 详细信息：{{content.DATA}}

```
保存后复制生成的 **模板ID**，填入仓库 Secret 的 `WECHAT_TEMPLATE_ID` 中即可。

---


## 🚀 使用与工作流说明 (Workflows)

1. **Fork 本仓库**（或导入代码），并在仓库 `Secrets` 中配置上述环境变量。
2. 进入仓库顶部的 **Actions** 页面，启用 Workflows。
3. 本仓库包含两条核心自动化流水线：
* **`Auto Renew ...`（主力续期任务）**：默认在 **UTC 时间每周六 22:35 与周日 15:36**（对应北京时间周日 06:35 与 23:36）自动运行，支持随时点击 `Run workflow` 手动触发测试。
* **`Sync Upstream ...`（上游自动同步任务）**：默认在 **UTC 时间每周六 12:00**（北京时间周六 20:00）自动检测上游更新，无损注入微信双推与日志脱敏网关补丁，通过 Python 语法自检后自动提交生效。



---

## ⚠️ 免责声明 (Disclaimer)

本项目仅用于个人编程学习、自动化测试与技术交流。请勿用于滥用免费资源或违反相关服务商条款的行为。使用本脚本所产生的一切风险与后果由使用者自行承担，开发者不承担任何直接或间接责任。

