# 91 短视频 重写脚本

91Porn 短视频（PWA）完整视频解锁 + 付费/金币视频解锁 + VIP 解锁 + 去广告。
Surge / Loon / Stash / Quantumult X / Shadowrocket / Egern 通用。

> 全部功能默认开启、无开关，导入后只需（可选）选日志级别。无需任何后端 / 养号 / 登录额外账号。

---

## 订阅地址

代理工具里填对应格式的地址即可：

```
Surge / Egern     https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.sgmodule
Quantumult X      https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.conf
Loon              https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.lpx
Shadowrocket      https://raw.githubusercontent.com/Yu9191/91sp/refs/heads/main/modules/91porn.shadowrocket.sgmodule
```

> Stash：用 [Script-Hub](https://github.com/Script-Hub-Org/Script-Hub) 把 `91porn.sgmodule` 转换后订阅。

---

<details>
<summary><b>功能</b></summary>

- **完整视频解锁**：普通账号只给 10 秒试看，脚本自动补全为完整版长视频。
- **付费 / 金币视频解锁**：去金币墙、标记已购。
- **VIP 解锁**：会员特权 / 等级 / 观看次数放开（次数用尽也不再被拦）。
- **去广告**：开屏 / banner / 弹窗 / 悬浮 / 信息流广告卡 / 图标广告行（学生兼职、棋牌、博彩等）/ 推广公告弹窗 / 原创页活动大赛横幅与倒计时。

</details>

<details>
<summary><b>主机名（MITM）</b></summary>

App 业务接口的 API 主机会随机轮换（随机子域名 + 轮换顶级域）。若某天脚本不生效，多半是主机变了：把抓包拿到的最新主机 / 顶级域加进各模块文件末尾的 `hostname`（MITM 不区分端口）即可。

当前已内置线路：`*.yxrznglgy.cc`、`*.nkbvfuvu.cc`

</details>
