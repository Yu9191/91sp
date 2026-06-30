# 91 短视频 重写脚本

91Porn 短视频（PWA）VIP 解锁 + 付费/金币视频解锁 + 长视频完整版解锁 + 去广告。
Surge / Loon / Stash / Quantumult X / Shadowrocket / Egern 通用。

---

## 订阅地址

### 推荐：动态下发（主机名自动更新，无需手动改）

浏览器打开下面地址，按工具一键导入；代理刷新订阅时会自动拿到**最新 API 主机名**（顶级域整体更换也跟着变）：

```
https://worker91.pages.dev/module
```

或直接订阅对应格式（代理工具里填这个地址即可）：

| 工具 | 订阅地址 |
| --- | --- |
| Surge | `https://worker91.pages.dev/module/surge` |
| Loon | `https://worker91.pages.dev/module/loon` |
| Quantumult X | `https://worker91.pages.dev/module/qx` |
| Shadowrocket | `https://worker91.pages.dev/module/shadowrocket` |

> Stash / Egern 等：用 [Script-Hub](https://github.com/Script-Hub-Org/Script-Hub) 把 `https://worker91.pages.dev/module/surge?raw=1` 转换后订阅。

### 备用：静态文件（主机名需自行更新）

```
Surge / Egern     https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.sgmodule
Quantumult X      https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.conf
Loon              https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.lpx
Shadowrocket      https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.shadowrocket.sgmodule
```

---

<details>
<summary><b>功能</b></summary>

- **VIP 解锁**：会员特权 / 等级 / 观看次数 / 昵称
- **付费 / 金币视频解锁**：注入 VIP 账号会话，服务端按 VIP 鉴权下发完整 `play_url`（非仅前端去墙）
- **长视频（完整版）解锁**：`hasLongVideo` 视频普通账号只给 `yd-10play` 10 秒试看，VIP 会话才下发 `yd-long` 完整版
- **去广告**：开屏 / banner / 弹窗 / 悬浮 / 信息流广告卡 / 图标广告行（学生兼职、棋牌、博彩等）/ 推广公告弹窗 / 原创页活动大赛横幅与倒计时
- 默认全部开启，只需选日志级别

</details>

<details>
<summary><b>原理</b></summary>

业务接口统一打到 `/pwa.php`，请求/响应体 `data` 字段为 **AES-256-CFB** 加密并带签名校验（`sign = md5(sha256(排序后的参数 + secret))`）。

- 视频请求（`mod=index` 且 `code` ∈ `detail_v1` / `com_video_url` / `long`）：脚本从 Worker 取 VIP 账号的 `token` + `oauth_id` 注入请求体，重算签名后转发。
- 关键：`verify_token` 由 `detail_v1` 响应下发并与会话(token+oauth_id)绑定，`com_video_url` / `long` 会校验一致性——所以 `detail_v1` 与播放接口必须用**同一 VIP 会话**注入，否则报「用户校验失败」。
- 其余响应做去广告 / VIP 伪装改写：解密 → 改写 → 重新加密 → 重算签名回填。

</details>

<details>
<summary><b>VIP 账号从哪来（Worker 后端养号）</b></summary>

后端 Cloudflare Worker（`worker91`）自动养号：

1. 自动注册主号 A
2. 取 A 的邀请码，注册小号 B（**B 第一次打开 app 即带 A 的推广码**——计数在这一步）
3. 每成功推广 1 人 = 送 3 天 VIP（可叠加）
4. `/api/vip` 把最新 VIP `token` + `oauth_id` + 到期时间下发给脚本

- 每 3 天自动养号，完成后给管理员发 **Telegram 通知**（主号 / VIP / 邀请数 / 到期时间 / 本次新增小号）。
- VIP 到期时间：服务端不下发真实到期，按「邀请 1 人 = 3 天」本地估算缓存。

</details>

<details>
<summary><b>主机名（已自动发现，一般无需手动）</b></summary>

API 主机随机轮换（随机子域名 + 轮换顶级域）。Worker 会抓 PWA 入口 `app.<hash>.js` 解析出 `pwa.php` 域名写入 KV，**每小时**刷新一次；动态订阅地址（上面 `worker91.pages.dev/module/*`）实时取最新主机名。

- 用动态订阅 → 主机名自动跟随，无需手动改。
- 用静态文件 → 若不生效多半是主机变了，把抓包拿到的最新主机/顶级域加进各模块的 `hostname`（MITM 不区分端口）。当前线路：`*.yxrznglgy.cc`

</details>

<details>
<summary><b>持久化键</b></summary>

**Worker KV**（命名空间绑定名 `KV`）

| 键 | 说明 |
| --- | --- |
| `main_account` | 主号信息：account/password/token/oauthId/isVip/vipLevel/invitedNum/coins/vipExpireAt/updatedAt |
| `farm_records` | 养出的小号记录（account/password/createdAt），最多保留 200 条 |
| `api_hosts` | 自动发现的最新 API 域名数组 |
| `api_hosts_updated_at` | 域名最后更新时间戳 |
| `pwa_base` | PWA 入口地址（域名发现起点） |
| `api_host` / `api_origin` / `api_ua` | 手动 `/sethost` 兜底用的单域名 / Origin / UA |

**脚本侧（BoxJS / 持久化存储）**

| 键 | 说明 |
| --- | --- |
| `91porn_vip_token` | VIP token 缓存 `{token, oauthId, ts}`，TTL 1 小时，减少请求 Worker |
| `91porn_worker_url` / `91porn_worker_key` | 覆盖默认 Worker 地址 / 密钥 |
| `91porn_log_level` | 日志级别 |
| `91porn_remove_ads` / `91porn_unlock_vip` / `91porn_unlock_video` / `91porn_modify_profile` | 各功能开关（默认开） |
| `91porn_nickname` | 自定义昵称 |

</details>

---

频道：<https://t.me/Jsforbaby> ・ 作者：TG [@ios151](https://t.me/ios151)
