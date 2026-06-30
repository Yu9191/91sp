# 91 短视频 重写脚本

91Porn 短视频（PWA）VIP 解锁 + 付费视频解锁 + 去广告。
Surge / Loon / Stash / Quantumult X / Shadowrocket / Egern 通用。

---

## 订阅地址

**Surge / Egern**

```
https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.sgmodule
```

**Quantumult X**

```
https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.conf
```

**Loon**

```
https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.lpx
```

> Stash / Shadowrocket 等：用 [Script-Hub](https://github.com/Script-Hub-Org/Script-Hub) 把 `.sgmodule` 转换后订阅，或直接用 `modules/91porn.shadowrocket.sgmodule`。

---

## 功能

- **VIP 解锁**：会员特权 / 等级 / 观看次数
- **付费视频解锁**：去金币墙、标记已购（仅去除前端付费墙；完整版视频由服务端按真实账号鉴权下发，无法通过改写响应获取）
- **去广告**：开屏 / 弹窗 / 悬浮 / 横幅 / 信息流广告卡 / 图标广告行（学生兼职、棋牌、博彩等）/ 推广公告弹窗 / 原创页活动大赛横幅与倒计时
- 默认全部开启，只需选日志级别

## 原理

业务接口统一打到 `/pwa.php`，请求/响应体的 `data` 字段为 AES 加密并带签名校验。脚本只做**响应改写**：解密 → 改写 → 重新加密 → 重算签名回填，不改请求，避免触发风控。

## 主机（需自行更新）

API 主机随机轮换（随机子域名 + 轮换顶级域）。若脚本不生效，多半是主机变了——请把抓包拿到的最新主机/顶级域加进各模块的 `hostname`（MITM 不区分端口）。

当前已添加域名：

```
*.yxrznglgy.cc
```
